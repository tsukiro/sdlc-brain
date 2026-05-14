---
title: "LangGraph.js — Guía de implementación TypeScript/Node.js"
type: source-summary
tags: [langgraph, javascript, typescript, nodejs, agentes, stategraph, persistence]
sources: []
updated: 2026-05-14
---

# LangGraph.js — Guía de implementación TypeScript/Node.js

**Fuente:** https://docs.langchain.com/oss/javascript/langgraph/overview  
**Instalación:** `npm install @langchain/langgraph @langchain/core`

## Perspectiva de esta fuente

Documentación oficial de LangGraph para JavaScript/TypeScript. Comparte la misma arquitectura conceptual que la versión Python, con adaptaciones al ecosistema Node.js: tipado estricto con TypeScript y solo programación async.

---

## Instalación

```bash
npm install @langchain/langgraph @langchain/core
# + proveedor LLM
npm install @langchain/openai     # OpenAI
npm install @langchain/anthropic  # Anthropic
npm install @langchain/google-genai # Google Gemini

# Alternativas
yarn add @langchain/langgraph @langchain/core @langchain/openai
pnpm add @langchain/langgraph @langchain/core @langchain/openai
bun add @langchain/langgraph @langchain/core @langchain/openai
```

---

## Conceptos fundamentales en TypeScript

### State con MessagesValue

```typescript
import { StateGraph, START, END, MessagesAnnotation } from "@langchain/langgraph";
import { BaseMessage } from "@langchain/core/messages";

// Opción 1: MessagesAnnotation (prebuilt para conversaciones)
const graph = new StateGraph(MessagesAnnotation);

// Opción 2: Schema personalizado
import { Annotation } from "@langchain/langgraph";

const StateAnnotation = Annotation.Root({
    messages: MessagesAnnotation.spec,
    userId: Annotation<string>(),
    stepCount: Annotation<number>({
        reducer: (a, b) => a + b,
        default: () => 0,
    }),
});
```

### Nodes

```typescript
import { ChatOpenAI } from "@langchain/openai";

const llm = new ChatOpenAI({ model: "gpt-4o-mini" });

async function agentNode(state: typeof StateAnnotation.State) {
    const response = await llm.invoke(state.messages);
    return { messages: [response] };
}
```

### Edges

```typescript
import { ToolNode } from "@langchain/langgraph/prebuilt";
import { isAIMessage } from "@langchain/core/messages";

const tools = [getWeather];
const toolNode = new ToolNode(tools);

function routeAfterAgent(state: typeof StateAnnotation.State): string {
    const lastMessage = state.messages[state.messages.length - 1];
    if (isAIMessage(lastMessage) && lastMessage.tool_calls?.length) {
        return "tools";
    }
    return "__end__";
}
```

---

## Flujo completo: ReAct agent en TypeScript

```typescript
import { ChatOpenAI } from "@langchain/openai";
import { tool } from "@langchain/core/tools";
import { StateGraph, START, END, MessagesAnnotation } from "@langchain/langgraph";
import { ToolNode, isAIMessage } from "@langchain/langgraph/prebuilt";
import { HumanMessage } from "@langchain/core/messages";
import { z } from "zod";

// 1. Definir tools
const getWeather = tool(
    async ({ city }) => `Soleado, 22°C en ${city}.`,
    {
        name: "get_weather",
        description: "Obtiene el clima de una ciudad.",
        schema: z.object({ city: z.string() }),
    }
);

const tools = [getWeather];
const llm = new ChatOpenAI({ model: "gpt-4o-mini" }).bindTools(tools);
const toolNode = new ToolNode(tools);

// 2. Nodo del agente
async function agent(state: typeof MessagesAnnotation.State) {
    const response = await llm.invoke(state.messages);
    return { messages: [response] };
}

// 3. Routing
function route(state: typeof MessagesAnnotation.State): string {
    const last = state.messages[state.messages.length - 1];
    return isAIMessage(last) && last.tool_calls?.length ? "tools" : "__end__";
}

// 4. Construir y compilar
const graph = new StateGraph(MessagesAnnotation)
    .addNode("agent", agent)
    .addNode("tools", toolNode)
    .addEdge(START, "agent")
    .addConditionalEdges("agent", route)
    .addEdge("tools", "agent")
    .compile();

// 5. Ejecutar
const result = await graph.invoke({
    messages: [new HumanMessage("¿Qué clima hace en Madrid?")]
});
console.log(result.messages[result.messages.length - 1].content);
```

