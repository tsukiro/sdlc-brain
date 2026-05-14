---
title: "LangChain — Framework base para aplicaciones LLM"
type: concept
tags: [langchain, framework, agentes, llm, tools, messages, python, typescript]
sources: []
updated: 2026-05-14
---

# LangChain — Framework base para aplicaciones LLM

> [!info]
> LangChain es el framework de abstracción que estandariza la interacción con LLMs, herramientas y datos. LangGraph (orquestación) y LangSmith (observabilidad) se construyen sobre él o junto a él.

---

## La familia LangChain

```
docs.langchain.com
├── LangChain         ← framework base (este concepto)
│   ├── Python:       langchain + langchain-[proveedor]
│   └── JavaScript:   langchain + @langchain/[proveedor]
│
├── LangGraph         ← orquestación de agentes stateful
│   ├── Python:       langgraph
│   └── JavaScript:   @langchain/langgraph
│
└── LangSmith         ← observabilidad, evaluación, trazabilidad
    └── SaaS platform
```

---

## Qué resuelve LangChain

### Problema 1: Vendor lock-in de modelos
Cada proveedor de LLM tiene una API diferente. LangChain provee una **interfaz unificada** que permite cambiar de `ChatOpenAI` a `ChatAnthropic` cambiando una línea.

### Problema 2: Boilerplate de integración
Parsear respuestas, manejar tool calls, construir historial de mensajes, conectar retrievers — todo tiene boilerplate. LangChain lo abstrae.

### Problema 3: Composición de componentes
Las chains permiten conectar modelo → retriever → output parser → tool de forma declarativa.

---

## Componentes principales

### Messages
La unidad fundamental de contexto:

| Tipo | Rol |
|---|---|
| `SystemMessage` | Instrucciones del sistema (persona, restricciones) |
| `HumanMessage` | Input del usuario (texto, imágenes, audio) |
| `AIMessage` | Respuesta del modelo (+ tool_calls) |
| `ToolMessage` | Resultado de una herramienta (enlazado por `tool_call_id`) |

### Models (Chat Models)
Interfaz unificada para modelos de chat:
```python
model = ChatOpenAI(model="gpt-4o-mini")
model = ChatAnthropic(model="claude-sonnet-4-6")
model = ChatGoogleGenerativeAI(model="gemini-3-flash-preview")
# La misma interfaz: model.invoke(messages)
```

### Tools
Funciones que los agentes pueden invocar. El schema de la función (tipos + docstring en Python; Zod en JS) es la descripción que el LLM usa para decidir cuándo usarla.

### Retrievers
Abstracciones para búsqueda: vector stores, keyword search, hybrid. Se encadenan con modelos para RAG.

### Output Parsers
Transforman la respuesta del LLM en estructuras específicas. En la práctica, Instructor/Pydantic es más robusto para este propósito (ver [[concepts/structured-generation]]).

---

## Instalación por plataforma

### Python

```bash
# Core
pip install langchain langchain-core

# Por proveedor
pip install langchain-openai       # OpenAI / Azure
pip install langchain-anthropic    # Anthropic (Claude)
pip install langchain-google-genai # Google Gemini
pip install langchain-ollama       # Ollama (local)
```

### Node.js / TypeScript

```bash
# Core
npm install langchain @langchain/core

# Por proveedor
npm install @langchain/openai
npm install @langchain/anthropic
npm install @langchain/google-genai
```

---

## Patrón básico: agente con tools

### Python
```python
from langchain_openai import ChatOpenAI
from langchain_core.tools import tool
from langgraph.prebuilt import create_react_agent

@tool
def search(query: str) -> str:
    """Busca información en internet."""
    return f"Resultados para: {query}"

agent = create_react_agent(
    model=ChatOpenAI(model="gpt-4o-mini"),
    tools=[search],
)
result = agent.invoke({"messages": [("human", "¿Qué es LangGraph?")]})
```

### TypeScript / Node.js
```typescript
import { ChatOpenAI } from "@langchain/openai";
import { tool } from "@langchain/core/tools";
import { createReactAgent } from "@langchain/langgraph/prebuilt";
import { z } from "zod";

const search = tool(
    async ({ query }) => `Resultados para: ${query}`,
    { name: "search", description: "Busca en internet.", schema: z.object({ query: z.string() }) }
);

const agent = createReactAgent({
    llm: new ChatOpenAI({ model: "gpt-4o-mini" }),
    tools: [search],
});

const result = await agent.invoke({ messages: [{ role: "user", content: "¿Qué es LangGraph?" }] });
```

---

## LangChain vs LangGraph: cuándo usar cada uno

| Situación | Usar |
|---|---|
| Agente simple: LLM + 2-3 tools | `create_react_agent` (LangGraph prebuilt) |
| Workflow multi-paso con estado compartido | `StateGraph` (LangGraph) |
| Conversación con memoria entre sesiones | LangGraph + checkpointer |
| Human-in-the-loop / aprobación manual | LangGraph + interrupts |
| Pipeline de transformación (LLM → parser → DB) | LangChain chains / LCEL |
| Prototipo rápido sin control de flujo | LangChain básico |

> **Regla práctica**: empieza con `create_react_agent`. Cuando necesites control granular del flujo, estado personalizado o persistencia avanzada, migra a `StateGraph`.

---

## Observabilidad (LangSmith)

```bash
# Python o Node.js — misma configuración
export LANGSMITH_API_KEY="lsv2_..."
export LANGSMITH_TRACING="true"
export LANGSMITH_PROJECT="mi-proyecto"
```

Activar estas variables habilita trazado automático de cada invocación: inputs, outputs, latencia, tokens, errores.

---

## Tensiones y consideraciones

- **Abstracción vs. control**: LangChain abstrae mucho; cuando algo falla, puede ser difícil debuggear. LangSmith resuelve esto.
- **Madurez Python > JS**: Python tiene más integraciones y ejemplos. JS/TS está en paridad creciente.
- **Liu ("simple tools win")**: antes de agregar LangChain, evalúa si la llamada directa a la API del LLM no es suficiente. Ver [[jxnl-pipelines]].

---

## Conexiones

- [[langchain-python]] — implementación Python detallada
- [[langchain-js]] — implementación JavaScript/TypeScript
- [[concepts/langgraph]] — orquestación avanzada (se construye sobre LangChain)
- [[concepts/rag]] — RAG usa retrievers de LangChain
- [[langsmith-evaluation]] — observabilidad del ecosistema
- [[concepts/structured-generation]] — Instructor se integra con LangChain para outputs tipados
- [[entities/langchain]] — entidad del framework
- [[jxnl-pipelines]] — "simple tools win" — cuándo NO usar LangChain
