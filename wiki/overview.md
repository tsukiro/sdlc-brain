---
title: "Overview"
type: overview
tags: [sdlc, agentes, rag, chunking, langgraph, crewai, gemini, planning, evaluación, seguridad, computer-use]
sources: [2026-05-13_ibm-sdlc.md, 2026-05-13_aws-sdlc.md, 2026-05-13_atlassian-sdlc.md, 2026-05-13_langgraph.md, 2026-05-13_crewai.md, 2026-05-13_gemini.md, 2026-05-13_chunking-rag.md, 2026-05-13_langchain-text-splitters.md, 2026-05-13_lilian-weng-agents.md, 2026-05-13_andrew-ng-agentic-patterns.md, 2026-05-13_balrog.md, 2026-05-13_langsmith-evaluation.md, 2026-05-13_reflexion-paper.md, 2026-05-13_llm-agents-vulnerabilities.md, 2026-05-13_owasp-llm-top10.md, 2026-05-13_computer-use-anthropic.md, 2026-05-13_owasp-llm01-prompt-injection.md, 2026-05-13_simon-willison-prompt-injection-2022.md, 2026-05-13_simon-willison-indirect-injection-2023.md, 2026-05-13_nemo-guardrails.md, 2026-05-13_learn-prompting-defensive-measures.md, arxiv-2307.09702, python.useinstructor.com, typechat-microsoft, jxnl.co]
updated: 2026-05-13
---

# Overview

> Síntesis de alto nivel del conocimiento acumulado. Actualizada con cada ingest significativo.

---

## Estado actual

- Fuentes procesadas: 58
- Páginas wiki: 87
- Último ingest: 2026-05-14 (Prompt Engineering Guide — Function Calling, DAIR-AI; 3º lote)

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
Plan→Design→Implement→Test→Deploy→Maintain. Con IA: cada fase tiene herramientas específicas; adopción recomendada incremental (piloto 30 días → evaluación → escala). Gartner: 75% de engineers usarán AI coding assistants para 2028. Ver [[concepts/sdlc]], [[concepts/waterfall]], [[concepts/agile]], [[snyk-ai-sdlc]].

### 2. Arquitectura de Agentes LLM
Planning + Memory + Tools (Weng) + Multi-Agent (Ng). Ver [[concepts/llm-agents]].

### 3. Los 4 Agentic Design Patterns
Reflection, Tool Use, Planning, Multi-Agent. Se componen. Ver [[concepts/agentic-design-patterns]].

### 3b. Prompt Engineering Fundamentos
Zero-shot (instruction tuning + RLHF) y Few-shot (Brown et al. 2020 — in-context learning). Hallazgo clave: el formato de los ejemplos importa más que las etiquetas correctas (Min et al. 2022). Son la base de todas las técnicas de reasoning. Ver [[concepts/prompt-engineering]], [[promptingguide-techniques]].

### 4. Técnicas de Planning
**11 técnicas documentadas**: CoT (Wei + Kojima 2022; "Let's think step by step"), Self-Consistency (Wang 2022; +10–20 pts sobre greedy), Generated Knowledge (Liu 2022), ToT (Yao 2023 + Long 2023; BFS/DFS), ReAct (Yao **2022**; mejor combo = ReAct + CoT + Self-Consistency), ART (Paranjape 2023; supera few-shot en BigBench + MMLU), **PAL** (Gao 2022; código Python como razonamiento → intérprete determinista), **APE** (Zhou 2022; encontró mejor zero-shot CoT que el humano), Reflexion (91% HumanEval, 130/134 AlfWorld; 3 componentes: Actor/Evaluator/Self-Reflection), LLM+P, CoH. Ver [[concepts/planning]].

### 5. Memoria en Agentes
STM (contexto) + LTM (vector DB = RAG). Ver [[concepts/agent-memory]].

### 6. RAG y Chunking
Pipeline completo. Chunking es el paso crítico (7 estrategias). Ver [[concepts/rag]], [[concepts/chunking]].

### 7. Frameworks de Orquestación
**LangChain** (framework base: models, tools, messages) + **LangGraph** (orquestación stateful, producción) + **CrewAI** (crews, prototipado). Ambos Python y JS/TypeScript. Ver [[concepts/langchain]], [[concepts/langgraph]], [[concepts/multi-agent-frameworks]].

**Regla práctica**: `create_react_agent` para agentes simples → `StateGraph` cuando se necesita control granular, estado persistente o HITL.

### 8. Evaluación de Agentes
Trajectory evaluation, no zero-shot. BALROG para horizonte largo. LangSmith para producción. Ver [[concepts/agent-evaluation]].

