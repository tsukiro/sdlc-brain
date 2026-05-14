---
title: "LangChain JS/TypeScript — Documentación oficial"
type: source-summary
tags: [langchain, javascript, typescript, nodejs, agentes, llm, framework]
sources: []
updated: 2026-05-14
---

# LangChain JS/TypeScript — Documentación oficial

**Fuente:** https://docs.langchain.com/oss/javascript/langchain/overview  
**Instalación:** `npm install langchain @langchain/[proveedor]`

## Perspectiva de esta fuente

Documentación oficial de LangChain para JavaScript/TypeScript. Comparte la misma arquitectura conceptual que la versión Python, pero adaptada al ecosistema Node.js con tipado TypeScript.

---

## Qué es LangChain JS

> "Un framework open source con arquitectura de agente preintegrada e integraciones para cualquier modelo o herramienta."

Misma propuesta de valor que Python: interfaz unificada para LLMs, tools, chains y agentes — pero orientada a aplicaciones Node.js, Next.js y entornos JS/TS.

---

## Instalación

```bash
# Core + proveedor específico
npm install langchain @langchain/core @langchain/openai
npm install langchain @langchain/core @langchain/anthropic
npm install langchain @langchain/core @langchain/google-genai

# Para LangGraph (orquestación)
npm install @langchain/langgraph @langchain/core
```

```bash
# Alternativas
yarn add langchain @langchain/openai
pnpm add langchain @langchain/openai
bun add langchain @langchain/openai
```

---

## Tipos de mensajes (TypeScript)

```typescript
import { SystemMessage, HumanMessage, AIMessage, ToolMessage } from "@langchain/core/messages";

const messages = [
    new SystemMessage("Eres un asistente experto en TypeScript."),
    new HumanMessage("¿Cómo tipar un array de objetos?"),
];

const response = await model.invoke(messages);
// response es AIMessage
```

---

## Tools en TypeScript

```typescript
import { tool } from "@langchain/core/tools";
import { z } from "zod";

const getWeather = tool(
    async ({ city }: { city: string }) => {
        return `El clima en ${city} es soleado, 22°C.`;
    },
    {
        name: "get_weather",
        description: "Obtiene el clima actual de una ciudad.",
        schema: z.object({
            city: z.string().describe("Nombre de la ciudad"),
        }),
    }
);
```

**Diferencia con Python:** en JS se usa `zod` para definir el schema de entrada en lugar de anotaciones de tipo Python. El schema Zod es equivalente funcional al `BaseModel` de Pydantic.

---

## Agente básico con LangGraph.js

```typescript
import { ChatOpenAI } from "@langchain/openai";
import { createReactAgent } from "@langchain/langgraph/prebuilt";
import { HumanMessage } from "@langchain/core/messages";

const model = new ChatOpenAI({ model: "gpt-4o-mini" });
const tools = [getWeather];

const agent = createReactAgent({ llm: model, tools });

const result = await agent.invoke({
    messages: [new HumanMessage("¿Qué clima hace en Madrid?")]
});
```

---

## Deep Agents (opción "batteries-included")

La documentación menciona **Deep Agents** como alternativa de alto nivel: agentes con compresión automática de contexto y configuración mínima. Ideal para prototipado rápido sin necesidad de definir el grafo manualmente.

```bash
npm install @langchain/deep-agents
```

---

## Interfaz de modelos (provider-agnostic)

```typescript
// Intercambiar proveedor sin cambiar lógica
import { ChatAnthropic } from "@langchain/anthropic";
import { ChatOpenAI } from "@langchain/openai";
import { ChatGoogleGenerativeAI } from "@langchain/google-genai";

const model = new ChatAnthropic({ model: "claude-sonnet-4-6" });
// o
const model = new ChatOpenAI({ model: "gpt-4o" });
// o
const model = new ChatGoogleGenerativeAI({ model: "gemini-3-flash-preview" });

// La misma llamada funciona con los tres
const response = await model.invoke(messages);
```

---

## LangSmith (observabilidad)

```bash
# Variables de entorno
LANGSMITH_API_KEY=...
LANGSMITH_TRACING=true
```

Igual que Python: activar estas variables habilita trazado automático sin cambios de código.

---

## Diferencias Python vs JS

| Dimensión | Python | JavaScript/TypeScript |
|---|---|---|
| **Schema de tools** | Anotaciones de tipo + docstring | Zod schema |
| **Tipado** | Type hints (runtime opcional) | TypeScript estricto |
| **Async** | `async/await` o síncrono | Solo `async/await` |
| **Package manager** | pip / uv | npm / yarn / pnpm / bun |
| **Package names** | `langchain`, `langchain-openai` | `langchain`, `@langchain/openai` |
| **Madurez** | Mayor — más integraciones | Menor — paridad creciente |

---

## Conexiones

- [[concepts/langchain]] — concepto principal del framework
- [[langchain-python]] — versión Python (más completa)
- [[langgraph-js]] — orquestación en TypeScript
- [[entities/langchain]] — entidad del framework
- [[concepts/structured-generation]] — Zod en JS ↔ Pydantic en Python como schemas
