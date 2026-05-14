---
title: "LLM Wiki — Concepto"
type: concept
tags: [llm-wiki, rag, knowledge-base, arquitectura, context-window, karpathy]
sources: []
updated: 2026-05-14
---

# LLM Wiki

> Patrón de knowledge management donde un LLM compila y mantiene una base de conocimiento en markdown, diseñada para ser cargada completa en contexto — eliminando el retrieval step de RAG.

---

## La idea central

RAG asume que el conocimiento no cabe en contexto → hay que buscarlo en runtime. LLM Wiki invierte la premisa: si el conocimiento sintetizado **cabe** en el contexto del modelo moderno, es más eficiente compilarlo una vez que redescubrirlo con cada query.

```
RAG:     [query] → embed → ANN search → retrieve → [LLM + chunks] → respuesta
LLM Wiki:[query] →                                  [LLM + wiki]   → respuesta
```

El wiki es más denso que los chunks crudos porque está **sintetizado**: contradicciones resueltas, duplicados fusionados, cross-references explícitos.

---

## Árbol de decisión: ¿RAG o LLM Wiki?

```
¿Cuántos tokens tiene tu corpus sintetizado?
├── > 400k tokens → RAG (no cabe en contexto razonable)
└── ≤ 400k tokens
    ├── ¿Cambia el corpus con alta frecuencia (diaria/semanal)?
    │   ├── Sí → RAG (re-index automático es más práctico)
    │   └── No → ¿Necesitas síntesis profunda y cross-references?
    │             ├── No → RAG (chunk retrieval suficiente)
    │             └── Sí → LLM Wiki ← sweet spot
```

**Regla práctica:** 50k–400k tokens de conocimiento estable → LLM Wiki. Más o cambiante → RAG. Ambos pueden coexistir (LLM Wiki para el núcleo estable + RAG para documentos frescos).

---

## Propiedades clave

### Compile time vs. Query time
| Momento | Qué hace el LLM |
|---|---|
| **Compile time** (ingest) | Lee fuentes, sintetiza, crea wikilinks, resuelve tensiones |
| **Query time** | Carga el wiki completo en contexto → responde |

La separación es la clave del rendimiento: el trabajo pesado se hace una vez, no en cada query.

### Ventajas sobre RAG
- **Latencia:** sin retrieval step → 6× más rápido (MariaDB Labs: 1100ms → 180ms P95)
- **Costo:** wiki denso vs. chunks crudos → 5× más barato ($0.15 → $0.03 / 1000 queries)
- **Calidad:** síntesis preservada entre queries; el LLM no re-sintetiza en cada pregunta
- **Auditable:** el wiki es markdown legible; un humano puede revisar y corregir

### Limitaciones
- **Escala acotada:** el wiki debe caber en el context window del modelo
- **Mantenimiento activo:** requiere re-ingest cuando las fuentes cambian
- **Error de síntesis:** el LLM puede alucinear durante la compilación (mitigación: linting + revisión humana)

---

## Los tres componentes

### 1. Markdown como verdad
Formato plano, versionable, legible por humanos y máquinas. Git como historial.

### 2. Compilación (ingest)
El LLM procesa fuentes y produce:
- `source-summary` por documento
- Actualizaciones a páginas `concept` y `entity` existentes
- Wikilinks cruzados
- Resolución de contradicciones (documentadas como tensiones)
- Entrada en `index.md` y `log.md`

### 3. Linting
Auditoría periódica para mantener la coherencia:
- Contradicciones entre páginas
- Páginas huérfanas
- Claims obsoletos
- Gaps de cobertura
- Cross-references faltantes

---

## Errores comunes

| Error | Consecuencia | Mitigación |
|---|---|---|
| No hacer linting | Contradicciones silenciosas | Lint periódico (por sesión o por N ingests) |
| Wiki sin límite | Excede context window | Densidad mínima: eliminar páginas de bajo valor |
| Alucinación en síntesis | Claims no respaldados | Revisión humana en pages de alto impacto |
| No versionar | No hay rollback ni audit trail | Git para el directorio `wiki/` |
| Actualización infrecuente | Conocimiento obsoleto | Re-ingest en cambios significativos de fuentes |

---

## Tensión con RAG

Los dos patrones no son mutuamente excluyentes:

| Escenario | Arquitectura recomendada |
|---|---|
| Núcleo de conocimiento estable + docs frescos | LLM Wiki (core) + RAG (frescos) |
| Corpus masivo y cambiante | RAG puro |
| Conocimiento compacto y estable | LLM Wiki puro |
| Multimodal (imágenes, audio) | RAG (LLM Wiki es solo texto) |

---

## Conexiones

- [[llm-wiki-karpathy]] — fuente primaria: definición, caso MariaDB Labs, métricas
- [[concepts/rag]] — alternativa principal; complementaria según escala
- [[concepts/chunking]] — chunking strategies que LLM Wiki reemplaza en su scope
- [[concepts/agent-memory]] — LLM Wiki como forma de LTM sin vector DB
- [[concepts/spec-driven-development]] — SDD + LLM Wiki: la spec actúa como wiki para el agente implementador
- [[overview]] — este vault es la implementación de referencia del patrón
