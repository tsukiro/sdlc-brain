---
title: "LangGraph"
type: concept
tags: [langgraph, agentes, grafo, state-machine, producción, langchain]
sources: [2026-05-13_langgraph.md]
updated: 2026-05-13
---

# LangGraph

## Qué es

Framework Python de LangChain para construir **workflows de agentes como grafos dirigidos**. Inspirado en el sistema Pregel de Google. La abstracción central: state + nodes + edges, donde el estado es explícito, persistente y compartido entre todos los nodos.

## Por qué importa

LangGraph es el estándar de facto para **agentic AI en producción** en 2026. ~400 empresas en producción (Klarna, Uber, LinkedIn, JPMorgan). Ofrece lo que los sistemas basados en chains de LangChain no tienen: control determinista del flujo, state management durable y human-in-the-loop nativo.

## Arquitectura

```
                    ┌─────────────────────────────────┐
                    │           StateGraph             │
                    │                                  │
  input ──► START ──► Node A ──► Node B ──► END ──► output
                    │     └──► Node C ──┘            │
                    │      (conditional)              │
                    └─────────────────────────────────┘
                              ↕ State (shared)
```

### State
TypedDict o Pydantic model. Cada campo puede tener un **reducer** que define cómo se agregan actualizaciones concurrentes. Ejemplo: `add_messages` acumula mensajes en lugar de sobreescribirlos.

### Nodes
Funciones Python que reciben state y retornan state actualizado. Pueden contener: llamadas a LLM, herramientas, lógica de código puro. No hay diferencia entre "agente" y "función" — todo es un nodo.

### Edges
- **Normal**: transición fija entre nodos
- **Conditional**: función de routing que devuelve el nombre del próximo nodo según el estado actual
- **START / END**: puntos especiales de entrada/salida

## Capacidades avanzadas

### Human-in-the-Loop (Interrupts)
Pausa el grafo en cualquier punto, espera input humano, y reanuda. Permite supervisión, corrección y aprobación en flujos críticos.

### Checkpointing
Persiste el estado completo del grafo en un store (en memoria, SQLite, Postgres, Redis). Permite: reanudar ejecuciones, time travel (volver a checkpoints anteriores), debugging de flujos complejos.

### Map-Reduce con Send
Un nodo puede generar N instancias downstream con diferentes estados — el patrón map-reduce nativo. Útil para: procesar múltiples documentos en paralelo, fan-out de subtareas.

### Subgraphs
Los grafos son componibles: un nodo puede ser otro grafo compilado. Permite modularidad y reutilización.

### Command Primitive
Combina state update + navegación en una sola operación atómica. Simplifica patrones donde la decisión de routing depende del resultado de la acción.

## Comparativa con alternativas

Ver [[multi-agent-frameworks]] para la comparativa completa con CrewAI.

**Resumen**: LangGraph es más complejo de aprender (~45 min al primer flow) pero es la elección para producción cuando se necesita state management avanzado, routing condicional y durabilidad.

## Patrones de uso comunes

1. **ReAct Agent**: nodo de razonamiento → nodo de herramientas → loop hasta respuesta final
2. **Supervisor**: un nodo LLM decide qué agente especializado invocar
3. **Multi-agent**: varios sub-grafos conectados por un grafo orquestador
4. **Adaptive RAG**: routing condicional basado en calidad del retrieval

## Stack típico

LangGraph + [[entities/gemini]] / OpenAI + LangChain text splitters + vector DB

## Conexiones

- [[langgraph]] — fuente primaria
- [[multi-agent-frameworks]] — comparativa con CrewAI
- [[crewai]] — framework alternativo (menor curva, menor control)
- [[rag]] — LangGraph para Adaptive RAG
- [[entities/gemini]] — LLM comúnmente usado con LangGraph
