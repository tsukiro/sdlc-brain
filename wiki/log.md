# Log

Registro cronológico append-only de todas las operaciones del wiki.

**Parsear últimas entradas:** `grep "^## \[" wiki/log.md | tail -10`

---

## [2026-05-13] meta | Inicialización del wiki

## [2026-05-13] ingest | IBM — What is SDLC?
- Páginas creadas: [[ibm-sdlc]], [[concepts/v-model]]

## [2026-05-13] ingest | AWS — What is SDLC?
- Páginas creadas: [[aws-sdlc]], [[concepts/devsecops]], [[concepts/spiral]], [[concepts/iterative-model]]

## [2026-05-13] ingest | Atlassian — What is SDLC?
- Páginas creadas: [[atlassian-sdlc]]
- Tensión: 6 fases (AWS) vs 7 (Atlassian)

## [2026-05-13] ingest | LangGraph (docs.langchain.com)
- Páginas creadas: [[langgraph]], [[concepts/langgraph]]

## [2026-05-13] ingest | CrewAI OSS (crewai.com)
- Páginas creadas: [[crewai]]

## [2026-05-13] ingest | Gemini (gemini.google.com + ai.google.dev)
- Gemini 3 Pro (1501 Elo), Flash ($0.50/1M, 3x más rápido), gemini-embedding-2-preview
- Páginas creadas: [[gemini]], [[entities/gemini]]

## [2026-05-13] ingest | Firecrawl — Chunking Strategies for RAG
- 7 estrategias, recall 64-92%
- Páginas creadas: [[chunking-rag]], [[concepts/chunking]]
- Tensión: overlap estándar cuestionado (ene-2026)

## [2026-05-13] ingest | LangChain Text Splitters
- Páginas creadas: [[langchain-text-splitters]]

## [2026-05-13] ingest | Lilian Weng — LLM Powered Autonomous Agents
- Arquitectura Planning+Memory+Tools, técnicas CoT/ToT/ReAct/Reflexion, ANN, casos de uso
- Páginas creadas: [[lilian-weng-agents]], [[concepts/llm-agents]], [[concepts/planning]], [[concepts/agent-memory]]

## [2026-05-13] ingest | Andrew Ng — Agentic Design Patterns
- 4 patrones; HumanEval: GPT-3.5 agente 95.1% > GPT-4 zero-shot 67%
- Páginas creadas: [[andrew-ng-agentic-patterns]], [[concepts/agentic-design-patterns]]

## [2026-05-13] ingest | BALROG — Benchmarking Agentic LLM/VLM (arXiv 2411.13543, ICLR 2025)
- Benchmark en juegos: BabyAI/Crafter/TextWorld/Baba Is AI/MiniHack/NetHack
- Mejor modelo: 34.9%; VLMs rinden PEOR con visión que LLMs con solo texto
- Nota: el arXiv ID provisto (2408.03297) era incorrecto; ID correcto: 2411.13543
- Páginas creadas: [[balrog]], [[concepts/agent-evaluation]]

## [2026-05-13] ingest | LangSmith — Evaluating Agents (docs.smith.langchain.com)
- Trajectory match, LLM judge, evaluación de pasos intermedios, 10-20 ejemplos curados
- Páginas creadas: [[langsmith-evaluation]]
- Páginas actualizadas: [[concepts/agent-evaluation]]

## [2026-05-13] ingest | Reflexion paper (arXiv 2303.11366, Shinn et al.)
- Verbal RL, 91% HumanEval; confirmation bias; MAR; reflection grounding
- Páginas creadas: [[reflexion-paper]]
- Páginas actualizadas: [[concepts/planning]] (datos empíricos del paper + limitaciones investigación 2024-2025)

## [2026-05-13] ingest | LLM Agents exploit One-day Vulnerabilities (arXiv 2404.08144, Fang et al.)
- GPT-4: 87% de 15 CVEs críticos; otros modelos y ZAP/Metasploit: 0%
- Páginas creadas: [[llm-agents-vulnerabilities]], [[concepts/llm-security]]