### 9. Seguridad LLM y Prompt Injection
OWASP Top 10 LLM + evidencia empírica de Fang et al. + deep dive en Prompt Injection (LLM01). Ver [[concepts/llm-security]], [[concepts/prompt-injection]].

### 10. Computer Use
Agentes que controlan GUIs. Anthropic (Claude) + Gemini 3. Ver [[concepts/computer-use]].

### 11. Gemini 3
LLM frontier. Flash: mejor costo/calidad para producción. Soporta `response_schema` para structured outputs nativos. Ver [[entities/gemini]].

### 12. Structured Generation (SDD como constrained decoding)
El paradigma que reemplaza prompt engineering frágil por contratos de datos garantizados. Dos niveles: Constrained Decoding en logits (Outlines) para modelos locales; validación + retry (Instructor/TypeChat) para APIs cloud. Aplicaciones: pipelines confiables, routing infalible en multi-agent, mitigación parcial de prompt injection. Ver [[concepts/structured-generation]].

### 13. SDD como Metodología de Desarrollo
Inversión de poder: la spec es el artefacto primario; el código es su expresión. Tres niveles de rigor: Spec-First (guía inicial), Spec-Anchored (sincronía mantenida con tests), Spec-as-Source (código generado, jamás editado). Workflow: Specify → Plan → Implement → Validate. Specs como "super-prompts" para AI agents: reduce errores en ~50%. Ver [[concepts/spec-driven-development]].

### 14. TOON — Token Economy
TOON (Token-Oriented Object Notation) reemplaza JSON en prompts LLM. Para arrays uniformes (logs, RAG chunks, catálogos), reduce tokens 40-60% con mayor accuracy de extracción. Estrategia: "JSON-In, TOON-Between, JSON-Out". Ver [[entities/toon]].

### 16. AI Framework como Artefacto Organizacional
El AI Framework encapsula el conocimiento de la organización (agents, skills, MCPs, prompts, rules, templates, examples) en un artefacto vivo que todos los tools AI consumen. No es un producto — se construye y mantiene internamente. **Efecto compuesto**: cada mejora beneficia todos los casos de uso inmediatamente. MCP (Model Context Protocol) es la capa de integración que conecta el framework con herramientas externas (Jira, Git, Nx, DBs). Claude Code es la implementación de referencia de este patrón. Ver [[gelin-ai-framework]].

