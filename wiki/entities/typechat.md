---
title: "TypeChat (Microsoft Research)"
type: entity
tags: [structured-generation, typechat, microsoft, typescript, llm-pipelines]
sources: []
updated: 2026-05-13
---

# TypeChat

**Tipo:** Librería TypeScript open-source  
**Organización:** Microsoft Research  
**Repositorio:** https://github.com/microsoft/TypeChat  
**Docs:** https://microsoft.github.io/TypeChat/

## Qué es

TypeChat es una librería de Microsoft Research que utiliza **interfaces TypeScript** como especificación para las respuestas de LLMs. El compilador de TypeScript actúa como validador; sus mensajes de error se retroalimentan al modelo para autocorrección.

## Premisa

> "Types are all you need" — Microsoft TypeChat team

Los tipos TypeScript reemplazan al prompt engineering como mecanismo principal de control de la salida del LLM.

## Flujo

```typescript
// 1. Define la interfaz
interface OrderResponse {
  items: { name: string; quantity: number }[];
  total: number;
}

// 2. TypeChat construye el prompt con el esquema TS incluido
// 3. LLM responde con JSON
// 4. tsc valida el JSON contra la interfaz
// 5. Si falla: el error de tsc va de vuelta al LLM
// 6. Resultado: objeto TypeScript validado y tipado ✓
```

## Diferenciador

TypeScript como schema language es **más expresivo** que JSON Schema:
- Union types: `"pendiente" | "completado" | "cancelado"`
- Optional fields: `descripcion?: string`
- Comentarios inline que guían al LLM

## Posición en el ecosistema

| | TypeChat | Instructor |
|---|---|---|
| **Lenguaje** | TypeScript | Python |
| **Validador** | Compilador TypeScript (tsc) | Runtime Pydantic |
| **Ecosistema** | Node.js, Next.js, apps TS | FastAPI, LangChain, agentes Python |

## Conexiones

- [[typechat]] — source-summary detallado
- [[entities/instructor]] — equivalente Python
- [[concepts/structured-generation]] — principio compartido
- [[outlines-paper]] — base matemática del constrained decoding
