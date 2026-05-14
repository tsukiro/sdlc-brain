---
title: "LangGraph Python — Guía de implementación"
type: source-summary
tags: [langgraph, python, agentes, stategraph, persistence, checkpointing, human-in-the-loop, streaming]
sources: []
updated: 2026-05-14
---

# LangGraph Python — Guía de implementación

**Fuente:** https://docs.langchain.com/oss/python/langgraph/overview + /persistence  
**Instalación:** `pip install -U langgraph`

## Perspectiva de esta fuente

Documentación oficial de LangGraph Python. LangGraph es la capa de orquestación de bajo nivel de la familia LangChain: controla el flujo de ejecución, el estado compartido y la persistencia de agentes stateful de larga duración.

---

## Qué es LangGraph

> "Un framework de orquestación de bajo nivel y runtime para construir, gestionar y desplegar agentes stateful de larga duración."

El diferenciador clave frente a LangChain base: **control granular** sobre cada paso del agente. LangGraph es el estándar de la industria para agentes en producción (~400 empresas).

---

## Los tres conceptos fundamentales

### State
Un `TypedDict` o `BaseModel` de Pydantic que representa el snapshot del agente. Cada key puede tener un **reducer** que define cómo se combinan valores concurrentes:

```python
from typing import Annotated, TypedDict
from langgraph.graph.message import add_messages

class State(TypedDict):
    messages: Annotated[list, add_messages]  # reducer: añade al historial
    user_id: str                              # sin reducer: sobreescribe
    step_count: Annotated[int, lambda a, b: a + b]  # reducer: acumula
```

### Nodes
Funciones Python (sync o async) que reciben state, ejecutan lógica y retornan un dict con los campos a actualizar:

```python
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(model="gpt-4o-mini")

def agent_node(state: State) -> dict:
    response = llm.invoke(state["messages"])
    return {"messages": [response]}

async def async_agent_node(state: State) -> dict:
    response = await llm.ainvoke(state["messages"])
    return {"messages": [response]}
```

### Edges
Conexiones entre nodos — fijas o condicionales:

```python
from langgraph.graph import StateGraph, START, END

graph = StateGraph(State)
graph.add_node("agent", agent_node)
graph.add_node("tools", tool_node)

# Edge fijo
graph.add_edge(START, "agent")

# Edge condicional (routing)
def should_use_tools(state: State) -> str:
    last_message = state["messages"][-1]
    if hasattr(last_message, "tool_calls") and last_message.tool_calls:
        return "tools"
    return END

graph.add_conditional_edges("agent", should_use_tools)
graph.add_edge("tools", "agent")  # loop: tools → agent
```

---

## Flujo completo: ReAct agent desde cero

```python
from langchain_openai import ChatOpenAI
from langchain_core.tools import tool
from langgraph.graph import StateGraph, START, END, MessagesState
from langgraph.prebuilt import ToolNode

# 1. Definir tools
@tool
def get_weather(city: str) -> str:
    """Obtiene el clima de una ciudad."""
    return f"Soleado, 22°C en {city}."

tools = [get_weather]
llm = ChatOpenAI(model="gpt-4o-mini").bind_tools(tools)

# 2. Nodos
def agent(state: MessagesState):
    return {"messages": [llm.invoke(state["messages"])]}

tool_node = ToolNode(tools)

# 3. Routing
def route(state: MessagesState):
    last = state["messages"][-1]
    return "tools" if last.tool_calls else END

# 4. Construir grafo
graph = StateGraph(MessagesState)
graph.add_node("agent", agent)
graph.add_node("tools", tool_node)
graph.add_edge(START, "agent")
graph.add_conditional_edges("agent", route)
graph.add_edge("tools", "agent")

# 5. Compilar y ejecutar
app = graph.compile()
result = app.invoke({"messages": [("human", "¿Qué clima hace en Madrid?")]})
```

---

## Atajo: create_react_agent

Para el patrón ReAct estándar, `create_react_agent` evita construir el grafo manualmente:

```python
from langgraph.prebuilt import create_react_agent

agent = create_react_agent(
    model=ChatOpenAI(model="gpt-4o-mini"),
    tools=[get_weather],
    prompt="Eres un asistente de meteorología.",
)

result = agent.invoke({"messages": [("human", "¿Clima en Barcelona?")]})
```

---

## Persistencia (Checkpointing)

La persistencia permite que los agentes retengan estado entre conversaciones y se recuperen de fallos:

