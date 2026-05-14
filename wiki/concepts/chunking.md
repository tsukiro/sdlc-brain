---
title: "Chunking — Estrategias para RAG"
type: concept
tags: [chunking, rag, embeddings, text-splitting, retrieval]
sources: [2026-05-13_chunking-rag.md, 2026-05-13_langchain-text-splitters.md]
updated: 2026-05-13
---

# Chunking — Estrategias para RAG

## Qué es el chunking

El proceso de dividir documentos grandes en fragmentos más pequeños (**chunks**) antes de calcular embeddings y almacenarlos en una base vectorial. Es el primer paso crítico en un pipeline [[rag]], y su calidad determina el techo de lo que el sistema puede recuperar.

> "Chunking de calidad importa más que la elección de base de datos vectorial para muchas aplicaciones." — Firecrawl

## Por qué importa

La estrategia de chunking afecta directamente el **recall**: diferencia de hasta **9%** entre las mejores y peores estrategias en el mismo corpus. Un chunk mal construido puede:
- Fragmentar una idea a mitad
- Mezclar temas no relacionados
- Perder el contexto necesario para responder la pregunta

## Las 7 estrategias

### 1. Recursive Character Splitting ⭐ (default recomendado)
**Cómo funciona:** Divide respetando jerarquía semántica: párrafos → líneas → oraciones → palabras → caracteres. Solo baja un nivel si el chunk sigue siendo demasiado grande.

**Config recomendada:** 400-512 tokens, 50-100 tokens de overlap, separadores: `["\n\n", "\n", ". ", " ", ""]`

**Recall:** 88-89% | **Costo:** Bajo | **Velocidad:** Rápido

**LangChain:** `RecursiveCharacterTextSplitter`

**Cuándo:** Default para ~80% de aplicaciones RAG. Artículos, documentación, papers.

---

### 2. Size-Based (Character / Token)
**Cómo funciona:** Divide al alcanzar un límite de chars o tokens, sin considerar límites semánticos.

**Recall:** Variable (bajo) | **Costo:** Muy bajo | **Velocidad:** Muy rápido

**LangChain:** `CharacterTextSplitter` / `TokenTextSplitter`

**Cuándo:** Prototipos, MVPs, contenido muy uniforme, cuando la velocidad es crítica.

**Riesgo:** Fragmenta oraciones y pensamientos completos.

---

### 3. Sentence-Based
**Cómo funciona:** NLP para detectar límites de oraciones; agrupa oraciones completas hasta alcanzar el tamaño objetivo.

**Cuándo:** Chat logs, Q&A, contenido conversacional. Tamaños variables.

---

### 4. Page-Level
**Cómo funciona:** Cada página del documento = un chunk.

**Recall:** 64.8% average (pero varianza más baja: 0.107 en benchmark NVIDIA 2024)

**Cuándo:** PDFs con estructura visual (tablas, figuras, layouts). Reportes financieros, papers, documentos legales. No aplicable a texto plano.

**Ventaja única:** Preserva la integridad visual/semántica de la página como unidad.

---

### 5. Semantic Chunking
**Cómo funciona:** Calcula embeddings de cada oración → divide donde la similitud entre oraciones consecutivas cae bajo un umbral.

**Recall:** 91-92% | **Costo:** Alto (embedding por oración) | **Velocidad:** Lento

**LangChain:** `SemanticChunker` (en `langchain_experimental`)

**Métodos de threshold:** Percentile (p95), Standard deviation (3σ), IQR

**Caveat (enero 2026):** El costo computacional puede no justificarse vs. Recursive Character en muchos casos.

**Cuándo:** Contenido denso con cambios sutiles de tema, presupuesto de cómputo disponible, cuando el 1-3% adicional de recall importa.

---

### 6. LLM-Based
**Cómo funciona:** Se envía el documento a un LLM con instrucciones para determinar límites óptimos de chunking.

**Recall:** ~91.9% | **Costo:** Muy alto | **Velocidad:** Muy lento

**Cuándo:** Contenido de altísimo valor (contratos críticos, documentos legales únicos), colecciones muy pequeñas, proyectos experimentales.

---

### 7. Late Chunking
**Cómo funciona:** Procesa el documento completo por el transformer (con atención bidireccional total) *antes* de dividir en chunks. El embedding de cada chunk lleva contexto del documento completo.

**Disponibilidad:** Jina AI embeddings API (`late_chunking=True`)

**Ventaja:** Los chunks tienen contexto global, no solo local.

**Cuándo:** Documentos con referencias cruzadas intensas (contratos, papers con dependencias metodológicas). Requiere modelos con context window largo.

---

## Árbol de decisión

```
¿Qué tipo de contenido?
├── PDFs con estructura visual → Page-Level
├── Conversacional / Q&A → Sentence-Based
├── Código → Recursive con separadores code-aware
└── Texto general (artículos, docs, papers)
    └── ¿Cuál es la prioridad?
        ├── Velocidad / bajo costo → Size-Based
        ├── Balance → Recursive Character (512t) ← DEFAULT
        ├── Máxima accuracy con presupuesto → Semantic
        └── Alto valor, colección pequeña → LLM-Based
```

## Solapamiento (Overlap)

Práctica estándar: 10-20% del chunk size (chunk de 512t → 50-100t overlap).

> **Tensión / caveat:** Análisis de enero 2026 (Firecrawl) sugiere que el overlap no provee beneficio medible en todos los casos. La práctica estándar sigue siendo recomendada como punto de partida, pero merece validación empírica en el contexto específico.

## Métricas de evaluación

| Métrica | Qué mide |
|---------|---------|
| **Recall** | ¿Se recuperan los chunks relevantes? |
| **Precision** | ¿Los chunks recuperados son útiles? |
| **MRR** | ¿Qué posición tienen los correctos en el ranking? |
| **NDCG** | Calidad completa del ranking |

## Mejores prácticas de pipeline

1. **Limpieza antes de chunking**: HTML crudo → chunks ruidosos. Convertir a markdown limpio primero.
2. **Probar con datos reales**: 50-100 documentos representativos antes de escalar
3. **Routing híbrido en producción**: PDFs → Page-level; web → Recursive; código → code-aware
4. **Monitoring continuo**: el recall puede degradarse si el corpus cambia de carácter

## Conexiones

- [[rag]] — pipeline completo donde chunking es el primer paso
- [[chunking-rag]] — fuente Firecrawl (comparativa detallada)
- [[langchain-text-splitters]] — implementaciones concretas en LangChain
- [[entities/gemini]] — gemini-embedding-2-preview para el paso de embedding multimodal
