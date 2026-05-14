---
title: "LangGraph — Documentación oficial"
type: source-summary
tags: [langgraph, agentes, grafo, state-machine, multi-agent, langchain]
sources: [2026-05-13_langgraph.md]
updated: 2026-05-13
---

# LangGraph — Documentación oficial

**Fuente:** https://langchain-ai.github.io/langgraph/ → docs.langchain.com

## Perspectiva de esta fuente

La documentación oficial de LangGraph. Es la fuente más técnica y prescriptiva: define la arquitectura, las primitivas y los patrones recomendados. No discute trade-offs con otros frameworks.

## Qué es LangGraph

Framework Python para construir workflows de agentes modelados como **grafos dirigidos**. Inspirado en el sistema Pregel de Google. El valor central: control preciso sobre flujo de ejecución, estado compartido y persistencia.

> "Nodes do the work, edges tell what to do next."

## Los tres pilares

### State
Estructura de datos compartida (TypedDict o Pydantic) que representa el snapshot actual de la aplicación. Cada key puede tener un **reducer** que define cómo se agregan valores concurrentes — por ejemplo, `add_messages` para una lista de mensajes, `operator.add` para acumular resultados.

### Nodes
Funciones Python (sync o async) que reciben state, ejecutan lógica y retornan state actualizado. Pueden contener un LLM o simplemente código. Se convierten automáticamente a `RunnableLambda` para soporte batch/async y tracing.

### Edges
Determina qué node ejecutar a continuación:
- **Normal edge**: `add_edge("node_a", "node_b")` — transición fija
- **Conditional edge**: `add_conditional_edges("node", routing_fn)` — routing dinámico
- **START / END**: puntos de entrada y salida del grafo

## Primitivas avanzadas

| Primitiva | Para qué sirve |
|-----------|----------------|
| **Command** | Combinar state update + navegación en una sola operación |
| **Send** | Map-reduce: un nodo genera N invocaciones downstream con diferentes estados |
| **Subgraphs** | Composición modular de grafos; parent-child navigation |
| **Interrupts** | Pausar ejecución para human-in-the-loop; reanudar con valores |
| **CachePolicy** | Caching de tasks con TTL configurable |
| **Checkpointing** | Persistir estado por thread_id; time travel entre checkpoints |

## Modelo de ejecución: super-steps

Basado en Pregel: la ejecución ocurre en "super-steps" discretos. Los nodos en paralelo pertenecen al mismo super-step; los secuenciales a super-steps distintos. Límite de recursión: 1000 (configurable).

## Flujo de desarrollo

```python
# 1. Definir state
class State(TypedDict):
    messages: Annotated[list, add_messages]

# 2. Definir nodes
def my_agent(state: State):
    return {"messages": [llm.invoke(state["messages"])]}

# 3. Construir grafo
graph = StateGraph(State)
graph.add_node("agent", my_agent)
graph.add_edge(START, "agent")
graph.add_edge("agent", END)

# 4. Compilar y ejecutar
app = graph.compile()
app.invoke({"messages": [HumanMessage(content="Hello")]})
```

## Adopción en producción (2026)

~400 empresas en LangGraph Platform: Klarna, Uber, LinkedIn, Elastic, BlackRock, Cisco, Replit, JPMorgan.

## Conexiones

- [[concepts/langgraph]] — página de concepto principal
- [[concepts/multi-agent-frameworks]] — comparativa LangGraph vs CrewAI
- [[crewai]] — framework alternativo
- [[concepts/rag]] — LangGraph usado para Adaptive RAG
