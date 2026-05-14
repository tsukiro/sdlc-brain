---
title: "Overview"
type: overview
tags: [sdlc, agentes, rag, chunking, langgraph, crewai, gemini, planning, evaluación, seguridad, computer-use]
sources: [2026-05-13_ibm-sdlc.md, 2026-05-13_aws-sdlc.md, 2026-05-13_atlassian-sdlc.md, 2026-05-13_langgraph.md, 2026-05-13_crewai.md, 2026-05-13_gemini.md, 2026-05-13_chunking-rag.md, 2026-05-13_langchain-text-splitters.md, 2026-05-13_lilian-weng-agents.md, 2026-05-13_andrew-ng-agentic-patterns.md, 2026-05-13_balrog.md, 2026-05-13_langsmith-evaluation.md, 2026-05-13_reflexion-paper.md, 2026-05-13_llm-agents-vulnerabilities.md, 2026-05-13_owasp-llm-top10.md, 2026-05-13_computer-use-anthropic.md, 2026-05-13_owasp-llm01-prompt-injection.md, 2026-05-13_simon-willison-prompt-injection-2022.md, 2026-05-13_simon-willison-indirect-injection-2023.md, 2026-05-13_nemo-guardrails.md, 2026-05-13_learn-prompting-defensive-measures.md]
updated: 2026-05-13
---

# Overview

> Síntesis de alto nivel del conocimiento acumulado. Actualizada con cada ingest significativo.

---

## Estado actual

- Fuentes procesadas: 22
- Páginas wiki: 51
- Último ingest: 2026-05-13 (Prompt Injection — Irreducibilidad Arquitectural: Von Neumann, Constrained Decoding, Dual LLM, Control Vectors)

---

## Tesis central

Este vault cubre la intersección entre el **SDLC clásico** y los **sistemas de IA agéntica modernos**. La tesis que emerge después de 16 fuentes:

> Los agentes LLM están redefiniendo tres dimensiones del SDLC: cómo se *construye* software (frameworks agénticos + RAG), cómo se *evalúa* (trajectory evaluation, no zero-shot), y cómo se *asegura* (OWASP LLM Top 10, nuevas superficies de ataque).

Los tres datos que anclan esta tesis:
1. **Rendimiento**: GPT-3.5 + agente iterativo → 95.1% HumanEval > GPT-4 zero-shot 67% (Ng). La arquitectura importa más que el modelo.
2. **Evaluación**: el mejor modelo en BALROG alcanza solo 34.9% de progreso en tareas de largo plazo. Los benchmarks zero-shot sobreestiman capacidad agéntica.
3. **Seguridad**: GPT-4 explotó 87% de 15 CVEs críticos autónomamente (Fang et al.). Los agentes son atacantes de nueva generación.

---

## Temas principales

### 1. SDLC Fundamentos
Plan→Design→Implement→Test→Deploy→Maintain. Ver [[concepts/sdlc]], [[concepts/waterfall]], [[concepts/agile]].

### 2. Arquitectura de Agentes LLM
Planning + Memory + Tools (Weng) + Multi-Agent (Ng). Ver [[concepts/llm-agents]].

### 3. Los 4 Agentic Design Patterns
Reflection, Tool Use, Planning, Multi-Agent. Se componen. Ver [[concepts/agentic-design-patterns]].

### 4. Técnicas de Planning
CoT, ToT, ReAct, Reflexion (91% HumanEval), LLM+P. Ver [[concepts/planning]].

### 5. Memoria en Agentes
STM (contexto) + LTM (vector DB = RAG). Ver [[concepts/agent-memory]].

### 6. RAG y Chunking
Pipeline completo. Chunking es el paso crítico (7 estrategias). Ver [[concepts/rag]], [[concepts/chunking]].

### 7. Frameworks de Orquestación
LangGraph (producción) vs. CrewAI (prototipado). Ver [[concepts/multi-agent-frameworks]].

### 8. Evaluación de Agentes
Trajectory evaluation, no zero-shot. BALROG para horizonte largo. LangSmith para producción. Ver [[concepts/agent-evaluation]].

### 9. Seguridad LLM y Prompt Injection
OWASP Top 10 LLM + evidencia empírica de Fang et al. + deep dive en Prompt Injection (LLM01). Ver [[concepts/llm-security]], [[concepts/prompt-injection]].

### 10. Computer Use
Agentes que controlan GUIs. Anthropic (Claude) + Gemini 3. Ver [[concepts/computer-use]].

