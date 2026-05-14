---
title: "LLM Wiki — Patrón Karpathy"
type: source-summary
tags: [llm-wiki, rag, knowledge-base, karpathy, mariadb, arquitectura, wiki]
sources: []
updated: 2026-05-14
---

# LLM Wiki — Patrón Karpathy

**Origen:** Andrej Karpathy (concepto) + MariaDB Labs (caso de estudio implementado)  
**Tipo:** Patrón de knowledge management para sistemas LLM  
**Alternativa a:** RAG clásico (retrieval at runtime)

---

## Definición

Un **LLM Wiki** es una base de conocimiento en markdown compilada y mantenida por un LLM, diseñada para ser cargada completa en el contexto en tiempo de consulta — sin búsqueda semántica en runtime.

> "The LLM reads sources once, compiles a cross-referenced wiki, and answers without runtime search."

La clave es la distinción entre **compile time** y **query time**:
- **Compile time:** el LLM lee las fuentes, sintetiza, crea páginas, resuelve contradicciones, crea wikilinks
- **Query time:** el wiki (ya compilado) se carga completo en el contexto → el LLM responde

---

## Diferencia fundamental con RAG

| Dimensión | RAG | LLM Wiki |
|---|---|---|
| **Runtime retrieval** | Sí — búsqueda semántica por query | No — wiki cargado completo |
| **Compilación** | No — chunks indexados sin síntesis | Sí — LLM sintetiza y conecta |
| **Cross-references** | Implícitas (similaridad vectorial) | Explícitas (wikilinks mantenidos por LLM) |
| **Latencia query** | Alta (embed query → retrieve → rerank) | Baja (solo inferencia LLM) |
| **Drift de fuentes** | Inmediato (re-index automático posible) | Requiere re-compilación explícita |
| **Escala máxima** | Ilimitada (vector DB) | Acotada por context window |

### Cuándo RAG es mejor
- Corpus > 1M tokens (no cabe en contexto)
- Fuentes cambian con alta frecuencia
- Se necesita atribución granular por chunk

### Cuándo LLM Wiki es mejor
- Corpus de 50k-400k tokens (cabe en contexto moderno)
- Conocimiento relativamente estable
- Se quiere síntesis profunda, no recuperación literal
- Latencia importa (no hay retrieval step)

---

## Comparativa extendida

| | LLM Wiki | RAG | NotebookLM |
|---|---|---|---|
| **Modelo de acceso** | Wiki completo en contexto | Retrieval semántico | Propietario (Google) |
| **Síntesis** | Compilada offline por LLM | Por LLM en runtime (sin síntesis previa) | Automática |
| **Cross-references** | Explícitas (wikilinks) | Implícitas | Automáticas |
| **Control** | Total (markdown legible) | Total (index + vector DB) | Ninguno |
| **Mantenimiento** | Manual (re-ingest) | Automático (re-index) | Automático |
| **Costo por query** | Solo inferencia | Embed + retrieve + inferencia | Opaco |

---

## Caso de estudio: MariaDB Labs

**Corpus:** 78 artículos / 265k palabras / ~1M tokens de documentación técnica

### Resultados comparados con RAG previo

| Métrica | RAG (baseline) | LLM Wiki |
|---|---|---|
| **P95 latencia** | 1,100 ms | 180 ms |
| **Costo / 1,000 queries** | $0.15 | $0.03 |
| **Reducción latencia** | — | **6.1×** |
| **Reducción costo** | — | **5×** |

La reducción de latencia se explica por la eliminación del retrieval step (embedding del query + búsqueda ANN + reranking). El costo baja porque el wiki compilado es más denso y relevante que un conjunto de chunks sin síntesis.

---

## Componentes del sistema

### 1. Markdown como fuente de verdad
- Todas las páginas del wiki son archivos `.md` versionables en git
- Human-readable: el equipo puede leer, auditar y corregir directamente
- Formato estructurado: frontmatter YAML + secciones consistentes

### 2. Compilación (ingest)
El LLM procesa cada nueva fuente y:
- Crea páginas de resumen (`source-summary`)
- Actualiza páginas de conceptos y entidades existentes
- Resuelve contradicciones entre fuentes (documenta la tensión)
- Crea wikilinks cruzados entre páginas relacionadas
- Actualiza el índice y el log

### 3. Linting
Proceso de auditoría periódica del wiki:
- Detecta contradicciones entre páginas
- Encuentra páginas huérfanas (sin inbound links)
- Identifica claims obsoletos por fuentes más recientes
- Señala gaps de cobertura (conceptos mencionados sin página propia)
- Verifica cross-references faltantes

---

## Errores comunes al implementar LLM Wiki

El artículo lista 5 errores frecuentes en implementaciones reales:

1. **No hacer linting** — el wiki acumula drift entre páginas sin detección automática; las contradicciones crecen silenciosamente
2. **Wiki sin límite de tamaño** — el wiki crece hasta exceder el context window; sin un criterio de densidad/relevancia, todo se acumula
3. **Alucinación en síntesis** — el LLM extiende las fuentes más allá de lo que dicen; la compilación introduce inferencias no respaldadas
4. **No versionar el wiki** — sin git o similar, es imposible auditar qué cambió entre compilaciones ni revertir errores
5. **Actualización demasiado infrecuente** — las fuentes evolucionan pero el wiki se congela; el sesgo de información antigua afecta todas las respuestas

---

## Workflow de implementación

```
1. RECOPILAR fuentes (URLs, PDFs, docs)
         ↓
2. INGEST (por fuente):
   - LLM lee la fuente
   - Crea/actualiza páginas wiki
   - Actualiza índice y log
         ↓
3. LINT (periódico):
   - Detectar contradicciones y gaps
   - Corregir páginas afectadas
         ↓
4. QUERY:
   - Cargar wiki completo en contexto
   - LLM responde sin retrieval adicional
         ↓
5. RE-INGEST cuando fuentes cambian
   (volver a paso 2)
```

---

## Meta-referencia: este vault ES un LLM Wiki

El vault SDLC donde reside esta página implementa exactamente el patrón descrito:
- Las fuentes se ingresan con `ingest`
- El LLM (Claude) compila páginas wiki estructuradas
- El linting se realiza periódicamente
- Las queries cargan páginas relevantes del wiki en contexto

La diferencia respecto al ejemplo de MariaDB: este vault usa Obsidian como interfaz de lectura, y el LLM es el escritor exclusivo del directorio `wiki/`.

---

## Conexiones

- [[concepts/llm-wiki]] — concepto destilado con árbol de decisión RAG vs LLM Wiki
- [[concepts/rag]] — pipeline RAG como alternativa principal
- [[concepts/chunking]] — chunking strategies relevantes para RAG (contrapartida)
- [[concepts/agent-memory]] — LTM como vector DB vs LLM Wiki como alternativa
- [[overview]] — este wiki es la implementación viva del patrón
