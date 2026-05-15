---
title: "RAG — Retrieval-Augmented Generation"
type: concept
tags: [rag, retrieval, embeddings, chunking, llm, vector-db]
sources: [2026-05-13_chunking-rag.md, 2026-05-13_langchain-text-splitters.md, 2026-05-13_langgraph.md, 2026-05-13_crewai.md, promptingguide-techniques.md]
updated: 2026-05-14
---

# RAG — Retrieval-Augmented Generation

## Qué es

RAG es el patrón arquitectural que combina un sistema de **recuperación de información** (retrieval) con un **modelo generativo** (LLM). En lugar de depender solo del conocimiento del LLM, RAG le provee contexto relevante extraído de una base de conocimiento propia.

**Problema que resuelve**: Los LLMs tienen conocimiento estático (hasta su fecha de corte), no conocen documentos privados, y alucina cuando no tiene información suficiente. RAG grunda las respuestas en fuentes verificables.

## Origen del paradigma

**Lewis et al. 2021** — "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks" (NeurIPS 2021): el paper que definió el paradigma.

Modelo conceptual fundamental:
- **Memoria paramétrica:** el conocimiento en los pesos del LLM (fija; solo actualizable via fine-tuning)
- **Memoria no-paramétrica:** un índice vectorial denso consultable en tiempo de inferencia

El paper original usó **DPR (Dense Passage Retrieval)** sobre un índice vectorial completo de Wikipedia. El generador seq2seq recibe los pasajes recuperados como contexto y produce respuestas grounded.

Esta separación paramétrico/no-paramétrico sigue siendo el modelo mental correcto para RAG: el LLM aporta razonamiento y síntesis; el índice vectorial aporta conocimiento actualizado, privado o especializado.

## El pipeline clásico de RAG

```
Documento
    │
    ▼
[1. Load] → Cargar documentos desde la fuente
    │
    ▼
[2. Split] → Dividir en chunks (ver [[chunking]])
    │
    ▼
[3. Embed] → Calcular vector embedding de cada chunk
    │
    ▼
[4. Store] → Almacenar en base de datos vectorial (Pinecone, Chroma, pgvector, etc.)
    │
    ▼ ← ← ← ← ← ← Query del usuario
[5. Retrieve] → Buscar chunks más similares al query (cosine similarity)
    │
    ▼
[6. Generate] → Proveer chunks recuperados al LLM como contexto → respuesta
```

## Limitación del RAG clásico

> "El LLM redescubre el conocimiento desde cero en cada pregunta. No hay acumulación."

Esta es la limitación que motiva el patrón LLM Wiki: construir un wiki persistente que sintetice las fuentes una vez y permita consultas sobre esa síntesis acumulada.

## El paso más crítico: Chunking

La calidad del retrieval está limitada por la calidad del chunking. Un chunk demasiado grande diluye la señal; uno demasiado pequeño pierde contexto. Ver [[chunking]] para la guía completa de estrategias.

**Regla general**: Empieza con `RecursiveCharacterTextSplitter` a 400-512 tokens.

## Técnicas de retrieval

### similarity_search() vs max_marginal_relevance_search() (MMR)

| Técnica | Criterio | Cuándo usar |
|---|---|---|
| `similarity_search()` | Máxima similitud al query | Corpus con poca redundancia; respuestas focalizadas |
| `max_marginal_relevance_search()` | Similitud + diversidad (penaliza redundancia) | Múltiples docs cubren el mismo tema; corpus Confluence/Wiki |

> MMR es preferible cuando varias páginas repiten la misma información con diferente wording — evita que el LLM reciba 5 chunks que dicen lo mismo. (WestTrain, 2025)

### Similarity thresholding

Si ningún chunk supera un umbral de similitud configurado → respuesta fallback ("No relevant information found") en lugar de enviar chunks irrelevantes al LLM. Es la forma más simple y efectiva de reducir alucinaciones en RAG.

```python
# Patrón de implementación
results = vectorstore.similarity_search_with_score(query, k=5)
if all(score < THRESHOLD for _, score in results):
    return "No relevant information found."
```

### On-premise LLM para privacidad

