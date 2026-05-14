# LangGraph — Documentación oficial

**URLs:** https://langchain-ai.github.io/langgraph/ → redirige a docs.langchain.com/oss/python/langgraph  
**Fecha de captura:** 2026-05-13  
**Capturado vía:** docs.langchain.com/oss/python/langgraph/graph-api + búsqueda web

---

## Qué es LangGraph

Framework de Python para construir workflows de agentes modelados como grafos. Inspirado en el sistema Pregel de Google. Permite control preciso sobre flujo de ejecución, estado y persistencia.

## Componentes核 (núcleo)

### State
Estructura de datos compartida que representa el snapshot actual de la aplicación. Definida con TypedDict o Pydantic. Cada key puede tener un *reducer* que define cómo se agregan valores concurrentes.

### Nodes
Funciones Python (sync o async) que encodifican lógica de agentes. Reciben state, ejecutan computación o side-effects, devuelven state actualizado. "Nodes do the work."

### Edges
Funciones que determinan qué node ejecutar a continuación. Tipos:
- **Normal edge**: transición fija via `add_edge()`
- **Conditional edge**: routing dinámico via `add_conditional_edges()` + función de routing
- **START / END**: nodos especiales para entrada y salida

## Modelo de ejecución

Basado en message-passing (Pregel). Ejecución en "super-steps" discretos:
- Nodes empiezan inactivos
- Se activan al recibir mensajes en sus edges entrantes
- Ejecutan y envían updates
- Super-step completa cuando todos están inactivos

Límite de recursión: 1000 pasos por defecto (configurable). `RemainingSteps` para monitoreo proactivo.

## State Management

- `MessagesState`: clase prebuilt para workflows basados en mensajes
- `add_messages` reducer: maneja objetos LangChain Message con ID tracking
- Multiple schemas: schemas separados para estado interno, input y output
- Reducers custom con `Annotated` types

## Primitivas avanzadas

- **Command**: combina state updates + control flow en una sola operación (update + goto + graph + resume)
- **Send objects**: patrones map-reduce donde un nodo genera múltiples invocaciones downstream con diferentes instancias de estado
- **Subgraphs**: composición modular de grafos; parent-child navigation
- **Interrupts**: human-in-the-loop pausando ejecución y resumiendo con valores provistos
- **CachePolicy**: caching de tasks con TTL configurable

## Checkpointing

Persiste estado para reanudación. Soporta ejecución basada en thread_id. Maneja migraciones de topología preservando compatibilidad de checkpoints.

## Compilación

Grafos deben compilarse antes de ejecución via `.compile()`. Valida estructura y configura runtime (checkpointers, breakpoints).

## Casos de uso documentados

- Multi-agent collaboration
- Hierarchical agent teams
- Adaptive RAG (recuperación adaptativa con routing)
- Human-in-the-loop workflows

## Adopción en producción (2026)

~400 empresas en LangGraph Platform: Klarna, Uber, LinkedIn, Elastic, BlackRock, Cisco, Replit, JPMorgan.