---

## Atajo: createReactAgent

```typescript
import { createReactAgent } from "@langchain/langgraph/prebuilt";
import { ChatOpenAI } from "@langchain/openai";
import { HumanMessage } from "@langchain/core/messages";

const agent = createReactAgent({
    llm: new ChatOpenAI({ model: "gpt-4o-mini" }),
    tools: [getWeather],
    prompt: "Eres un asistente de meteorología.",
});

const result = await agent.invoke({
    messages: [new HumanMessage("¿Clima en Barcelona?")]
});
```

---

## Persistencia (Checkpointing) en Node.js

```typescript
import { MemorySaver } from "@langchain/langgraph";

// Desarrollo
const checkpointer = new MemorySaver();
const appWithMemory = graph.compile({ checkpointer });

// Thread ID para identificar la conversación
const config = { configurable: { thread_id: "user-123-session-1" } };

// Primera invocación
await appWithMemory.invoke(
    { messages: [new HumanMessage("Hola, me llamo Ana")] },
    config
);

// Segunda invocación — recuerda el contexto anterior
await appWithMemory.invoke(
    { messages: [new HumanMessage("¿Cómo me llamo?")] },
    config
);
// Responde: "Te llamas Ana"
```

### Checkpointer SQL en Node.js

```bash
npm install @langchain/langgraph-checkpoint-sqlite
```

```typescript
import { SqliteSaver } from "@langchain/langgraph-checkpoint-sqlite";
import Database from "better-sqlite3";

const db = new Database("checkpoints.db");
const checkpointer = SqliteSaver.fromConn(db);
const app = graph.compile({ checkpointer });
```

---

## Human-in-the-Loop en TypeScript

```typescript
import { interrupt, Command } from "@langchain/langgraph";

async function reviewStep(state: typeof MessagesAnnotation.State) {
    // Pausa y espera input humano
    const feedback = interrupt("¿Apruebas esta acción? (si/no)");
    return { messages: [{ role: "system", content: `Decisión: ${feedback}` }] };
}

const appWithHITL = graph.compile({ checkpointer: new MemorySaver() });

// Ejecutar hasta el interrupt
const partialResult = await appWithHITL.invoke(input, config);
// partialResult.__interrupt__ contiene el mensaje de pausa

// Reanudar con la decisión
const finalResult = await appWithHITL.invoke(new Command({ resume: "si" }), config);
```

---

## Streaming en Node.js

```typescript
// Stream token a token
const stream = await app.stream(
    { messages: [new HumanMessage("Explícame LangGraph")] },
    { ...config, streamMode: "messages" }
);

for await (const [chunk, metadata] of stream) {
    process.stdout.write(chunk.content as string);
}

// Stream de actualizaciones de estado
const eventStream = await app.stream(input, { ...config, streamMode: "updates" });
for await (const event of eventStream) {
    console.log(event); // { agent: { messages: [...] } }
}
```

---

## Diferencias Python vs TypeScript en LangGraph

| Dimensión | Python | TypeScript |
|---|---|---|
| **State definition** | `TypedDict` o Pydantic `BaseModel` | `Annotation.Root({...})` |
| **Reducers** | `Annotated[list, add_messages]` | `Annotation<list>({ reducer: addMessages })` |
| **Tool schema** | Anotaciones de tipo + docstring | Zod schema |
| **Ejecución síncrona** | Sí (`.invoke()`) | No — solo async (`await graph.invoke()`) |
| **Routing constante** | `END` (string literal) | `"__end__"` (string) |
| **ToolNode import** | `from langgraph.prebuilt import ToolNode` | `from @langchain/langgraph/prebuilt` |

---

## Conexiones

- [[concepts/langgraph]] — conceptos y primitivas principales
- [[langgraph-python]] — versión Python con más detalle en persistencia
- [[langchain-js]] — LangChain base en JavaScript
- [[entities/langchain]] — entidad del ecosistema
- [[concepts/structured-generation]] — Zod como schema equivalente a Pydantic para outputs tipados
- [[langsmith-evaluation]] — observabilidad (igual que Python, variables de entorno)