### 15. LLM Wiki (Patrón Karpathy)
Alternativa a RAG para corpus que caben en el context window moderno: el LLM compila las fuentes una sola vez en un wiki markdown cross-referenciado; las queries cargan el wiki completo en contexto sin retrieval step. Caso MariaDB Labs (78 artículos / 265k palabras / ~1M tokens): latencia P95 de 1100ms → 180ms (6×) y costo de $0.15 → $0.03 / 1000 queries (5×). **Meta-nota:** este vault es la implementación viva del patrón. Ver [[concepts/llm-wiki]].

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
    │   ├── Planning (CoT / Self-Consistency / Generated Knowledge / ToT / ART / ReAct / Reflexion 91% HE / LLM+P)
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
    │   ├── LangChain (base: models, tools, messages — Python + JS)
    │   ├── LangGraph (grafos, producción, ~400 empresas — Python + JS)
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
    ├── LLMs
    │   └── Gemini 3 (Pro: #1 LMArena 1501 Elo; Flash: $0.50/1M)
    │
    ├── Structured Generation (constrained decoding)
    │   ├── Nivel logits: Outlines (FSM + logit masking, garantía matemática)
    │   ├── Nivel API: Instructor (Python/Pydantic) · TypeChat (TS/Microsoft)
    │   └── Soporte nativo: OpenAI · Anthropic · Gemini (response_schema)
    │
    ├── SDD — Metodología (Piskala 2025)
    │   ├── Espectro: Spec-First → Spec-Anchored → Spec-as-Source
    │   ├── Workflow: Specify → Plan → Implement → Validate
    │   └── Herramientas: BDD (Cucumber) · OpenAPI · Specmatic · Kiro · Tessl
    │
    ├── Token Economy
    │   └── TOON: 40-60% menos tokens que JSON para arrays uniformes
    │
    └── LLM Wiki (Patrón Karpathy)
        ├── Compile time: LLM sintetiza fuentes → wiki markdown
        ├── Query time: wiki completo en contexto (sin retrieval)
        └── MariaDB Labs: 6× latencia, 5× costo vs RAG
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

## Tensiones nuevas (ingest AI-SDLC)

16. **Gobernanza vs. velocidad de adopción de IA**: la adopción fragmentada de IA (vibe coding, tools distintos por equipo, sin MCP centralizado) crea deuda técnica y riesgo de seguridad. La gobernanza (MCP broker, prompt library, standardized skills) es condición necesaria para escalar pero introduce fricción inicial. Sin gobernanza, la IA ralentiza en lugar de acelerar.
17. **SDD overhead vs. valor para tareas pequeñas**: SDD aporta valor claro en features complejas con múltiples agentes; es overkill para tareas simples. Las best practices de cuándo usar SDD no están consolidadas aún (requiere trial-and-error por equipo).
18. **PRs generados por IA como bottleneck de review**: velocidad alta de generación → PRs masivos difíciles de revisar. Solución: mantener PRs pequeños + agentes de review especializados. El humano valida intención de negocio; los agentes validan calidad técnica.

## Tensiones nuevas (ingest SDD metodología + TOON)

13. **SDD-metodología vs SDD-constrained decoding**: el término "SDD" se usa para dos cosas distintas. En este vault: [[concepts/spec-driven-development]] = workflow de desarrollo; [[concepts/structured-generation]] = constrained decoding de outputs LLM. No confundir.
14. **Spec-Anchored overhead vs code-first velocidad**: mantener specs sincronizadas con código requiere disciplina real. Sin CI enforcement, degenera en documentación tradicional con drift.
15. **TOON latencia vs ahorro de tokens**: para inferencia local con vLLM/Ollama, JSON tiene aceleración C++ que puede ganar en latencia aunque TOON use menos tokens. Siempre medir TTFT antes de migrar.

## Tensiones nuevas (ingest SDD)

11. **Instructor (retry) vs. Outlines (logit masking)**: Instructor con APIs cloud no puede garantizar matemáticamente el schema si el proveedor no soporta constrained decoding nativo. Es validación probabilística con reintentos, no garantía formal.
12. **Simple tools win (Liu) vs. arquitecturas multi-agent (LangGraph/CrewAI)**: Liu argumenta que multi-agent introduce ruido por "telephone game effect"; LangGraph y CrewAI están diseñados precisamente para multi-agent. La tensión se resuelve con SDD: si cada hop del multi-agent usa outputs estructurados, el ruido se reduce dramáticamente.

---

## Preguntas abiertas (actualizadas)

- **[PARCIALMENTE RESPONDIDA]** ¿Cómo implementar un Centralized MCP Broker? → MCP conecta AI agents con herramientas externas (Jira, Git, Nx, DBs) vía Model Context Protocol. La gobernanza requiere un broker centralizado que solo exponga servicios aprobados. Claude Code es la implementación de referencia con el sistema de plugins/MCP. Ver [[gelin-ai-framework]] y [[isenberg-ai-sdlc]]. Gap pendiente: no hay página de entidad para MCP en el wiki.
- ¿Cómo se integra LangSmith/evaluación en el CI/CD de sistemas agénticos?
- ¿Cuándo justifica el overhead de Reflexion (múltiples intentos) el gain de calidad?
- ¿Cuál es el trade-off óptimo entre autonomía del agente y HITL para diferentes tipos de tarea?
- ¿Cómo se implementa DevSecOps para pipelines agénticos con herramientas dinámicas?
- ¿Qué tan aplicable es el hallazgo de Computer Use de Anthropic a otros contextos (automatización de testing, CI/CD)?
- ¿Cuándo los VLMs superarán a los LLMs de texto en toma de decisiones agéntica? (BALROG muestra que aún no)
- **[RESPONDIDA]** ¿Existe solución arquitectural para prompt injection? → No al 100% (Von Neumann gap). Las soluciones robustas son Constrained Decoding + Dual LLM + Zero Trust, no prompt engineering. Ver [[prompt-injection-architecture]].
- **[RESPONDIDA]** ¿Cuándo están los Control Vectors listos para producción? → Ya están en producción. Anthropic publicó **Persona Vectors** (arXiv 2507.21509, agosto 2025) — aplicación directa del concepto en un frontier model. Apple usa *Activation Transport* (variante con magnitud calculada por transporte óptimo). Para modelos open-source propios: GCAV (Zhang et al., ene-2025) — 51% reducción de toxicidad en Llama-2-7B. **Limitación persistente**: requiere acceso a activaciones internas; no aplicable a APIs opacas (OpenAI, Anthropic Cloud). Ver [[gcav-paper]], [[calcagni-control-vectors]].
- ¿Cómo escala NeMo Guardrails en sistemas de alta latencia? ¿Cuándo el costo de los rails supera el beneficio de seguridad?
- ¿Cómo auditar un sistema agéntico multi-tool para detectar vectores de indirect injection antes del deployment?
