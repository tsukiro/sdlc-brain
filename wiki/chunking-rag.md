---
title: "Firecrawl — Best Chunking Strategies for RAG"
type: source-summary
tags: [chunking, rag, retrieval, embeddings, text-splitting, firecrawl]
sources: [2026-05-13_chunking-rag.md]
updated: 2026-05-13
---

# Firecrawl — Best Chunking Strategies for RAG

**Fuente:** https://www.firecrawl.dev/blog/best-chunking-strategies-rag

## Perspectiva de esta fuente

Blog técnico de Firecrawl (empresa de web scraping/crawling). Perspectiva práctica y orientada a producción. Compara 7 estrategias de chunking con métricas reales, benchmarks y recomendaciones concretas. Una de las fuentes más completas y accionables sobre el tema.

## La pregunta central

¿Cómo dividir documentos para maximizar la calidad de recuperación en sistemas RAG? La estrategia de chunking afecta directamente el recall: diferencia de hasta **9%** entre mejores y peores enfoques.

## Las 7 estrategias (resumen ejecutivo)

| Estrategia | Recall | Costo | Cuándo |
|-----------|--------|-------|--------|
| **Recursive Character** | 88-89% | Bajo | **Default para 80% de casos** |
| **Size-Based** | Variable | Muy bajo | Prototipos, MVPs |
| **Sentence-Based** | — | Bajo | Contenido conversacional |
| **Page-Level** | 64.8% avg (low variance) | Bajo | PDFs paginados |
| **Semantic** | 91-92% | Alto | Máxima accuracy con presupuesto |
| **LLM-Based** | ~91.9% | Muy alto | Alto valor, colección pequeña |
| **Late Chunking** | — | Medio | Documentos con referencias cruzadas |

## Hallazgos notables (enero 2026)

1. **Semantic chunking**: los costos computacionales no siempre justifican las ganancias de recall vs. Recursive Character Splitting
2. **Overlap**: análisis reciente sugiere que el solapamiento no provee beneficio medible en algunos casos (contradice práctica estándar)
3. **Page-Level**: ganó benchmarks NVIDIA 2024 para PDFs estructurados (menor varianza: 0.107)

> **Tensión con práctica estándar:** La recomendación de overlap (10-20%) es convención ampliamente aceptada, pero este artículo cita análisis de enero 2026 que la cuestiona. Requiere validación en contexto propio.

## Marco de decisión

**Por tipo de contenido:**
- Blogs/docs/artículos → Recursive 512t
- PDFs estructurados → Page-Level
- Chat/Q&A → Sentence-Based
- Código → Recursive con separadores code-aware
- Máxima accuracy → Semantic

**Por tipo de query:**
- Factoid (fechas, nombres) → 256-512 tokens
- Analytical (contexto, explicaciones) → 1024+ tokens
- Mixed → 400-512 tokens

## Métricas de evaluación RAG

- **Recall**: ¿Se recuperan los chunks relevantes?
- **Precision**: ¿Los chunks recuperados son útiles?
- **MRR**: ¿Qué posición tienen los correctos en el ranking?
- **NDCG**: Calidad del ranking completo

## Conexiones

- [[concepts/chunking]] — síntesis de todas las estrategias
- [[concepts/rag]] — pipeline completo de RAG
- [[langchain-text-splitters]] — implementaciones en LangChain
- [[entities/gemini]] — gemini-embedding-2-preview relevante para el paso de embedding
