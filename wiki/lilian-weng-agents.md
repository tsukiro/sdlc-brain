---
title: "Lilian Weng — LLM Powered Autonomous Agents"
type: source-summary
tags: [agentes, planning, memoria, herramientas, react, reflexion, cot, tot, mrkl]
sources: [2026-05-13_lilian-weng-agents.md]
updated: 2026-05-13
---

# Lilian Weng — LLM Powered Autonomous Agents

**Fuente:** https://lilianweng.github.io/posts/2023-06-23-agent/  
**Autora:** Lilian Weng, OpenAI. Artículo de referencia ampliamente citado en la comunidad.

## Perspectiva de esta fuente

La taxonomía técnica más completa y citada sobre arquitectura de agentes LLM. Propone el marco **Planning + Memory + Tools** como los tres pilares de cualquier agente autónomo. Cubre técnicas de investigación con referencias a papers, no solo frameworks prácticos.

## La tesis central

> El LLM actúa como el "cerebro" del agente. Los tres componentes extienden sus capacidades más allá de la generación de texto.

```
              ┌─────────────────────────────────────────┐
              │           AGENTE LLM                    │
              │                                         │
              │   ┌─────────┐                           │
              │   │ PLANNING│  CoT, ToT, ReAct,         │
              │   │         │  Reflexion, LLM+P         │
              │   └────┬────┘                           │
              │        │                                │
Input ───────►│   ┌────▼────┐     ┌──────────────┐    │
              │   │  LLM    │────►│ TOOL USE     │    │──► Output
              │   │ (brain) │     │ MRKL, APIs,  │    │
              │   └────┬────┘     │ Funciones    │    │
              │        │          └──────────────┘    │
              │   ┌────▼────┐                           │
              │   │ MEMORY  │  STM (context), LTM       │
              │   │         │  (vector DB + ANN)        │
              │   └─────────┘                           │
              └─────────────────────────────────────────┘
```

## Técnicas de Planning — mapa completo

| Técnica | Idea central | Paper |
|---------|-------------|-------|
| **Chain of Thought** | "Think step by step" | Wei et al. 2022 |
| **Tree of Thoughts** | Explorar múltiples razonamientos en árbol (BFS/DFS) | Yao et al. 2023 |
| **ReAct** | Ciclo Thought → Action → Observation | Yao et al. 2023 |
| **Reflexion** | Detectar fallas → autorreflexión → mejorar | Shinn & Labash 2023 |
| **Chain of Hindsight** | Mejorar condicionado en historial de intentos anotados | Liu et al. 2023 |
| **LLM+P** | Delegar planificación a motor PDDL externo | Liu et al. 2023 |
| **Algorithm Distillation** | In-context RL desde historial de trayectorias | Laskin et al. 2023 |

## Tipos de memoria — mapeo humano → agente

| Memoria humana | Duración | Equiv. en agente |
|----------------|---------|-----------------|
| Sensorial | Segundos | Embeddings de inputs crudos |
| Corto plazo (STM) | ~20-30 seg, ~7 items | Ventana de contexto del Transformer |
| Largo plazo — explícita | Ilimitada | Vector DB + retrieval (ANN) |
| Largo plazo — procedural | Ilimitada | Pesos del modelo (fine-tuning) |

**Algoritmos ANN para LTM**: LSH, ANNOY, HNSW, FAISS, ScaNN. Todos sirven para MIPS (Maximum Inner Product Search) eficiente.

## Herramientas — arquitecturas clave

- **MRKL**: LLM como router hacia módulos expertos (simbólicos o neuronales)
- **HuggingGPT**: LLM planifica + delega a modelos especializados de HuggingFace
- **Toolformer**: fine-tuning para aprender cuándo y cómo llamar APIs
- **Function Calling / Plugins**: implementación práctica de tool use

## Desafíos (vigentes)

1. **Contexto finito**: el vector store ayuda pero no iguala la atención completa
2. **Planificación largo plazo**: LLMs fallan al ajustar planes ante errores inesperados
3. **Confiabilidad de interfaz natural**: errores de formato, comportamiento "rebelde"

> Estos tres desafíos son exactamente los que LangGraph y CrewAI intentan mitigar estructuralmente. Ver [[concepts/multi-agent-frameworks]].

## Hallazgo metodológico: autoevaluación sesgada

En ChemCrow: los LLMs evaluadores mostraron equivalencia entre el agente y GPT-4, pero los expertos humanos sí detectaron superioridad del agente. **Los LLMs son malos evaluando dominios especializados que ellos mismos no dominan bien.**

## Conexiones

- [[concepts/llm-agents]] — síntesis principal de arquitectura de agentes
- [[concepts/planning]] — Planning: CoT, ToT, ReAct, Reflexion detallados
- [[concepts/agent-memory]] — Memory: tipos, ANN, conexión con RAG
- [[andrew-ng-agentic-patterns]] — perspectiva complementaria (4 design patterns)
- [[concepts/langgraph]] — implementa ReAct, multi-agent, human-in-the-loop
- [[crewai]] — implementa multi-agent + memory nativa
- [[concepts/rag]] — LTM de agentes = pipeline RAG