### 11. Gemini 3
LLM frontier. Flash: mejor costo/calidad para producción. Ver [[entities/gemini]].

---

## Mapa de conceptos completo

```
SDLC
├── Proceso clásico
│   ├── Fases (Plan→Design→Implement→Test→Deploy→Maintain)
│   ├── Modelos (Waterfall, V-Model, Agile, Iterativo, Spiral)
│   └── DevSecOps → LLM Security (nueva capa necesaria)
│
└── Agentic AI
    ├── Arquitectura del Agente
    │   ├── Planning (CoT / ToT / ReAct / Reflexion 91% HE / LLM+P)
    │   ├── Memory (STM = contexto / LTM = Vector DB + ANN = RAG)
    │   └── Tool Use (Function Calling / Computer Use)
    │
    ├── Design Patterns (Ng)
    │   ├── Reflection
    │   ├── Tool Use
    │   ├── Planning
    │   └── Multi-Agent
    │
    ├── Frameworks
    │   ├── LangGraph (grafos, producción, ~400 empresas)
    │   └── CrewAI (crews, prototipado, Agentic RAG)
    │
    ├── RAG Pipeline
    │   ├── Chunking (7 estrategias; default: Recursive 512t)
    │   └── Retrieval (ANN: HNSW estándar)
    │
    ├── Evaluación
    │   ├── BALROG (horizonte largo: max 34.9%)
    │   └── LangSmith (trajectory match / LLM judge)
    │
    ├── Seguridad
    │   ├── OWASP LLM Top 10 (LLM01 Prompt Injection, LLM06 Excessive Agency...)
    │   └── Fang et al. (GPT-4: 87% CVEs autónomamente)
    │
    ├── Computer Use
    │   ├── Claude (Anthropic) — beta, WebArena SOTA
    │   └── Gemini 3 Pro/Flash — preview
    │
    └── LLMs
        └── Gemini 3 (Pro: #1 LMArena 1501 Elo; Flash: $0.50/1M)
```

---

## Tensiones y contradicciones

1. **Fases del SDLC**: AWS (6) vs. Atlassian (7) — granularidad, no contradicción
2. **Overlap en chunking**: estándar (10-20%) vs. análisis ene-2026 que lo cuestiona
3. **Semantic chunking**: mayor recall pero costos potencialmente injustificados
4. **Reflection como patrón vs. técnica**: Ng (patrón) vs. Weng (subcomponente de Planning)
5. **Autoevaluación sesgada**: LLMs son malos evaluando dominios especializados (ChemCrow)
6. **Reflexion — confirmation bias**: mismo modelo que actúa, evalúa y reflexiona → solución: MAR
7. **VLMs en BALROG**: modelos de visión rinden **peor** con input visual en tareas agénticas
8. **Zero-shot vs. agéntico**: GPT-3.5+agente > GPT-4 zero-shot; pero modelos avanzados en BALROG apenas llegan al 35%
9. **Prompt Injection sin solución técnica**: SQL injection tiene solución (queries parametrizadas); prompt injection no — los LLMs no pueden distinguir arquitecturalmente instrucciones de datos
10. **Mitigación vs. prevención**: todas las defensas contra prompt injection son probabilísticas, no deterministas

---

## Preguntas abiertas (actualizadas)

- ¿Cómo se integra LangSmith/evaluación en el CI/CD de sistemas agénticos?
- ¿Cuándo justifica el overhead de Reflexion (múltiples intentos) el gain de calidad?
- ¿Cuál es el trade-off óptimo entre autonomía del agente y HITL para diferentes tipos de tarea?
- ¿Cómo se implementa DevSecOps para pipelines agénticos con herramientas dinámicas?
- ¿Qué tan aplicable es el hallazgo de Computer Use de Anthropic a otros contextos (automatización de testing, CI/CD)?
- ¿Cuándo los VLMs superarán a los LLMs de texto en toma de decisiones agéntica? (BALROG muestra que aún no)
- **[RESPONDIDA]** ¿Existe solución arquitectural para prompt injection? → No al 100% (Von Neumann gap). Las soluciones robustas son Constrained Decoding + Dual LLM + Zero Trust, no prompt engineering. Ver [[prompt-injection-architecture]].
- ¿Cuándo están los Control Vectors listos para producción? (2024-2025: investigación activa)
- ¿Cómo escala NeMo Guardrails en sistemas de alta latencia? ¿Cuándo el costo de los rails supera el beneficio de seguridad?
- ¿Cómo auditar un sistema agéntico multi-tool para detectar vectores de indirect injection antes del deployment?
