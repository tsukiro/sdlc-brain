# Index

Catálogo de todas las páginas del wiki. El LLM mantiene este archivo actualizado con cada operación.

---

## Overview

- [[overview]] — Síntesis de alto nivel: tesis central, mapa completo, tensiones, preguntas abiertas

---

## Source Summaries

### SDLC
- [[ibm-sdlc]] — IBM: SDLC como roadmap, gestión de riesgos, V-Model, IBM Bob (1 fuente)
- [[aws-sdlc]] — AWS: 6 fases, tabla de modelos, DevSecOps, distinciones SDLC/ALM (1 fuente)
- [[atlassian-sdlc]] — Atlassian: 7 fases, frameworks Agile (Scrum/Kanban/Lean/XP), Jira (1 fuente)
- [[snyk-ai-sdlc]] — Snyk/Thoemmes: SDLC con IA por fase, tabla traditional vs AI, implementación incremental, Gartner 2028 (1 fuente)
- [[isenberg-ai-sdlc]] — Ran Isenberg: AI-SDLC gobernado con SDD, MCP broker centralizado, flujo Jira→producción, desafíos de escala (1 fuente)
- [[gelin-ai-framework]] — Jonathan Gelin: AI Framework como artefacto organizacional, 7 componentes, efecto compuesto, MCP, Claude Code como referencia, Interview Mode (1 fuente)
- [[gelin-skills-format]] — Jonathan Gelin: Skills como npm de AI, anatomía YAML+markdown, progressive loading 3 niveles, convergencia cross-platform, ClawHub (1 fuente)
- [[promptingguide-techniques]] — DAIR-AI: 20 técnicas (Zero-shot→Prompt Functions), jerarquía de prompting, 17 papers; APE, PAL, Active-Prompt, ReAct, Reflexion, Multimodal CoT, Function Calling, Context Caching, Prompt Functions (20 artículos)
- [[promptingguide-introduction]] — DAIR-AI: LLM Settings (Temperature/Top P/Frequency/Presence Penalty), Prompt Elements (4 componentes), Tips (Do vs. Don't, especificidad, verbos de comando) (4 artículos)
- [[promptingguide-risks]] — DAIR-AI: Factualidad/alucinaciones, Distribution Bias + Order Bias (experimento 8:2), Adversarial Prompting (DAN, Waluigi Effect, Prompt Injection, defenses) (3 artículos)
- [[promptingguide-applications-data]] — DAIR-AI: Generación de datos sintéticos, Synthetic RAG (Dai et al. 2022: 8 ejemplos/$55/50K docs), Code Gen (imports failure mode), Case Study Clavié et al. 2023 (F1 65.6→91.7, CoT few-shot peor que zero-shot) (4 artículos)
- [[promptingguide-models]] — DAIR-AI: ChatGPT (RLHF, Role Prompting), GPT-4 (128K, JSON Mode, seed, system_fingerprint), LLaMA (Chinchilla insight, 13B > GPT-3 en 1 GPU), Mistral 7B (GQA+SWA, 14-cat moderation, Apache 2.0) (4 artículos)

### Agentes LLM — Fundamentos
- [[lilian-weng-agents]] — Weng: Planning+Memory+Tools, CoT/ToT/ReAct/Reflexion, ANN, casos de uso (1 fuente)
- [[andrew-ng-agentic-patterns]] — Ng: 4 patrones, HumanEval GPT-3.5 agente 95.1% (1 fuente)
- [[reflexion-paper]] — Shinn et al.: Verbal RL, 91% HumanEval, confirmation bias, MAR (1 fuente)

### Agentes LLM — Evaluación
- [[balrog]] — BALROG (ICLR 2025): benchmark en juegos, max 34.9%, VLMs peor con visión (1 fuente)
- [[langsmith-evaluation]] — LangSmith: trajectory match, LLM judge, evaluación de pasos intermedios (1 fuente)

### Agentes LLM — Seguridad
- [[llm-agents-vulnerabilities]] — Fang et al.: GPT-4 explota 87% de CVEs; otros modelos 0% (1 fuente)
- [[owasp-llm-top10]] — OWASP LLM Top 10 2025: 10 vulnerabilidades, LLM01/LLM06 críticas (1 fuente)
- [[owasp-llm01-detail]] — LLM01 Prompt Injection: 9 escenarios, 7 mitigaciones, no existe prevención fool-proof (1 fuente)
- [[simon-willison-2022]] — Willison: acuñación del término, analogía SQL injection, límite de prompts parametrizados (1 fuente)
- [[simon-willison-2023]] — Willison/Greshake: indirect injection, ataque Bing Chat, exfiltración (1 fuente)
- [[nemo-guardrails]] — NVIDIA NeMo: 5 rails, Colang DSL, integración LangChain (1 fuente)
- [[learn-prompting-defense]] — Learn Prompting: 7 técnicas defensivas (Sandwich, XML Tagging, etc.) (1 fuente)
- [[prompt-injection-architecture]] — Análisis arquitectural: Von Neumann gap, Constrained Decoding, Dual LLM, Control Vectors (1 fuente)
- [[gcav-paper]] — Zhang et al. (arXiv 2501.05764): GCAV framework, CAV training, ε dinámico, 51% toxicity reduction, multi-concepto (1 fuente)
- [[calcagni-control-vectors]] — Calcagni (LessWrong 2024): rasgos disposicionales, PCA-based, 12-trait framework, Anthropic Persona Vectors (ago-2025) (1 fuente)

### Frameworks de Orquestación
- [[langgraph]] — LangGraph: StateGraph, nodes/edges, checkpointing, interrupts, primitivas (1 fuente)
- [[langgraph-python]] — LangGraph Python: implementación completa, persistencia, HITL, streaming, memory store (2 fuentes)
- [[langgraph-js]] — LangGraph.js/TypeScript: StateGraph, ToolNode, checkpointing, streaming en Node.js (1 fuente)
- [[langchain-python]] — LangChain Python: messages, tools (@tool), agentes, interfaz multi-proveedor (1 fuente)
- [[langchain-js]] — LangChain JS/TypeScript: instalación npm, Zod schemas, tools, agentes en Node.js (1 fuente)
- [[crewai]] — CrewAI OSS: agents, tasks, crews, memoria, Agentic RAG (1 fuente)
- [[gemini]] — Gemini 3 Pro/Flash, multimodalidad, Computer Use, embeddings multimodales (1 fuente)
- [[computer-use]] — Anthropic Computer Use: agent loop, acciones, seguridad, WebArena SOTA (1 fuente)

### RAG y Chunking
- [[chunking-rag]] — Firecrawl: 7 estrategias, recall 64-92%, árbol de decisión (1 fuente)
- [[langchain-text-splitters]] — LangChain: splitters, parámetros, pipeline RAG (1 fuente)
- [[langchain-confluence-loader]] — ConfluenceLoader: carga páginas Confluence como Documents, autenticación cloud/on-prem, pipeline RAG (1 fuente)
- [[westtrain-rag-confluence]] — WestTrain/Joshua Lee: caso RAG on-premise con Mistral/Ollama + ChromaDB + Confluence, MMR, similarity thresholding (1 fuente)

### Structured Generation / SDD (constrained decoding)
- [[outlines-paper]] — Willard & Louf (2023): FSM + logit masking, base matemática de constrained decoding (1 fuente)
- [[instructor]] — Instructor (Jason Liu): Pydantic como schema, 15+ proveedores, reintentos automáticos (1 fuente)
- [[typechat]] — TypeChat (Microsoft): interfaces TypeScript como especificación LLM, compilador como validador (2 fuentes)
- [[jxnl-pipelines]] — Jason Liu: LLM pipelines = software engineering; simple tools win, no multi-agents por defecto (1 fuente)

### SDD — Metodología de desarrollo
- [[sdd-piskala-paper]] — Piskala: espectro spec-first/anchored/as-source, workflow 4 fases, 50% error reduction, casos de estudio (1 fuente)
- [[sdd-power-inversion]] — Power Inversion: filosofía SDD, comandos speckit (/specify /plan /tasks), constitutional architecture (1 fuente)
- [[toon-format]] — TOON: Token-Oriented Object Notation, 40-60% ahorro vs JSON, Tabular Eligibility, estrategia híbrida (1 fuente)

### LLM Wiki
- [[llm-wiki-karpathy]] — Karpathy + MariaDB Labs: compile-time wiki, 6× latencia y 5× costo vs RAG, 5 errores comunes (1 fuente)

---

## Concepts

### SDLC
- [[concepts/sdlc]] — SDLC: definición, fases comparadas, todos los modelos (3 fuentes)
- [[concepts/waterfall]] — Waterfall: secuencial, ventajas/desventajas (3 fuentes)
- [[concepts/agile]] — Agile: sprints, frameworks Scrum/Kanban/Lean/XP (3 fuentes)
- [[concepts/v-model]] — V-Model: Waterfall + testing por fase (1 fuente: IBM)
- [[concepts/spiral]] — Spiral: iterativo con análisis de riesgo (1 fuente: AWS)
- [[concepts/iterative-model]] — Iterativo: versiones sucesivas (1 fuente: AWS)
- [[concepts/devsecops]] — DevSecOps: seguridad integrada en SDLC (1 fuente: AWS)

### Agentes LLM
- [[concepts/llm-agents]] — Arquitectura base: Planning+Memory+Tools + benchmark HumanEval (2 fuentes)
- [[concepts/agentic-design-patterns]] — 4 patrones Ng: Reflection/Tool Use/Planning/Multi-Agent (2 fuentes)
- [[concepts/prompt-engineering]] — Zero-shot, Few-shot, ICL (Brown et al. 2020; Min et al. 2022 sobre el rol de las etiquetas) (1 fuente)
- [[concepts/planning]] — 11 técnicas: CoT, Self-Consistency, Generated Knowledge, ToT, ReAct, ART, PAL, APE, Reflexion (91% HE + 130/134 AlfWorld), LLM+P, CoH (4 fuentes)
- [[concepts/agent-memory]] — Memoria: STM/LTM, ANN (LSH/ANNOY/HNSW/FAISS/ScaNN) (1 fuente)
- [[concepts/agent-evaluation]] — Evaluación: trajectory evaluation, BALROG, LangSmith (3 fuentes)

### Seguridad
- [[concepts/llm-security]] — Seguridad LLM: OWASP Top 10, Fang et al., superficies agénticas (8 fuentes)
- [[concepts/computer-use]] — Computer Use: agent loop visual, Claude+Gemini, seguridad (2 fuentes)
- [[concepts/prompt-injection]] — Prompt Injection: taxonomía completa, 9 vectores, mitigaciones en capas (5 fuentes)

### Frameworks
- [[concepts/langchain]] — LangChain: framework base, messages, tools, models, instalación Python+JS, cuándo usar LangChain vs LangGraph (2 fuentes)
- [[concepts/langgraph]] — LangGraph: arquitectura, primitivas, tabla de paridad Python vs JS (3 fuentes)
- [[concepts/multi-agent-frameworks]] — LangGraph vs CrewAI: comparativa, cuándo elegir (2 fuentes)

### RAG y Chunking
- [[concepts/chunking]] — 7 estrategias con métricas, árbol de decisión (2 fuentes)
- [[concepts/rag]] — Pipeline RAG: variantes (Adaptive/Agentic/Multimodal), stack 2026 (4 fuentes)

### Structured Generation (constrained decoding)
- [[concepts/structured-generation]] — Constrained Decoding / SDD: FSM+logit masking, Instructor/TypeChat, árbol de decisión, relación con seguridad (4 fuentes)

### SDD — Metodología de desarrollo
- [[concepts/spec-driven-development]] — SDD como metodología: espectro 3 niveles, workflow 4 fases, herramientas por categoría, árbol de decisión, relación TDD/BDD/DDD (3 fuentes)

### LLM Wiki
- [[concepts/llm-wiki]] — LLM Wiki vs RAG: árbol de decisión, compile/query time, propiedades, errores comunes (1 fuente)

---

## Entities

- [[entities/langchain]] — LangChain (ecosistema): LangChain + LangGraph + LangSmith, paquetes Python y JS, adopción en producción (2 fuentes)
- [[entities/gemini]] — Gemini (Google DeepMind): 3 Pro/Flash/2.x, capacidades, pricing, Context Caching (comparativa vs. RAG vs. LLM Wiki) (2 fuentes)
- [[entities/nemo-guardrails]] — NeMo Guardrails (NVIDIA): 5 rails, Colang, mitigación de prompt injection (1 fuente)
- [[entities/outlines]] — Outlines (Normal Computing): constrained decoding a nivel de logits, FSM, garantía matemática (1 fuente)
- [[entities/instructor]] — Instructor (Jason Liu): librería Python #1 structured outputs, Pydantic, 15+ proveedores (1 fuente)
- [[entities/typechat]] — TypeChat (Microsoft Research): interfaces TypeScript como schema LLM, compilador como validador (1 fuente)
- [[entities/toon]] — TOON (Token-Oriented Object Notation): formato de datos para prompts LLM, 40-60% menos tokens que JSON (1 fuente)

---

## Analyses

- [[analyses/sdlc-ia-flujo-recomendado]] — Flujo recomendado fase a fase para SDLC con IA (síntesis multi-fuente + árbol de decisión)
- [[analyses/agentic-patterns-diagramas]] — Diagramas Mermaid: 4 patrones agénticos + composición unificada
