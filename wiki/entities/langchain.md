---
title: "LangChain (ecosistema)"
type: entity
tags: [langchain, framework, ecosistema, python, typescript, langsmith, langgraph]
sources: []
updated: 2026-05-14
---

# LangChain

**Tipo:** Ecosistema open-source de frameworks para LLMs  
**Organización:** LangChain, Inc.  
**Docs:** https://docs.langchain.com  
**Repositorios:** github.com/langchain-ai/langchain · langchain-ai/langgraph

## Qué es

LangChain es simultáneamente una empresa, un ecosistema y el nombre del framework base. El ecosistema comprende tres productos:

| Producto | Propósito | Plataforma |
|---|---|---|
| **LangChain** | Framework base: models, tools, chains, retrievers | Python + JS |
| **LangGraph** | Orquestación de agentes stateful con grafos | Python + JS |
| **LangSmith** | Observabilidad, evaluación, trazabilidad | SaaS + SDK |

## Adopción (2026)

- ~400 empresas usando LangGraph Platform en producción
- Clientes: Klarna, Uber, LinkedIn, Elastic, BlackRock, Cisco, Replit, JPMorgan

## Stack técnico

```
Aplicación
    ↓
LangSmith (trazabilidad)
    ↓
LangGraph (orquestación del flujo)
    ↓
LangChain (abstracciones: models, tools, messages)
    ↓
APIs de LLMs (OpenAI, Anthropic, Gemini, Ollama...)
```

## Paquetes principales

### Python
```
langchain            — framework base
langchain-core       — abstracciones fundamentales (shared con LangGraph)
langchain-openai     — integración OpenAI
langchain-anthropic  — integración Anthropic
langchain-google-genai — integración Gemini
langgraph            — orquestación
langgraph-checkpoint-sqlite / postgres — persistencia
langsmith            — SDK de observabilidad
```

### JavaScript / TypeScript
```
langchain            — framework base
@langchain/core      — abstracciones fundamentales
@langchain/openai    — integración OpenAI
@langchain/anthropic — integración Anthropic
@langchain/google-genai — integración Gemini
@langchain/langgraph — orquestación
@langchain/langgraph-checkpoint-sqlite — persistencia local
```

## Conexiones

- [[concepts/langchain]] — arquitectura y casos de uso del framework
- [[concepts/langgraph]] — primitivas de orquestación
- [[langchain-python]] — guía de implementación Python
- [[langchain-js]] — guía de implementación JS/TS
- [[langgraph-python]] — guía de implementación LangGraph Python
- [[langgraph-js]] — guía de implementación LangGraph JS
- [[langsmith-evaluation]] — evaluación y trazabilidad
- [[concepts/multi-agent-frameworks]] — LangGraph vs CrewAI