```python
from langgraph.checkpoint.memory import InMemorySaver

# Desarrollo / testing
checkpointer = InMemorySaver()
app = graph.compile(checkpointer=checkpointer)

# Invocar con thread_id para identificar la conversación
config = {"configurable": {"thread_id": "user-123-session-1"}}
app.invoke({"messages": [("human", "Hola")]}, config)

# La siguiente invocación retoma la conversación automáticamente
app.invoke({"messages": [("human", "¿Recuerdas lo que te dije?")]}, config)
```

### Checkpointers para producción

```bash
# SQLite (local)
pip install langgraph-checkpoint-sqlite

# PostgreSQL (producción)
pip install langgraph-checkpoint-postgres

# Azure CosmosDB
pip install langchain-azure-cosmosdb
```

```python
# PostgreSQL en producción
from langgraph.checkpoint.postgres import PostgresSaver

with PostgresSaver.from_conn_string("postgresql://user:pass@host/db") as checkpointer:
    app = graph.compile(checkpointer=checkpointer)
```

---

## Inspección y time travel

```python
# Estado actual
snapshot = app.get_state(config)
print(snapshot.values)   # estado
print(snapshot.next)     # próximos nodos a ejecutar

# Historial completo
for state in app.get_state_history(config):
    print(state.config["checkpoint_id"], state.values)

# Replay desde checkpoint anterior (time travel)
old_config = {
    "configurable": {
        "thread_id": "user-123-session-1",
        "checkpoint_id": "<id-del-checkpoint>"
    }
}
app.invoke(None, old_config)  # retoma desde ese punto

# Modificar estado manualmente
app.update_state(config, {"messages": [("human", "Ignora la pregunta anterior")]})
```

---

## Human-in-the-Loop (HITL)

```python
from langgraph.types import interrupt

def review_step(state: State) -> dict:
    # Pausa la ejecución y espera input humano
    human_feedback = interrupt("¿Apruebas esta acción?")
    return {"approved": human_feedback == "si"}

app = graph.compile(checkpointer=InMemorySaver())

# Ejecutar hasta el interrupt
result = app.invoke(input_data, config)
# result["__interrupt__"] contiene el mensaje de pausa

# Reanudar con la decisión humana
app.invoke(Command(resume="si"), config)
```

---

## Memory Store (memoria entre sesiones)

```python
from langgraph.store.memory import InMemoryStore
from langchain.embeddings import init_embeddings

# Store con búsqueda semántica
store = InMemoryStore(
    index={
        "embed": init_embeddings("openai:text-embedding-3-small"),
        "dims": 1536,
        "fields": ["$"],
    }
)

# Guardar memoria
store.put(("user-123", "preferences"), "mem-1", {"topic": "Python", "level": "avanzado"})

# Buscar semánticamente
results = store.search(("user-123", "preferences"), query="lenguajes de programación", limit=3)

# Compilar grafo con store
app = graph.compile(checkpointer=InMemorySaver(), store=store)
```

---

## Primitivas avanzadas

| Primitiva | Para qué | Código |
|---|---|---|
| `Command` | Update state + navegar en un paso | `return Command(update={...}, goto="node")` |
| `Send` | Map-reduce: un nodo genera N invocaciones | `return [Send("node", {"item": x}) for x in items]` |
| `Subgraph` | Modularidad: grafo dentro de grafo | `graph.add_node("sub", subgraph.compile())` |
| `CachePolicy` | Cachear resultados de nodos costosos | `@cached(ttl=3600)` |

---

## Streaming

```python
# Streaming de mensajes token a token
for chunk in app.stream(
    {"messages": [("human", "Explícame qué es LangGraph")]},
    config,
    stream_mode="messages"
):
    print(chunk[0].content, end="", flush=True)

# Streaming de actualizaciones de estado
for event in app.stream(input_data, config, stream_mode="updates"):
    print(event)  # {"agent": {"messages": [...]}}
```

---

## Conexiones

- [[concepts/langgraph]] — concepto principal (primitivas, arquitectura)
- [[langgraph]] — source-summary de la versión anterior (doc inicial)
- [[langgraph-js]] — equivalente TypeScript/Node.js
- [[langchain-python]] — LangChain base (tools, messages, modelos)
- [[concepts/structured-generation]] — usar Instructor/Pydantic en nodos para outputs tipados
- [[concepts/prompt-injection]] — Dual LLM implementable con dos subgrafos LangGraph
- [[langsmith-evaluation]] — observabilidad y trazabilidad
- [[entities/langchain]] — entidad del ecosistema
