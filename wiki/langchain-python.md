---
title: "LangChain Python — Documentación oficial"
type: source-summary
tags: [langchain, python, agentes, llm, framework, tools, messages]
sources: []
updated: 2026-05-14
---

# LangChain Python — Documentación oficial

**Fuente:** https://docs.langchain.com/oss/python/langchain/overview  
**Instalación:** `pip install -qU langchain "langchain[openai]"` (o el proveedor que corresponda)

## Perspectiva de esta fuente

Documentación oficial de LangChain para Python. LangChain es el framework base de la familia; LangGraph y LangSmith se construyen sobre él o junto a él.

---

## Qué es LangChain

> "Un framework open source con arquitectura de agentes preintegrada e integraciones para cualquier modelo o herramienta."

LangChain permite construir aplicaciones y agentes impulsados por LLMs con ~10 líneas de código. Provee:
- Una **interfaz estándar** para interactuar con 50+ proveedores de LLMs
- Abstracciones para **tools**, **chains**, **retrievers** y **memory**
- Integración nativa con **LangGraph** (orquestación) y **LangSmith** (observabilidad)

---

## Instalación por proveedor

```bash
# OpenAI
pip install -qU langchain langchain-openai

# Anthropic
pip install -qU langchain langchain-anthropic

# Google Gemini
pip install -qU langchain langchain-google-genai

# Ollama (local)
pip install -qU langchain langchain-ollama

# Azure OpenAI
pip install -qU langchain langchain-openai  # misma librería, config diferente
```

---

## Tipos de mensajes

Los mensajes son la unidad fundamental del contexto para los modelos de chat:

| Tipo | Rol | Uso |
|---|---|---|
| `SystemMessage` | Instrucciones del sistema | Comportamiento del modelo, persona, restricciones |
| `HumanMessage` | Input del usuario | Texto, imágenes, audio, archivos |
| `AIMessage` | Respuesta del modelo | Contenido + tool_calls + metadata |
| `ToolMessage` | Resultado de una tool | Se requiere `tool_call_id` para enlazar con `AIMessage` |

```python
from langchain_core.messages import SystemMessage, HumanMessage, AIMessage, ToolMessage

messages = [
    SystemMessage("Eres un asistente experto en Python."),
    HumanMessage("¿Cómo parseo un JSON?"),
]
response = model.invoke(messages)
# response es AIMessage
```

---

## Tools

Las tools son funciones que los agentes pueden invocar. Se definen con el decorador `@tool`:

```python
from langchain_core.tools import tool

@tool
def get_weather(city: str) -> str:
    """Obtiene el clima actual de una ciudad."""
    return f"El clima en {city} es soleado, 22°C."

@tool("database_search", description="Busca en la base de datos de clientes.")
def search_db(query: str, limit: int = 10) -> list[dict]:
    """Busca registros que coincidan con el query."""
    return []  # implementación real aquí
```

**Reglas importantes:**
- Las anotaciones de tipo son **obligatorias** — definen el schema de entrada para el LLM
- Nombres en `snake_case` para compatibilidad entre proveedores
- La docstring *es* la descripción que el LLM usa para decidir cuándo invocar la tool

### Acceso a contexto en tools

```python
from langchain_core.tools import tool
from langgraph.runtime import Runtime

@tool
async def save_memory(content: str, runtime: Runtime) -> str:
    """Guarda información en memoria persistente."""
    await runtime.store.aput(("memories",), "key", {"data": content})
    return "Guardado."
```

---

## Agente básico con LangChain + LangGraph

```python
from langchain_openai import ChatOpenAI
from langgraph.prebuilt import create_react_agent

model = ChatOpenAI(model="gpt-4o-mini")
tools = [get_weather, search_db]

# create_react_agent es el punto de entrada más simple
agent = create_react_agent(model, tools)

result = agent.invoke({
    "messages": [HumanMessage("¿Qué clima hace en Madrid?")]
})
```

---

## Interfaz estándar de modelos

La misma interfaz funciona con cualquier proveedor:

```python
# Intercambiar proveedor sin cambiar código de negocio
from langchain_anthropic import ChatAnthropic
from langchain_openai import ChatOpenAI

model = ChatAnthropic(model="claude-sonnet-4-6")
# o
model = ChatOpenAI(model="gpt-4o")

# La misma llamada funciona con ambos
response = model.invoke(messages)
```

---

## Integración con LangSmith (observabilidad)

```bash
export LANGSMITH_API_KEY="..."
export LANGSMITH_TRACING=true
```

Con estas variables, cada invocación se traza automáticamente en LangSmith — sin cambios de código.

---

## Conexiones

- [[concepts/langchain]] — concepto principal del framework
- [[langchain-js]] — equivalente JavaScript/TypeScript
- [[langgraph]] — orquestación avanzada (se construye sobre LangChain)
- [[langchain-text-splitters]] — splitters para RAG (mismo ecosistema)
- [[langsmith-evaluation]] — observabilidad y evaluación
- [[entities/langchain]] — entidad del framework
