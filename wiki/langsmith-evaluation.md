---
title: "LangSmith — Evaluating Agents"
type: source-summary
tags: [evaluación, langsmith, trazado, observabilidad, trajectory, agentes, producción]
sources: [2026-05-13_langsmith-evaluation.md]
updated: 2026-05-13
---

# LangSmith — Evaluating Agents

**Fuente:** https://docs.smith.langchain.com/evaluation/evaluating-agents

## Perspectiva de esta fuente

Documentación oficial de LangSmith (plataforma de observabilidad de LangChain). Es la guía práctica más completa sobre cómo implementar evaluación de agentes en producción: no solo qué evaluar, sino cómo instrumentar el código para hacerlo.

## El problema que resuelve

Evaluar un agente por su output final es insuficiente: el agente puede llegar a la respuesta correcta por el camino equivocado (y fallar en casos similares), o puede fallar en un paso intermedio específico que siempre se repite.

## Estrategia en tres niveles

### Nivel 1: Curar ejemplos
Crear 10-20 ejemplos de alta calidad manualmente. Definen qué es "bueno" para el sistema. Sin estos, no hay baseline para medir.

### Nivel 2: Evaluar componentes
Descomponer el sistema y evaluar cada componente independientemente:
- Para RAG: evaluar retrieval (¿se recuperaron los docs correctos?) + generation (¿es correcta la respuesta con esos docs?)
- Para tool use: ¿llamó a la herramienta correcta con los parámetros correctos?

### Nivel 3: Trajectory Evaluation (AgentEvals)
Evaluar la **secuencia completa** de mensajes y tool calls:

| Modo | Mecanismo | Cuándo |
|------|-----------|--------|
| **Trajectory match** | Comparar contra trayectoria de referencia hardcodeada | Workflows bien definidos donde sabes el comportamiento esperado |
| **LLM judge** | Un LLM evalúa si la trayectoria fue apropiada | Workflows abiertos donde el camino correcto puede variar |

## Métricas clave para agentes

- Tasa de éxito en la tarea final
- Tool call accuracy (herramienta correcta + parámetros correctos)
- Eficiencia (número de steps para completar la tarea)
- Tasa de hallucination en razonamiento intermedio
- Consistencia entre runs similares

## Integración con LangGraph

LangSmith traza automáticamente todos los nodos y edges de un grafo LangGraph. Cada super-step es un span trazable, cada node call es un evento registrado. Ver [[concepts/langgraph]].

## Conexiones

- [[concepts/agent-evaluation]] — síntesis completa de cómo evaluar agentes
- [[balrog]] — benchmark académico complementario (evaluación en entornos dinámicos)
- [[concepts/langgraph]] — LangSmith es la herramienta de observabilidad nativa de LangGraph
- [[concepts/rag]] — evaluación de RAG como caso especial de evaluación de pasos intermedios
