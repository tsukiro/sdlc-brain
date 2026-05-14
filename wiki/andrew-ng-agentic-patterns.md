---
title: "Andrew Ng — Agentic Design Patterns"
type: source-summary
tags: [agentes, design-patterns, reflection, tool-use, planning, multi-agent, andrew-ng]
sources: [2026-05-13_andrew-ng-agentic-patterns.md]
updated: 2026-05-13
---

# Andrew Ng — Agentic Design Patterns

**Fuente:** https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance/  
**Autor:** Andrew Ng, DeepLearning.AI

## Perspectiva de esta fuente

Pieza divulgativa de alto impacto de Andrew Ng. Ofrece un marco de 4 patrones de diseño para agentes, y el dato cuantitativo más citado del campo: GPT-3.5 con flujo agentic alcanza 95.1% en HumanEval, superando GPT-4 zero-shot (67%).

## El dato central

| Modelo + Configuración | HumanEval |
|------------------------|-----------|
| GPT-3.5 zero-shot | 48.1% |
| GPT-4 zero-shot | 67.0% |
| **GPT-3.5 con agente iterativo** | **95.1%** |

> **La arquitectura del workflow puede importar más que el modelo subyacente.**

Este resultado reencuadra completamente la evaluación de LLMs: comparar modelos en zero-shot puede ser engañoso si se pretende usarlos en un contexto agéntico.

## Los 4 patrones

### 1. Reflection
El LLM revisa su propio output buscando errores, mejoras y refinamientos. No requiere herramientas externas ni múltiples agentes — solo el modelo evaluando su propia salida antes de entregarla.

**Analogía de Ng**: escribir sin poder borrar vs. escribir, revisar y editar. El segundo proceso produce resultados sustancialmente mejores.

### 2. Tool Use
El LLM tiene acceso a herramientas externas: búsqueda web, ejecución de código, llamadas a APIs, bases de datos. Amplía el conocimiento más allá del corte de entrenamiento y las capacidades de razonamiento puro.

### 3. Planning
El LLM descompone una tarea compleja en un plan multietapa y lo ejecuta. Puede iterar sobre el plan basándose en resultados intermedios. Reflection y Tool Use son subcomponentes del Planning.

### 4. Multi-Agent Collaboration
Múltiples agentes especializados trabajan en paralelo o en secuencia: dividen tareas, debaten soluciones, se especializan en dominios. Cada agente puede tener sus propios patterns de Reflection, Tool Use y Planning.

## Relación con el marco de Lilian Weng

| Ng (4 patrones) | Weng (3 componentes) |
|-----------------|----------------------|
| Reflection | Parte de Planning (autorreflexión) |
| Tool Use | Tool Use ✓ |
| Planning | Planning ✓ |
| Multi-Agent | Extensión del framework (no cubierto por Weng) |

> **Tensión / complementariedad**: Weng no trata Multi-Agent como componente separado de la arquitectura base; Ng lo eleva al nivel de los otros tres patrones. Ambas visiones son correctas — la diferencia es de nivel de abstracción.

## Conexiones

- [[concepts/agentic-design-patterns]] — síntesis de los 4 patrones
- [[concepts/llm-agents]] — arquitectura base (Weng)
- [[lilian-weng-agents]] — fuente complementaria
- [[concepts/planning]] — el patrón Planning en detalle
- [[concepts/multi-agent-frameworks]] — LangGraph y CrewAI implementan multi-agent
