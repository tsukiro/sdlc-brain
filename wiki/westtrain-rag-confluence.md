---
title: "RAG sobre Confluence — Caso de estudio WestTrain / Joshua Lee"
type: source-summary
tags: [rag, confluence, ollama, mistral, chromadb, embeddings, on-premise, python, langchain, hallucination]
sources: []
updated: 2026-05-14
---

# RAG sobre Confluence — Caso de estudio WestTrain / Joshua Lee

**Fuente:** https://westtrain.medium.com/building-an-internal-chatgpt-system-with-confluence-documents-and-rag-35ae1c2fe95d  
**Autor:** Joshua Lee  
**Publicado:** 2025-04-01  
**Contexto:** Sistema TMS interno (monitoreo de dispositivos, registros de alquiler). Documentación en Confluence con búsqueda limitada → chatbot RAG con LLM local.

---

## Tech stack

| Componente | Elección | Motivo |
|---|---|---|
| **OS / hardware** | Ubuntu 22.04 + NVIDIA RTX 3090 ×2 | On-premise, evita enviar docs internos a APIs cloud |
| **LLM** | Mistral vía [Ollama](https://ollama.com/) | Local, gratuito, privacidad |
| **Embedding model** | `intfloat/multilingual-e5-base` | Soporte multilingüe (contenido en coreano + inglés) |
| **Vector DB** | ChromaDB | Simple, local, sin infraestructura adicional |
| **Data source** | Confluence Cloud REST API | Fuente de verdad de documentación |
| **Librerías** | LangChain, Sentence-Transformers, BeautifulSoup | Ecosystem estándar |

---

## Implementación paso a paso

### 1. Fetch de Confluence

- Traversal **recursivo** desde una página raíz: se expanden sub-páginas dinámicamente
- El contenido está en `body.storage.value` — formato **HTML**
- Se parsea con **BeautifulSoup** para extraer texto plano
- Cada página se almacena como tupla `(title, text)` — el `title` se usa después como metadata de citación

```python
# La estructura clave de la API Confluence:
page["body"]["storage"]["value"]  # HTML del cuerpo de la página
```

### 2. Chunking y embedding

```python
splitter = RecursiveCharacterTextSplitter(
    chunk_size=400,
    chunk_overlap=100   # 25% overlap — más alto que el estándar 10-20%
)
```

- Modelo E5 multilingual → vector por chunk
- Almacenado en ChromaDB con `title` como metadata
- El `title` se incluye en el prompt final para citación

### 3. Query flow

```
Usuario envía pregunta
     ↓
Embed question → vector
     ↓
ChromaDB retrieve (MMR) → chunks relevantes y diversos
     ↓
Mistral + prompt estructurado → respuesta con citación de página
```

**Prompt diseñado para:**
1. No adivinar — referirse únicamente a los documentos recuperados
2. Incluir el título de la página fuente en la respuesta
3. Responder "No se encontró información relevante" si el retrieval falla

---

## Problemas y soluciones

### Problema 1: Navegación de la API de Confluence
La jerarquía de páginas requiere traversal recursivo — `children` de cada página deben expandirse. No es una descarga flat.

### Problema 2: Alucinaciones
**Causa:** El LLM genera respuestas fluidas aunque no tenga base documental.  
**Solución en capas:**
1. **Prompt explícito** — "no adivines, usa solo los documentos"
2. **Similarity thresholding** — si ningún chunk supera un umbral de similitud → respuesta fallback "No relevant information found"
3. **Metadata de citación** — el título de página en la respuesta permite al usuario verificar

### Problema 3: Baja calidad de retrieval
**Causa:** Word mismatch entre el query y el texto de los chunks (especialmente en contenido multilingüe).  
**Soluciones:**
- Cambiar de `similarity_search()` a **`max_marginal_relevance_search()`** → más diversidad, menos redundancia
- Ajustar `chunk_size` y `chunk_overlap`
- Considerar embeddings más robustos: `e5-large-v2`, `bge-ko`

### Debugging: inspección de cosine similarity

Para diagnosticar por qué una query falla:

```python
from sklearn.metrics.pairwise import cosine_similarity
# Calcular similitud entre el embedding de la pregunta y todos los vectores almacenados
# Visualiza si el score es muy bajo o si el contenido correcto existe pero está en posiciones bajas
```

Permite distinguir entre:
- Scores demasiado bajos globalmente (problema de embedding / chunking)
- Contenido correcto enterrado en el ranking (problema de retrieval strategy)

---

## Resultados

- Respuestas precisas basadas exclusivamente en documentación interna
- Citación de fuente (título de página) en cada respuesta
- Fallback "no sé" cuando no hay información relevante
- Feedback interno positivo: búsqueda de docs significativamente más rápida

---

## Próximos pasos planeados

- Embedding coreano-optimizado: `bge-ko`, `e5-large-v2`
- UI web con **Streamlit**
- Integración con **Slack** o chatbot interno
- **Auto-refresh**: re-embedding automático al detectar cambios en Confluence

---

## Hallazgos clave

1. **On-premise LLM (Mistral/Ollama) es viable para casos de uso internos** — RTX 3090 ×2 es suficiente para Mistral; la privacidad justifica el trade-off de capacidad vs GPT-4
2. **MMR > similarity_search en contenido con posible redundancia** — especialmente relevante cuando múltiples páginas Confluence cubren el mismo tema con diferente wording
3. **Similarity thresholding es esencial** — sin él, el LLM alucinará con chunks irrelevantes en lugar de admitir ignorancia
4. **Cosine similarity como herramienta de debug** — más útil que inspeccionar respuestas del LLM para diagnóstico de retrieval
5. **chunk_size=400 con overlap=100** — 25% de overlap (más alto que el 10-20% estándar) puede ser apropiado para contenido técnico multilingüe donde el contexto local es crítico

---

## Tensión

> **chunk_size=400 (este artículo, multilingüe) vs 512 (Firecrawl, estándar):** La diferencia es pequeña pero el overlap de 25% (100/400) es notablemente más alto que el estándar 10-20%. Podría explicarse por la naturaleza multilingüe del corpus — contexto local más crítico en idiomas con morfología compleja.

---

## Conexiones

- [[langchain-confluence-loader]] — ConfluenceLoader como alternativa al approach manual con requests + BeautifulSoup
- [[concepts/rag]] — pipeline completo; este artículo añade: MMR, similarity thresholding, on-premise LLM
- [[concepts/chunking]] — chunk_size=400 + overlap=100 como dato de producción multilingüe
- [[langchain-text-splitters]] — `RecursiveCharacterTextSplitter` usado en este artículo
- [[concepts/llm-security]] — anti-hallucination via similarity thresholding como capa de seguridad de contenido