## [2026-05-13] ingest | OWASP Top 10 for LLM Applications 2025 (genai.owasp.org)
- 10 vulnerabilidades; LLM01 (Prompt Injection) y LLM06 (Excessive Agency) son las más críticas en contextos agénticos
- Páginas creadas: [[owasp-llm-top10]]
- Páginas actualizadas: [[concepts/llm-security]]

## [2026-05-13] ingest | Anthropic Computer Use (docs.anthropic.com)
- Agent loop visual: screenshot→acción→screenshot; Docker+Xvfb; WebArena SOTA
- Amplifica LLM01+LLM06; clasificadores de prompt injection en screenshots
- Páginas creadas: [[computer-use]], [[concepts/computer-use]]
- Páginas actualizadas: [[concepts/llm-security]], [[entities/gemini]] (conectado)

## [2026-05-13] ingest | arXiv search — Trajectory noise / Memory contamination
- Contenido: MAR (Multi-Agent Reflexion), reflection grounding, experience-following behavior
- No se crearon páginas nuevas; contenido integrado en [[reflexion-paper]], [[concepts/planning]], [[concepts/agent-memory]]
- Nota: Lilian Weng (sección Planning) ya estaba ingresada — se saltó sin duplicar

## [2026-05-13] ingest | Prompt Injection — Deep Dive (5 fuentes + Lakera 404 + Anthropic auth)
- Fuentes: OWASP LLM01 específico, Simon Willison ×2 (2022 origen + 2023 indirect injection), NeMo Guardrails, Learn Prompting defensive measures
- Nota: Anthropic mitigation guide inaccesible (auth requerida); Lakera AI (404)
- Hallazgo clave: prompt injection NO tiene solución técnica equivalente a SQL injection — arquitecturalmente irresoluble en LLMs actuales
- Hallazgo clave: indirect injection (Greshake/Willison 2023) — el entorno ataca al agente sin que el usuario intervenga
- Páginas creadas: [[owasp-llm01-detail]], [[simon-willison-2022]], [[simon-willison-2023]], [[nemo-guardrails]], [[learn-prompting-defense]], [[concepts/prompt-injection]], [[entities/nemo-guardrails]]
- Páginas actualizadas: [[concepts/llm-security]] (LLM01 expandido, NeMo como mitigación), [[overview]] (2 nuevas tensiones, 3 nuevas preguntas abiertas), [[index]]

## [2026-05-13] ingest | Prompt Injection — Irreducibilidad Arquitectural y Soluciones Fuera del Modelo
- Responde la pregunta abierta: ¿existe solución arquitectural para PI? → No al 100%. Falla estructural: ausencia de separación Von Neumann en Transformers.
- 4 soluciones arquitecturales documentadas: Constrained Decoding (Outlines/Guidance/vLLM), Dual LLM, Control Vectors, Zero Trust + Ephemeral Containers
- Páginas creadas: [[prompt-injection-architecture]]
- Páginas actualizadas: [[concepts/prompt-injection]] (tabla comparativa 6 capas, Constrained Decoding, Dual LLM, Control Vectors), [[overview]] (pregunta abierta marcada respondida, 22 fuentes), [[index]]

## [2026-05-13] query | ¿Cuál es el flujo recomendado para implementar SDLC con IA?
- Síntesis fase a fase: Plan→Design→Implement→Test→Deploy→Maintain con los 4 patrones agénticos + capa de seguridad transversal
- Respuesta archivada en: [[analyses/sdlc-ia-flujo-recomendado]]

## [2026-05-13] query | Diagramas Mermaid de los 4 agentic design patterns
- 9 diagramas: bucle Reflection, variante Reflexion, Tool Use, CoT, ReAct, ToT, árbol de decisión de técnicas, topologías Multi-Agent, composición unificada
- Respuesta archivada en: [[analyses/agentic-patterns-diagramas]]

## [2026-05-14] ingest | LangChain ConfluenceLoader (docs.langchain.com)
- ConfluenceLoader: carga páginas Confluence como Document objects para pipelines RAG
- Métodos de auth: username+api_key (cloud), PAT token (on-prem), OAuth2, cookies
- Parámetros clave: page_ids, space_key, include_attachments, limit, max_pages
- Ejemplo completo de integración en pipeline RAG (load → chunk → embed → retrieve)
- Páginas creadas: [[langchain-confluence-loader]]
- Páginas actualizadas: [[index]]

