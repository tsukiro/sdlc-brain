---
title: "Overview"
type: overview
tags: [sdlc, agentes, rag, chunking, langgraph, crewai, gemini, planning, evaluación, seguridad, computer-use, prompt-engineering, structured-generation, sdd, control-vectors]
sources: []
updated: 2026-05-14
---

# Overview

> Síntesis de alto nivel del conocimiento acumulado. Actualizada con cada ingest significativo.

---

## Estado actual

- Fuentes procesadas: 64
- Páginas wiki: 92
- Último ingest: 2026-05-14 (Prompt Engineering Guide — repositorio completo DAIR-AI)

---

## Tesis central

Este vault cubre la intersección entre el **SDLC clásico** y los **sistemas de IA agéntica modernos**. La tesis que emerge después de 64 fuentes:

> Los agentes LLM están redefiniendo tres dimensiones del SDLC: cómo se *construye* software (frameworks agénticos + RAG + SDD), cómo se *evalúa* (trajectory evaluation, no zero-shot), y cómo se *asegura* (OWASP LLM Top 10, nuevas superficies de ataque sin solución determinista).

Los cuatro datos que anclan esta tesis:
1. **Rendimiento**: GPT-3.5 + agente iterativo → 95.1% HumanEval > GPT-4 zero-shot 67% (Ng). La arquitectura importa más que el modelo.
2. **Evaluación**: el mejor modelo en BALROG alcanza solo 34.9% de progreso en tareas de largo plazo. Los benchmarks zero-shot sobreestiman capacidad agéntica.
3. **Seguridad**: GPT-4 explotó 87% de 15 CVEs críticos autónomamente (Fang et al.). Los agentes son atacantes de nueva generación.
4. **Gobernanza**: sin AI Framework centralizado (skills, MCPs, prompt library), la adopción fragmentada de IA ralentiza en lugar de acelerar (Gelin, Isenberg).

---

## Temas principales

### 1. SDLC Fundamentos
Plan→Design→Implement→Test→Deploy→Maintain. Con IA: cada fase tiene herramientas específicas; adopción recomendada incremental (piloto 30 días → evaluación → escala). Gartner: 75% de engineers usarán AI coding assistants para 2028. Ver [[concepts/sdlc]], [[concepts/waterfall]], [[concepts/agile]], [[snyk-ai-sdlc]].

### 2. AI Framework como Artefacto Organizacional
El AI Framework encapsula el conocimiento de la organización (agents, skills, MCPs, prompts, rules, templates, examples) en un artefacto vivo que todos los tools AI consumen. **Efecto compuesto**: cada mejora beneficia todos los casos de uso inmediatamente. MCP (Model Context Protocol) es la capa de integración con herramientas externas (Jira, Git, Nx, DBs). Claude Code es la implementación de referencia. Ver [[gelin-ai-framework]], [[isenberg-ai-sdlc]].

### 3. Arquitectura de Agentes LLM
Planning + Memory + Tools (Weng) + Multi-Agent (Ng). Ver [[concepts/llm-agents]].

### 4. Los 4 Agentic Design Patterns
Reflection, Tool Use, Planning, Multi-Agent. Se componen entre sí. Ver [[concepts/agentic-design-patterns]].

