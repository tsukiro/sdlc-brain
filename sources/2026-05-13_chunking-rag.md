# Firecrawl — Best Chunking Strategies for RAG

**URL:** https://www.firecrawl.dev/blog/best-chunking-strategies-rag  
**Fecha de captura:** 2026-05-13

---

## Qué es Chunking en RAG

Proceso de dividir documentos grandes en fragmentos más pequeños antes de procesar con modelos de embeddings. La estrategia afecta directamente la precisión de recuperación, con diferencias de hasta 9% en recall entre mejores y peores enfoques.

## Las 7 estrategias comparadas

### 1. Recursive Character Splitting
- Respeta límites naturales del lenguaje: párrafos → líneas → espacios → caracteres
- Config recomendada: 400-512 tokens, 50-100 tokens solapamiento, separadores: `["\n\n", "\n", ". ", " ", ""]`
- Recall: 88-89% con text-embedding-3-large
- **Default recomendado para ~80% de aplicaciones RAG**
- Ideal para: artículos, documentación técnica, papers

### 2. Size-Based Chunking (Character/Token)
- Divide al alcanzar límite de chars/tokens sin considerar semántica
- Más rápido (3 líneas de código), tamaños predecibles
- Desventaja: fragmenta oraciones, ignora estructura
- Cuándo: prototipos, MVPs, contenido uniforme, velocidad crítica

### 3. Sentence-Based Chunking
- NLP para detectar límites de oraciones, agrupa hasta tamaño objetivo
- Mantiene integridad de oraciones; tamaños variables
- Cuándo: chat logs, Q&A, contenido conversacional

### 4. Page-Level Chunking
- Trata cada página como chunk separado
- "Ganó benchmarks NVIDIA 2024 con 0.648 accuracy" (menor varianza: 0.107)
- Preserva contexto visual: tablas, figuras, layouts
- Cuándo: PDFs de reportes, documentos legales, papers con estructura visual
- Solo para documentos paginados

### 5. Semantic Chunking
- Embeddings de oraciones → divide donde similitud entre consecutivas cae
- Threshold methods: percentile (95th), standard deviation (3σ), IQR
- Recall: 91-92% (hasta 9% mejor que métodos simples)
- Costoso: embedding de cada oración
- Hallazgo enero 2026: "costos no siempre justificados por ganancias"
- Cuándo: contenido denso, máxima accuracy, presupuesto disponible

### 6. LLM-Based Chunking
- Envía documento a LLM para determinar límites óptimos de forma context-aware
- Genera metadatos en el mismo paso
- Más costoso y lento; prohibitivo en escala
- Cuándo: contenido de alto valor, colecciones pequeñas, experimental

### 7. Late Chunking
- Procesa documento completo por el transformer ANTES de dividir
- Preserva contexto full-document en embeddings de cada chunk
- Disponible en Jina AI embeddings API (`late_chunking: True`)
- Requiere modelos con context windows largos
- Cuándo: documentos con referencias cruzadas (contratos, papers con dependencias metodológicas)

## Comparativa de rendimiento

| Estrategia | Recall | Costo | Velocidad |
|-----------|--------|-------|-----------|
| Recursive (400-512t) | 88-89% | Bajo | Rápido |
| Page-Level | 64.8% avg | Bajo | Rápido |
| Semantic | 91-92% | Alto | Lento |
| LLM-Based | ~91.9% | Muy alto | Muy lento |
| Size-based | Variable | Muy bajo | Muy rápido |

## Solapamiento (Overlap)

- Recomendación: 10-20% del tamaño del chunk
- Chunk 500 tokens → 50-100 tokens solapados
- Hallazgo enero 2026: "overlap no proporciona beneficio medible" (en algunos tests)

## Métricas de evaluación

- **Recall**: ¿Recupera chunks relevantes?
- **Precision**: ¿Son útiles los chunks recuperados?
- **MRR** (Mean Reciprocal Rank): posición de resultados correctos
- **NDCG** (Normalized Discounted Cumulative Gain): calidad del ranking

## Decisión por tipo de contenido

| Contenido | Estrategia |
|-----------|-----------|
| Blogs, docs, artículos | Recursive 512t, 50t overlap |
| PDFs estructurados, reportes | Page-Level |
| Chat, Q&A conversacional | Sentence-Based |
| Código | Recursive con separadores code-aware |
| Máxima accuracy (con presupuesto) | Semantic |
| Alto valor, pequeña colección | LLM-Based |

## Decisión por tipo de query

- Factoid (nombres, fechas): 256-512 tokens
- Analytical (explicaciones, contexto): 1024+ tokens
- Mixed: 400-512 tokens

## Mejor práctica de pipeline

1. Extracción limpia antes de chunking (HTML crudo crea chunks ruidosos)
2. Probar 2-3 estrategias en 50-100 docs representativos
3. Medir con tus queries reales
4. Considerar routing híbrido por tipo de contenido en producción