Para documentación interna confidencial, el stack completo puede correr on-premise:
- **LLM:** Mistral, LLaMA 3, Phi-3 vía [Ollama](https://ollama.com/)
- **Hardware mínimo:** NVIDIA RTX 3090 (24GB VRAM) para Mistral 7B
- **Trade-off:** privacidad total vs capacidad reducida respecto a GPT-4/Claude

## Variantes avanzadas de RAG

### Adaptive RAG
El sistema evalúa la calidad del retrieval y ajusta la estrategia dinámicamente:
- Si el retrieval es de baja calidad → reformula el query o busca en otras fuentes
- Si el documento no existe → puede hacer web search

Implementación común: [[concepts/langgraph]] con conditional edges para routing basado en calidad.

### Agentic RAG (CrewAI Knowledge)
CrewAI integra RAG nativamente en el sistema de Knowledge de los agentes, con reescritura inteligente de queries. Los agentes no solo recuperan — deciden cuándo recuperar y cómo formular el query. Ver [[crewai]].

### Multimodal RAG
Usar embeddings multimodales (como [[entities/gemini]] `gemini-embedding-2-preview`) para indexar y recuperar no solo texto sino imágenes, video y audio en el mismo espacio vectorial.

## Herramientas del stack RAG típico en 2026

| Capa | Opciones comunes |
|------|-----------------|
| Loading | LangChain loaders, Firecrawl, Unstructured |
| Splitting | `langchain-text-splitters`, chunking custom |
| Embedding | OpenAI text-embedding-3-large, Gemini embedding-2, Jina AI |
| Vector Store | Pinecone, Chroma, pgvector, Weaviate, Qdrant |
| Retrieval | LangChain retrievers, LlamaIndex |
| Generation | [[entities/gemini]] 3 Flash/Pro, Claude, GPT-4o |
| Orchestration | [[concepts/langgraph]], [[crewai]], LangChain chains |

## Métricas de evaluación

- **Recall@k**: ¿Están los chunks relevantes entre los k recuperados?
- **Precision@k**: ¿Cuántos de los k recuperados son relevantes?
- **Answer accuracy**: ¿La respuesta generada es correcta?
- **Faithfulness**: ¿La respuesta está grounded en los chunks recuperados?
- **Latency**: Tiempo total del pipeline

## Debugging del pipeline RAG

### Inspección de cosine similarity

Cuando una query falla en recuperar contenido relevante, calcular la similitud directamente entre el embedding de la pregunta y todos los vectores almacenados revela si:
- Los scores son globalmente bajos → problema de embedding o chunking
- El contenido correcto existe pero está en posiciones bajas → problema de ranking/retrieval strategy

```python
from sklearn.metrics.pairwise import cosine_similarity
# Embeddings de todos los docs vs embedding del query
scores = cosine_similarity([query_embedding], all_doc_embeddings)
```

## Generación de Datasets Sintéticos para RAG (Dai et al. 2022)

**Problema:** Los proyectos RAG sufren cuellos de botella por escasez de datos etiquetados para los sistemas de recuperación. LLMs permiten prototipar antes de iniciar procesos formales de etiquetado.

### Técnica

1. Preparar **15–20 ejemplos manuales** de pares (documento, query relevante)
2. Seleccionar aleatoriamente **2–8 por prompt** (la aleatorización aumenta diversidad del dataset)
3. El LLM genera queries sintéticas para cada nuevo documento del corpus

```
Instrucción específica de tarea de recuperación
+ k ejemplos (documento + query anotada)
+ Nuevo documento
→ LLM genera queries relevantes para el nuevo documento
```

### Resultados

| Métrica | Valor |
|---------|-------|
| Ejemplos manuales requeridos | **8** |
| Costo para 50,000 documentos | **~$55** con ChatGPT |
| Rendimiento | Near-SOTA con solo 8 ejemplos manuales + documentos no etiquetados |

**Principio:** La calidad de los 15–20 ejemplos manuales iniciales determina directamente la calidad del dataset sintético. Representatividad, buen formato y especificaciones de longitud son críticos.

**Variación por dominio:** Distintas tareas definen "relevancia" diferente (recuperación de argumentos puede buscar apoyo O contraargumento). El prompt y los ejemplos se adaptan por tarea.

---

## Conexiones

- [[chunking]] — el paso más crítico del pipeline (estrategias detalladas)
- [[langchain-text-splitters]] — implementaciones de splitting
- [[langchain-confluence-loader]] — carga de páginas Confluence como Documents
- [[westtrain-rag-confluence]] — caso de estudio completo: on-premise, MMR, thresholding
- [[concepts/langgraph]] — Adaptive RAG con LangGraph
- [[crewai]] — Agentic RAG integrado
- [[entities/gemini]] — embeddings multimodales y generación
- [[chunking-rag]] — fuente primaria: Firecrawl blog
- [[promptingguide-techniques]] — Lewis et al. 2021 como origen del paradigma (sección RAG como técnica de prompting)
- [[promptingguide-applications-data]] — Dai et al. 2022: datasets sintéticos para RAG con 8 ejemplos