### 5. Prompt Engineering Fundamentos
Zero-shot (instruction tuning + RLHF) y Few-shot (Brown et al. 2020 — in-context learning). Hallazgo clave: el formato de los ejemplos importa más que las etiquetas correctas (Min et al. 2022). Los **4 elementos de un prompt**: Instruction, Context, Input Data, Output Indicator. **Tip crítico (Do vs. Don't):** las prohibiciones en el prompt frecuentemente producen el comportamiento prohibido — reemplazar siempre con instrucciones afirmativas. Son la base de todas las técnicas de reasoning. Ver [[concepts/prompt-engineering]], [[promptingguide-techniques]], [[promptingguide-introduction]].

### 6. LLM Settings — Parámetros de Generación
Los parámetros controlan el comportamiento de generación tan decisivamente como el prompt. **Reglas críticas:** ajustar Temperature OR Top P (nunca ambos); Frequency OR Presence Penalty (nunca ambos). **Distinción clave:** Frequency Penalty acumula penalidad proporcionalmente a cada aparición; Presence Penalty es binaria (una aparición = penalidad máxima, sin importar cuántas veces más repita). Ver [[concepts/llm-settings]], [[promptingguide-introduction]].

### 7. Técnicas de Planning
**11 técnicas documentadas**: CoT (Wei + Kojima 2022; "Let's think step by step"), Self-Consistency (Wang 2022; +10–20 pts sobre greedy), Generated Knowledge (Liu 2022), ToT (Yao 2023 + Long 2023; BFS/DFS), ReAct (Yao **2022**; mejor combo = ReAct + CoT + Self-Consistency), ART (Paranjape 2023; supera few-shot en BigBench + MMLU), **PAL** (Gao 2022; código Python como razonamiento → intérprete determinista), **APE** (Zhou 2022; encontró mejor zero-shot CoT que el humano: "Let's work this out in a step by step way to be sure we have the right answer"), Reflexion (91% HumanEval, 130/134 AlfWorld; 3 componentes: Actor/Evaluator/Self-Reflection), LLM+P, CoH. Ver [[concepts/planning]].

### 8. Memoria en Agentes
STM (contexto de sesión) + LTM (vector DB = RAG). Algoritmos ANN: LSH, ANNOY, HNSW (estándar), FAISS, ScaNN. Ver [[concepts/agent-memory]].

### 9. RAG y Chunking
Pipeline completo: Load → Split → Embed → Store → Retrieve → Generate. Chunking es el paso más crítico (7 estrategias; default recomendado: `RecursiveCharacterTextSplitter` a 400–512 tokens). **Técnica de bootstrapping**: Synthetic RAG Dataset (Dai et al. 2022) — 8 ejemplos manuales + corpus no etiquetado → near-SOTA a ~$55 por 50,000 documentos. Ver [[concepts/rag]], [[concepts/chunking]].

### 10. Frameworks de Orquestación
**LangChain** (framework base: models, tools, messages — Python + JS) + **LangGraph** (orquestación stateful, grafos, producción, ~400 empresas — Python + JS) + **CrewAI** (crews, prototipado, Agentic RAG integrado).

**Regla práctica**: `create_react_agent` para agentes simples → `StateGraph` cuando se necesita control granular, estado persistente o HITL. Ver [[concepts/langchain]], [[concepts/langgraph]], [[concepts/multi-agent-frameworks]].

### 11. Evaluación de Agentes
Trajectory evaluation, no zero-shot. BALROG para horizonte largo (benchmark en juegos: BabyAI/Crafter/TextWorld). LangSmith para producción (trajectory match, LLM judge, evaluación de pasos intermedios). Ver [[concepts/agent-evaluation]].

### 12. Seguridad LLM y Prompt Injection
OWASP Top 10 LLM 2025 + evidencia empírica Fang et al. + deep dive en Prompt Injection (LLM01). **Hallazgo arquitectural clave**: Prompt Injection no tiene solución determinista (Von Neumann gap — los Transformers no separan instrucciones de datos). Defensa por capas: Constrained Decoding + Dual LLM + Zero Trust. **Adversarial Prompting**: DAN (role-play como IA sin restricciones), Waluigi Effect (RLHF codifica comportamientos opuestos), Prompt Leaking. Ver [[concepts/llm-security]], [[concepts/prompt-injection]], [[prompt-injection-architecture]].

### 13. Control Vectors — Steering de Activaciones
Técnica de steering que opera directamente sobre las activaciones internas del LLM durante la inferencia, sin modificar los pesos. **GCAV** (Zhang et al. 2025): entrena CAV con ~100 pares contrastivos → 51% reducción de toxicidad en Llama-2-7B. **Anthropic Persona Vectors** (arXiv 2507.21509, ago-2025): control vectors en producción en un frontier model. **Limitación crítica**: requiere acceso a activaciones internas — solo modelos open-source on-premise; no aplicable a APIs opacas. Ver [[gcav-paper]], [[calcagni-control-vectors]], [[prompt-injection-architecture]].

### 14. Computer Use
Agentes que controlan GUIs mediante screenshots y acciones (click, type, scroll). Anthropic (Claude) — beta, WebArena SOTA. Gemini 3 Pro/Flash — preview. Amplifica LLM01 + LLM06 (Excessive Agency). Ver [[concepts/computer-use]], [[computer-use]].

### 15. Modelos LLM de Referencia
**Gemini 3** (Pro: #1 LMArena 1501 Elo; Flash: $0.50/1M tokens, mejor costo/calidad para producción; `response_schema` para structured outputs nativos; Context Caching via KV states). **ChatGPT** (RLHF; Role Prompting: Intent + Identity; gpt-3.5-turbo 90% más barato que text-davinci-003). **GPT-4** (128K tokens; JSON Mode; `seed` para reproducibilidad; `system_fingerprint`; system messages resisten user overrides). **LLaMA** (Meta; optimiza inference budget sobre training budget — desafía Chinchilla; LLaMA-13B supera GPT-3-175B en 1 sola GPU). **Mistral 7B** (GQA+SWA; supera todos los modelos 13B; 14-categorías de content moderation self-hosted; Apache 2.0). Ver [[entities/gemini]], [[promptingguide-models]].

### 16. Structured Generation (constrained decoding)
El paradigma que reemplaza prompt engineering frágil por contratos de datos garantizados. Dos niveles: Constrained Decoding en logits (Outlines: FSM + logit masking, garantía matemática) para modelos locales; validación + retry (Instructor/TypeChat) para APIs cloud. Aplicaciones: pipelines confiables, routing infalible en multi-agent, mitigación parcial de prompt injection. Ver [[concepts/structured-generation]].

### 17. SDD como Metodología de Desarrollo
Inversión de poder: la spec es el artefacto primario; el código es su expresión. Tres niveles de rigor: Spec-First (guía inicial), Spec-Anchored (sincronía mantenida con tests), Spec-as-Source (código generado, jamás editado). Workflow: Specify → Plan → Implement → Validate. Specs como "super-prompts" para AI agents: reduce errores en ~50%. Ver [[concepts/spec-driven-development]].

### 18. Skills — Empaquetado de Comportamiento AI
Skills como "npm de AI agents": YAML frontmatter + markdown instructions + resources opcionales + triggers. Arquitectura de 3 niveles: metadata (siempre cargada), instructions (on trigger), resources (on demand) → solución al context window sin limitar profundidad. Convergencia cross-platform: OpenClaw, Claude Agent Skills, Cursor Rules. ClawHub como registry (análogo a npm). Precursor conceptual: Prompt Functions (DAIR-AI, 2023). Ver [[gelin-skills-format]].

### 19. TOON — Token Economy
TOON (Token-Oriented Object Notation) reemplaza JSON en prompts LLM para datos tabulares. Para arrays uniformes (logs, RAG chunks, catálogos), reduce tokens 40–60% con mayor accuracy de extracción (70% → 74%). Estrategia: "JSON-In, TOON-Between, JSON-Out". Ver [[entities/toon]], [[toon-format]].

### 20. LLM Wiki (Patrón Karpathy)
Alternativa a RAG para corpus que caben en el context window moderno: el LLM compila las fuentes una sola vez en un wiki markdown cross-referenciado; las queries cargan el wiki completo en contexto sin retrieval step. Caso MariaDB Labs (78 artículos / 265k palabras / ~1M tokens): latencia P95 1100ms → 180ms (6×), costo $0.15 → $0.03 / 1000 queries (5×). **Meta-nota:** este vault es la implementación viva del patrón. Ver [[concepts/llm-wiki]], [[llm-wiki-karpathy]].

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
    ├── Prompt Engineering
    │   ├── Técnicas base (Zero-shot / Few-shot / ICL)
    │   ├── Elementos del prompt (Instruction/Context/Input/Output Indicator)
    │   ├── LLM Settings (Temperature · Top P · Freq/Presence Penalty · Stop Seq)
    │   └── Técnicas avanzadas → Planning
    │
    ├── Arquitectura del Agente
    │   ├── Planning (CoT / Self-Consistency / Generated Knowledge / ToT /
    │   │            PAL / APE / ART / ReAct / Reflexion 91% HE / LLM+P)
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
    │   ├── Chunking (7 estrategias; default: Recursive 400-512t)
    │   ├── Retrieval (ANN: HNSW estándar · MMR · similarity thresholding)
    │   └── Synthetic Datasets (Dai et al. 2022: 8 ejemplos → near-SOTA)
    │
    ├── Evaluación
    │   ├── BALROG (horizonte largo: max 34.9%)
    │   └── LangSmith (trajectory match / LLM judge)
    │
    ├── Seguridad
    │   ├── OWASP LLM Top 10 (LLM01 Prompt Injection, LLM06 Excessive Agency)
    │   ├── Fang et al. (GPT-4: 87% CVEs autónomamente)
    │   ├── Adversarial Prompting (DAN / Waluigi Effect / Prompt Leaking)
    │   └── Control Vectors (GCAV · Anthropic Persona Vectors ago-2025)
    │
    ├── Computer Use
    │   ├── Claude (Anthropic) — beta, WebArena SOTA
    │   └── Gemini 3 Pro/Flash — preview
    │
    ├── LLMs
    │   ├── Gemini 3 (Pro: #1 LMArena 1501 Elo; Flash: $0.50/1M)
    │   ├── GPT-4 (128K ctx; JSON Mode; seed; system_fingerprint)
    │   ├── LLaMA (Meta; 13B > GPT-3-175B en 1 GPU; inference budget)
    │   └── Mistral 7B (GQA+SWA; Apache 2.0; 14-cat moderation)
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
    ├── AI Framework Organizacional (Gelin · Isenberg)
    │   ├── Componentes: Agents · Skills · MCPs · Prompts · Rules · Examples
    │   ├── Skills: YAML+markdown · progressive loading · ClawHub registry
    │   └── MCP: integración con Jira · Git · Nx · DBs
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
2. **Overlap en chunking**: estándar (10–20%) vs. análisis ene-2026 que lo cuestiona
3. **Semantic chunking**: mayor recall pero costos potencialmente injustificados
4. **Reflection como patrón vs. técnica**: Ng (patrón) vs. Weng (subcomponente de Planning)
5. **Autoevaluación sesgada**: LLMs son malos evaluando dominios especializados (ChemCrow)
6. **Reflexion — confirmation bias**: mismo modelo que actúa, evalúa y reflexiona → solución: MAR (Multi-Agent Reflexion)
7. **VLMs en BALROG**: modelos de visión rinden **peor** con input visual en tareas agénticas
8. **Zero-shot vs. agéntico**: GPT-3.5+agente > GPT-4 zero-shot; pero modelos avanzados en BALROG apenas llegan al 35%
9. **Prompt Injection sin solución técnica**: SQL injection tiene solución (queries parametrizadas); prompt injection no — los LLMs no pueden distinguir arquitecturalmente instrucciones de datos
10. **Mitigación vs. prevención**: todas las defensas contra prompt injection son probabilísticas, no deterministas
11. **Instructor (retry) vs. Outlines (logit masking)**: Instructor con APIs cloud no puede garantizar matemáticamente el schema si el proveedor no soporta constrained decoding nativo. Es validación probabilística con reintentos, no garantía formal.
12. **Simple tools win (Liu) vs. arquitecturas multi-agent**: Liu argumenta que multi-agent introduce ruido por "telephone game effect"; LangGraph y CrewAI están diseñados precisamente para multi-agent. Resolución: SDD — si cada hop usa outputs estructurados, el ruido se reduce dramáticamente.
13. **SDD-metodología vs SDD-constrained decoding**: el término "SDD" se usa para dos cosas distintas. En este vault: [[concepts/spec-driven-development]] = workflow de desarrollo; [[concepts/structured-generation]] = constrained decoding de outputs LLM. No confundir.
14. **Spec-Anchored overhead vs code-first velocidad**: mantener specs sincronizadas con código requiere disciplina real. Sin CI enforcement, degenera en documentación tradicional con drift.
15. **TOON latencia vs ahorro de tokens**: para inferencia local con vLLM/Ollama, JSON tiene aceleración C++ que puede ganar en latencia aunque TOON use menos tokens. Siempre medir TTFT antes de migrar.
16. **Gobernanza vs. velocidad de adopción de IA**: la adopción fragmentada (vibe coding, tools distintos por equipo, sin MCP centralizado) crea deuda técnica y riesgo de seguridad. La gobernanza es condición necesaria para escalar pero introduce fricción inicial. Sin gobernanza, la IA ralentiza en lugar de acelerar.
17. **SDD overhead vs. valor para tareas pequeñas**: SDD aporta valor claro en features complejas con múltiples agentes; es overkill para tareas simples. Las best practices de cuándo usar SDD no están consolidadas (requiere trial-and-error por equipo).
18. **PRs generados por IA como bottleneck de review**: velocidad alta de generación → PRs masivos difíciles de revisar. Solución: mantener PRs pequeños + agentes de review especializados. El humano valida intención de negocio; los agentes validan calidad técnica.
19. **CoT few-shot peor que zero-shot en clasificación**: Clavié et al. 2023 demuestra que CoT few-shot *empeora* el rendimiento en tareas de clasificación que no requieren razonamiento experto. Contradicción con la aplicación universal de CoT. Resolución: CoT es técnica de razonamiento, no mejora universal — agrega valor solo cuando hay descomposición de razonamiento complejo genuina.
20. **Waluigi Effect — consecuencia estructural de RLHF**: El entrenamiento RLHF que enseña comportamientos "alineados" codifica implícitamente sus opuestos en el espacio de representaciones. Los modelos son inherentemente susceptibles a roleplay adversarial. No es un bug patcheable — es consecuencia estructural del RLHF. Amplifica las tensiones 9–10.

---

## Preguntas abiertas

- **[PARCIALMENTE RESPONDIDA]** ¿Cómo implementar un Centralized MCP Broker? → MCP conecta AI agents con herramientas externas (Jira, Git, Nx, DBs) vía Model Context Protocol. La gobernanza requiere un broker centralizado que solo exponga servicios aprobados. Claude Code es la implementación de referencia. Ver [[gelin-ai-framework]], [[isenberg-ai-sdlc]]. Gap pendiente: no hay página de entidad MCP en el wiki.
- **[RESPONDIDA]** ¿Existe solución arquitectural para prompt injection? → No al 100% (Von Neumann gap). Las soluciones robustas son Constrained Decoding + Dual LLM + Zero Trust, no prompt engineering. Ver [[prompt-injection-architecture]].
- **[RESPONDIDA]** ¿Cuándo están los Control Vectors listos para producción? → Ya en producción. Anthropic publicó **Persona Vectors** (arXiv 2507.21509, agosto 2025). GCAV (Zhang et al. 2025): 51% reducción de toxicidad en Llama-2-7B. **Limitación persistente**: requiere acceso a activaciones — no aplicable a APIs opacas. Ver [[gcav-paper]], [[calcagni-control-vectors]].
- ¿Cómo se integra LangSmith/evaluación en el CI/CD de sistemas agénticos?
- ¿Cuándo justifica el overhead de Reflexion (múltiples intentos) el gain de calidad?
- ¿Cuál es el trade-off óptimo entre autonomía del agente y HITL para diferentes tipos de tarea?
- ¿Cómo se implementa DevSecOps para pipelines agénticos con herramientas dinámicas?
- ¿Qué tan aplicable es el hallazgo de Computer Use de Anthropic a otros contextos (automatización de testing, CI/CD)?
- ¿Cuándo los VLMs superarán a los LLMs de texto en toma de decisiones agéntica? (BALROG muestra que aún no)
- ¿Cómo escala NeMo Guardrails en sistemas de alta latencia? ¿Cuándo el costo de los rails supera el beneficio de seguridad?
- ¿Cómo auditar un sistema agéntico multi-tool para detectar vectores de indirect injection antes del deployment?