## [2026-05-14] ingest | SDD Metodología — Piskala paper + Power Inversion/speckit + TOON format (3 documentos)
- **Fuente 1:** Paper académico Deepak Babu Piskala — SDD como metodología (spec-first/anchored/as-source, workflow 4 fases, herramientas, casos de estudio)
- **Fuente 2:** "The Power Inversion" — filosofía SDD + comandos /speckit.specify /speckit.plan /speckit.tasks + constitutional architecture
- **Fuente 3:** "TOON format" — Token-Oriented Object Notation: 40-60% ahorro tokens vs JSON para arrays uniformes, Tabular Eligibility, estrategia híbrida
- Hallazgo clave: "SDD" tiene dos significados distintos en este vault — resolver la ambigüedad fue el primer paso del ingest
- Hallazgo clave (Piskala): human-refined specs reducen errores LLM en **~50%**; specs = "super-prompts" que permiten ejecución paralela de agentes
- Hallazgo clave (TOON): JSON "syntax tax" = 40-60% de tokens en datos tabulares; TOON con TE>80% aumenta accuracy 70%→74%
- Nueva tensión: SDD-metodología vs SDD-constrained decoding (mismo término, conceptos distintos)
- Nueva tensión: TOON latencia vs ahorro de tokens en inferencia local (siempre medir TTFT)
- Páginas creadas: [[sdd-piskala-paper]], [[sdd-power-inversion]], [[toon-format]], [[concepts/spec-driven-development]], [[entities/toon]]
- Páginas actualizadas: [[overview]] (temas 13+14, tensiones 13-15, mapa de conceptos), [[index]]

## [2026-05-13] ingest | Structured Generation / SDD — Outlines, Instructor, TypeChat, jxnl.co (4 fuentes)
- Fuentes: arXiv 2307.09702 (Willard & Louf), python.useinstructor.com, microsoft.github.io/TypeChat/, jxnl.co/writing/
- Nota: jxnl.co URL devolvió 404; contenido reconstruido desde blog principal y ecosistema Instructor
- Hallazgo clave: Constrained Decoding opera a nivel matemático (logit masking) — no depende del LLM para obedecer el schema
- Hallazgo clave (Liu): "Good LLM Pipelines are just Good Software Engineering" — tipado + contratos + reintentos + observabilidad
- Nueva tensión: Instructor (retry/API) vs. Outlines (logit masking/local) — distintos niveles de garantía
- Nueva tensión: "Simple tools win" (Liu) vs. arquitecturas multi-agent — SDD como puente (outputs estructurados reducen el ruido de los hops)
- Páginas creadas: [[outlines-paper]], [[instructor]], [[typechat]], [[jxnl-pipelines]], [[concepts/structured-generation]], [[entities/outlines]], [[entities/instructor]], [[entities/typechat]]
- Páginas actualizadas: [[gemini]] (sección Structured Outputs nativos + Instructor), [[overview]] (tema 12 + 2 tensiones nuevas + mapa de conceptos), [[index]]

## [2026-05-14] ingest | LLM Wiki — Patrón Karpathy + caso MariaDB Labs
- Alternativa a RAG para corpus ≤400k tokens: compile-time synthesis, query-time context load (sin retrieval step)
- MariaDB Labs (78 artículos / 265k palabras / ~1M tokens): P95 latencia 1100ms → 180ms (6×), costo $0.15 → $0.03 / 1000 queries (5×)
- 5 errores comunes documentados: no linting, wiki sin límite, alucinación en síntesis, falta de versionado, actualización infrecuente
- Meta-nota: este vault SDLC es la implementación viva del patrón descrito
- Páginas creadas: [[llm-wiki-karpathy]], [[concepts/llm-wiki]]
- Páginas actualizadas: [[overview]] (tema 15 + mapa de conceptos), [[index]]
