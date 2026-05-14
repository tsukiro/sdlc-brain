---
title: "RAG — Retrieval-Augmented Generation"
type: concept
tags: [rag, retrieval, embeddings, chunking, llm, vector-db]
sources: [2026-05-13_chunking-rag.md, 2026-05-13_langchain-text-splitters.md, 2026-05-13_langgraph.md, 2026-05-13_crewai.md]
updated: 2026-05-13
---

# RAG — Retrieval-Augmented Generation

## Qué es

RAG es el patrón arquitectural que combina un sistema de **recuperación de información** (retrieval) con un **modelo generativo** (LLM). En lugar de depender solo del conocimiento del LLM, RAG le provee contexto relevante extraído de una base de conocimiento propia.

**Problema que resuelve**: Los LLMs tienen conocimiento estático (hasta su fecha de corte), no conocen documentos privados, y alucina cuando no tiene información suficiente. RAG grunda las respuestas en fuentes verificables.

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

## Conexiones

- [[chunking]] — el paso más crítico del pipeline (estrategias detalladas)
- [[langchain-text-splitters]] — implementaciones de splitting
- [[concepts/langgraph]] — Adaptive RAG con LangGraph
- [[crewai]] — Agentic RAG integrado
- [[entities/gemini]] — embeddings multimodales y generación
- [[chunking-rag]] — fuente primaria: Firecrawl blog
