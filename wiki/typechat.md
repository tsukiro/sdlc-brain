---
title: "TypeChat — Microsoft (tipos TypeScript como especificación LLM)"
type: source-summary
tags: [structured-generation, typechat, microsoft, typescript, llm-pipelines]
sources: []
updated: 2026-05-13
---

# TypeChat — Microsoft

**Fuente principal:** https://microsoft.github.io/TypeChat/blog/introducing-typechat/  
**Docs:** https://microsoft.github.io/TypeChat/  
**Repositorio:** https://github.com/microsoft/TypeChat

## Perspectiva de esta fuente

Blog de lanzamiento y documentación oficial de TypeChat, desarrollado por Microsoft Research. TypeChat propone reemplazar la ingeniería de prompts con **ingeniería de tipos**: las interfaces TypeScript son la especificación de lo que el LLM debe devolver, y el compilador de TypeScript es el árbitro de corrección.

---

## Premisa

> "Prompting is fragile; typing is robust."

> "Types are all you need."

El problema que resuelve: los LLMs responden naturalmente en lenguaje conversacional. Integrarlos en sistemas que esperan datos estructurados requería prompts frágiles y parsers ad-hoc. TypeChat elimina el parser y convierte el compilador de TypeScript en el validador.

---

## Flujo de operación

```
1. Desarrollador define interfaz TypeScript
   interface Pedido {
     items: string[];
     total: number;
   }

2. TypeChat construye el prompt: solicitud del usuario + esquema TypeScript

3. LLM genera JSON que respeta la estructura

4. Compilador TypeScript valida la respuesta contra la interfaz

5. Si hay errores de compilación:
   → Los errores se retroalimentan al LLM como contexto
   → El LLM reintenta con la corrección guiada por el compilador

6. App recibe datos validados y tipados ✓
```

---

## Diferencias respecto a Instructor (Python/Pydantic)

| Dimensión | TypeChat | Instructor |
|---|---|---|
| **Lenguaje** | TypeScript | Python |
| **Schema** | Interfaz TS (human-readable, muy expresivo) | BaseModel Pydantic |
| **Validador** | Compilador TypeScript | Runtime Pydantic |
| **Error feedback** | Mensajes de error del compilador TS | ValidationError de Pydantic |
| **Origen** | Microsoft Research | Open-source (Jason Liu) |

Ambos implementan el mismo principio: el tipo *es* el prompt.

---

## Por qué TypeScript como schema

Las interfaces TypeScript son:
- **Más expresivas** que JSON Schema: generics, union types, optional fields, comentarios
- **Legibles por humanos** — un developer puede leer la interfaz sin conocer JSON Schema
- **Ecosistema de tooling**: el compilador como validador es una herramienta de producción robusta, no una librería ad-hoc

---

## Conexiones

- [[concepts/structured-generation]] — principio abstracto que TypeChat implementa
- [[entities/typechat]] — entidad de la herramienta
- [[instructor]] — equivalente Python/Pydantic
- [[outlines-paper]] — base matemática compartida (constrained decoding)
