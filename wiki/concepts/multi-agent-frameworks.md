---
title: "Frameworks Multi-Agente: LangGraph vs CrewAI"
type: concept
tags: [langgraph, crewai, multi-agent, agentes, comparativa, producción]
sources: [2026-05-13_langgraph.md, 2026-05-13_crewai.md]
updated: 2026-05-13
---

# Frameworks Multi-Agente: LangGraph vs CrewAI

## Contexto

LangGraph y CrewAI son los dos frameworks multi-agente open-source más adoptados en 2026. Se dirigen al mismo problema (orquestar múltiples agentes de IA) pero desde filosofías opuestas. La elección entre ambos es una de las decisiones arquitecturales más frecuentes en proyectos de agentic AI.

## Tabla comparativa

| Dimensión | LangGraph | CrewAI |
|-----------|-----------|--------|
| **Metáfora** | Máquina de estados / grafo | Equipo de trabajo / crew |
| **Abstracción** | Bajo nivel (control explícito) | Alto nivel (roles y tareas) |
| **Curva de aprendizaje** | ~45 min al primer flow | ~10 min al primer flow |
| **State management** | Explícito, tipado, con reducers | Paso secuencial de task outputs |
| **Checkpointing** | Nativo (time travel incluido) | No nativo en OSS |
| **Routing** | Conditional edges (código) | Process types (sequential/hierarchical) |
| **Human-in-the-Loop** | Interrupts nativos | HITL por tarea |
| **Memoria** | Via checkpointing + stores | 4 tipos integrados (corto, largo, entidades, contextual) |
| **RAG integrado** | No (se conecta externamente) | Sí (Agentic RAG / Knowledge nativo) |
| **Velocidad de prototipado** | Lenta (más código) | Rápida (YAML + kickoff) |
| **Producción** | Consenso como estándar | Punto de partida, a veces se migra |
| **Empresas en producción** | ~400 en LangGraph Platform | No reportado públicamente |

## Cuándo elegir LangGraph

- El workflow tiene branching condicional complejo
- Se necesita state persistence y durabilidad (reanudar, time travel)
- El equipo quiere control determinista sobre cada paso
- Se está construyendo para producción a escala
- El proyecto es a largo plazo y puede asumir mayor complejidad inicial

## Cuándo elegir CrewAI

- El workflow es "un equipo de expertos colaborando en una tarea"
- Se necesita un prototipo funcional rápidamente
- El equipo no tiene experiencia con grafos/máquinas de estados
- El RAG está integrado en el problema (Knowledge nativo de CrewAI)
- Es un proyecto interno o de menor criticidad

## Patrón de adopción común (2026)

> "Teams que empiezan con CrewAI para prototipar frecuentemente migran a LangGraph cuando necesitan state management de producción y routing condicional."

El patrón es: **CrewAI para explorar la idea → LangGraph para produccionalizarla**.

## Modelos LLM compatibles

Ambos frameworks son agnósticos al LLM:
- [[entities/gemini]] (Gemini 3 Pro / 3 Flash) — bien soportado en ambos
- OpenAI GPT-4o / o3
- Anthropic Claude 3.x / 4.x
- Cualquier modelo via LiteLLM

## Tensiones y trade-offs detectados

1. **Abstracción vs Control**: CrewAI oculta la complejidad pero limita el control; LangGraph expone toda la complejidad pero permite cualquier arquitectura.
2. **RAG**: CrewAI lo integra nativamente (pero con menos control); LangGraph requiere conectarlo externamente (pero es más flexible).
3. **Memoria**: CrewAI tiene un sistema de memoria más rico out-of-the-box; LangGraph requiere implementarlo via checkpointing + stores.

## Conexiones

- [[langgraph]] — fuente primaria LangGraph
- [[concepts/langgraph]] — concepto detallado de LangGraph
- [[crewai]] — fuente primaria CrewAI
- [[rag]] — ambos frameworks se integran con pipelines RAG
- [[entities/gemini]] — LLM común para ambos frameworks
