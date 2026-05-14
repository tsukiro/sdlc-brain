---
title: "LangChain Text Splitters"
type: source-summary
tags: [langchain, text-splitters, chunking, rag, python]
sources: [2026-05-13_langchain-text-splitters.md]
updated: 2026-05-13
---

# LangChain Text Splitters

**Fuente:** https://python.langchain.com/docs/concepts/text_splitters/ (redirige) + docs.langchain.com  
**Paquete:** `langchain-text-splitters`

## Perspectiva de esta fuente

Documentación oficial de LangChain. Es la **implementación de referencia** de las estrategias de chunking: donde el Firecrawl blog describe las estrategias conceptualmente, LangChain provee el código concreto para usarlas.

## Splitters disponibles

| Clase | Estrategia | Módulo |
|-------|-----------|--------|
| `RecursiveCharacterTextSplitter` | Recursive Character | `langchain_text_splitters` |
| `CharacterTextSplitter` | Size-Based (chars) | `langchain_text_splitters` |
| `TokenTextSplitter` | Size-Based (tokens) | `langchain_text_splitters` |
| `SemanticChunker` | Semantic | `langchain_experimental` |

## Splitter recomendado por defecto

`RecursiveCharacterTextSplitter` — divide respetando jerarquía semántica natural del texto. Es el mapeo directo de la estrategia "Recursive Character Splitting" del Firecrawl blog.

```python
from langchain_text_splitters import RecursiveCharacterTextSplitter

splitter = RecursiveCharacterTextSplitter(
    chunk_size=512,
    chunk_overlap=50,
    separators=["\n\n", "\n", ". ", " ", ""]
)
docs = splitter.split_documents(documents)
```

## Parámetros comunes

- `chunk_size`: tamaño máximo del chunk (en chars o tokens según `length_function`)
- `chunk_overlap`: tokens/chars de solapamiento entre chunks consecutivos
- `length_function`: `len` (chars, default) o función de tokenización
- `separators`: jerarquía de separadores (solo para Recursive)

## Integración en pipeline RAG

```
Load → Split → Embed → Store → Retrieve → Generate
         ↑
   Text Splitters actúan aquí
```

Los splitters producen objetos `Document` con `page_content` y `metadata`, directamente consumibles por los modelos de embeddings de LangChain.

## Relación con las 7 estrategias de Firecrawl

| Estrategia Firecrawl | Clase LangChain |
|---------------------|-----------------|
| Recursive Character | `RecursiveCharacterTextSplitter` |
| Size-Based (chars) | `CharacterTextSplitter` |
| Size-Based (tokens) | `TokenTextSplitter` |
| Semantic | `SemanticChunker` (experimental) |
| Sentence-Based | NLTK/spaCy splitters (integraciones) |
| Page-Level | Procesamiento por metadata de página |
| Late Chunking | No disponible directamente (requiere Jina AI) |

## Conexiones

- [[concepts/chunking]] — estrategias conceptuales
- [[chunking-rag]] — fuente comparativa de estrategias
- [[concepts/rag]] — pipeline completo
