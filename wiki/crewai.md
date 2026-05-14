---
title: "CrewAI — Open Source"
type: source-summary
tags: [crewai, agentes, multi-agent, crews, tasks, memoria, rag]
sources: [2026-05-13_crewai.md]
updated: 2026-05-13
---

# CrewAI — Open Source

**Fuente:** https://crewai.com/open-source

## Perspectiva de esta fuente

Sitio oficial de CrewAI. Énfasis en accesibilidad, velocidad de desarrollo y abstracción de alto nivel. El framing es "gestionar un equipo de IA", no "diseñar una máquina de estados".

## Qué es CrewAI

Framework open-source para orquestar **equipos colaborativos de agentes de IA** (crews). La metáfora es un equipo humano: cada agente tiene un rol, objetivo e historial; se les asignan tareas; colaboran bajo la dirección de un manager opcional.

## Los tres conceptos núcleo

### Agents
Entidades configurables con: `role`, `goal`, `backstory`. El desarrollador elige el LLM, habilita razonamiento/memoria, asigna herramientas. Definibles en YAML (declarativo) o código.

### Tasks
Unidades de trabajo con descripción y salida esperada (markdown, JSON o Pydantic object). Soportan: guardrails, human-in-the-loop (HITL), dependencias de otras tasks.

### Crews
El equipo completo: agentes + tareas. Configurable con manager agent, método de colaboración y planificación previa a ejecución.

## Capacidades diferenciadores

**Memoria sofisticada**:
- Corto plazo (compartida entre agentes del run)
- Largo plazo (persistente entre runs)
- Entidades (knowledge graph)
- Contextual (resúmenes de interacciones pasadas)

**Agentic RAG (Knowledge)**: Integra fuentes de conocimiento (archivos, web, vector DBs) con reescritura inteligente de consultas. No requiere pipeline RAG externo.

**Planning**: Un agente gestor genera un plan completo antes de ejecutar, luego orquesta.

**Razonamiento**: Agentes reflexionan sobre objetivos y refinan planes durante la ejecución.

## Flujo de desarrollo

```python
# 1. Definir agentes
researcher = Agent(role="Researcher", goal="Find info", llm=...)
writer = Agent(role="Writer", goal="Write report", llm=...)

# 2. Definir tareas
research_task = Task(description="Research X", expected_output="...")
write_task = Task(description="Write about X", expected_output="...")

# 3. Crear y ejecutar crew
crew = Crew(agents=[researcher, writer], tasks=[research_task, write_task])
result = crew.kickoff()
```

## Tiers del producto

| Tier | Qué ofrece |
|------|-----------|
| **OSS** | Framework gratuito, comunidad activa |
| **AMP Cloud** | SaaS: editor visual, despliegue gestionado |
| **AMP Factory** | Enterprise: on-prem o VPC privada |

## Cuándo usar CrewAI vs LangGraph

> CrewAI: prototipado rápido (~10 min a primer flow), abstracción accesible, workflows tipo "team collaboration".  
> LangGraph: producción, state management avanzado, control fino de routing y persistencia. Ver [[concepts/multi-agent-frameworks]].

## Conexiones

- [[concepts/multi-agent-frameworks]] — comparativa principal
- [[langgraph]] — framework alternativo
- [[concepts/rag]] — Agentic RAG integrado en CrewAI
- [[concepts/chunking]] — relevante para el Knowledge component
