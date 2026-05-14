---
title: "SDLC con IA — Flujo Recomendado"
type: analysis
tags: [sdlc, agentes, flujo, síntesis, planning, reflection, tool-use, multi-agent, devsecops]
sources: [2026-05-13_ibm-sdlc.md, 2026-05-13_aws-sdlc.md, 2026-05-13_atlassian-sdlc.md, 2026-05-13_andrew-ng-agentic-patterns.md, 2026-05-13_lilian-weng-agents.md, 2026-05-13_reflexion-paper.md, 2026-05-13_langgraph.md, 2026-05-13_crewai.md, 2026-05-13_owasp-llm-top10.md, 2026-05-13_llm-agents-vulnerabilities.md, 2026-05-13_computer-use-anthropic.md, 2026-05-13_prompt-injection-architecture.md]
updated: 2026-05-13
---

# SDLC con IA — Flujo Recomendado

> Síntesis de cómo aplicar los 4 patrones agénticos a cada fase del SDLC clásico.

## Principio guía

> La arquitectura del workflow importa más que el modelo. [[concepts/agentic-design-patterns]]

```
GPT-3.5 zero-shot:          48.1% HumanEval
GPT-4 zero-shot:            67.0% HumanEval
GPT-3.5 + flujo agéntico:  95.1% HumanEval
```

---

## Fase 1 — Plan

**Patrones: Planning + Tool Use**

- El agente descompone los requisitos usando **CoT o ReAct** (Thought → Action → Observation)
- Tool Use para buscar documentación existente, políticas, restricciones de negocio
- **RAG** con chunking Recursive 512 tokens (default seguro) para recuperar contexto previo → [[concepts/chunking]]
- Output esperado: requisitos estructurados, estimaciones, plan de fases

---

## Fase 2 — Design

**Patrones: Multi-Agent + Reflection**

- Agentes especializados por dominio: arquitectura, seguridad, UX, datos
- El agente de seguridad aplica **OWASP LLM Top 10** desde el diseño — no al final → [[concepts/devsecops]]
- Si el sistema procesa inputs externos: incluir **Dual LLM pattern** desde arquitectura para mitigar Prompt Injection → [[prompt-injection-architecture]]
- Verificación cruzada entre agentes para detectar inconsistencias antes de la implementación

---

## Fase 3 — Implement

**Patrones: Reflection + Tool Use**

- El agente genera código → autoevalúa → itera (patrón Reflection)
- **Reflexion** (Shinn et al.): 91% HumanEval con Verbal RL — guarda reflexiones textuales entre intentos → [[reflexion-paper]]
- Riesgo: **confirmation bias** — mismo modelo genera, evalúa y reflexiona
- Solución: **MAR (Multi-Agent Reflection)** — agente evaluador separado del agente actor

---

## Fase 4 — Test

**Patrones: Multi-Agent + Evaluación por Trayectoria**

- Evaluación por **trayectoria**, no zero-shot — los pasos intermedios importan tanto como el output → [[concepts/agent-evaluation]]
- **LangSmith** en producción: trajectory match + LLM judge para calidad de razonamiento
- Referencia de expectativas reales: el mejor modelo en BALROG alcanza solo **34.9%** en tareas de largo plazo → [[balrog]]
- No confiar en benchmarks zero-shot para predecir rendimiento agéntico

---

## Fase 5 — Deploy

**Framework: LangGraph**

- LangGraph para producción (~400 empresas): StateGraph, checkpointing, interrupts para HITL → [[concepts/langgraph]]
- CrewAI solo para prototipado rápido
- **Computer Use** como opción para automatizar testing en GUIs (Claude/Gemini) → [[concepts/computer-use]]
- HITL (Human-in-the-Loop) obligatorio para decisiones de alto impacto → mitiga LLM06 Excessive Agency

---

## Fase 6 — Maintain

**Patrones: Planning + Memory**

- **STM** (contexto de ventana): tareas de sesión corriente
- **LTM** (Vector DB + HNSW): conocimiento acumulado, incidentes previos, decisiones de arquitectura → [[concepts/agent-memory]]
- Pipeline RAG con Adaptive/Agentic RAG para recuperar contexto histórico relevante → [[concepts/rag]]

---

## Capa transversal: Seguridad Agéntica (DevSecOps)

| Riesgo | Origen | Mitigación |
|--------|--------|-----------|
| Prompt Injection (LLM01) | Cualquier input externo | Dual LLM + Constrained Decoding + Zero Trust |
| Excessive Agency (LLM06) | Herramientas con muchos permisos | HITL + permisos mínimos por agente |
| CVE autónomos | GPT-4 explotó 87% de 15 CVEs | Auditar herramientas disponibles al agente |
| Indirect Injection | Entorno ataca al agente sin intervención del usuario | Sanitizar toda observación de herramientas externas |

> Todas las defensas contra Prompt Injection son **probabilísticas**, no deterministas. No existe solución técnica equivalente a SQL injection. → [[concepts/prompt-injection]]

---

## Árbol de decisión: Framework y técnica

```
¿Es producción?
├── Sí → LangGraph (StateGraph + checkpointing + interrupts)
└── No → CrewAI (prototipado rápido con crews)

¿La tarea es multi-step?
├── Sí → Planning: ReAct (si hay herramientas) / CoT (si no)
└── No → Reflection (barato, alto ROI inicial)

¿El output puede mejorar con revisión?
├── Sí → Reflection → si confirmation bias importa → MAR
└── No → Tool Use directo
```

---

## Pregunta abierta pendiente

¿Cómo se integra LangSmith/evaluación en el CI/CD de sistemas agénticos? Es la brecha más relevante que el vault no tiene respondida para cerrar el ciclo SDLC completo con IA.

---

## Conexiones

- [[concepts/sdlc]] — fases del SDLC clásico
- [[concepts/agentic-design-patterns]] — los 4 patrones aplicados
- [[concepts/planning]] — técnicas de planning por fase
- [[concepts/llm-security]] — seguridad agéntica transversal
- [[concepts/agent-evaluation]] — evaluación por trayectoria
- [[concepts/multi-agent-frameworks]] — LangGraph vs CrewAI
- [[analyses/agentic-patterns-diagramas]] — diagramas Mermaid de los patrones
