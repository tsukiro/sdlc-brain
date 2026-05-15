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

## [2026-05-14] ingest | Skills — El nuevo formato de paquete para AI (Gelin, 2026-02-23)
- Skills como "npm de AI agents": YAML frontmatter + markdown instructions + optional resources + triggers; formato convergente en OpenClaw, Claude Agent Skills y Cursor Rules
- Arquitectura técnica de 3 niveles: metadata (siempre), instructions (on trigger), resources (on demand) → solución al context window sin limitar profundidad
- Patrones de composición: orquestador + sub-skills, workflow multi-step, skill de contexto puro (solo conocimiento de dominio)
- ClawHub como registry de skills (análogo a npm para JavaScript)
- Desafíos: seguridad (sandboxing), calidad, fragmentación de plataformas
- Páginas creadas: [[gelin-skills-format]]
- Páginas actualizadas: [[gelin-ai-framework]] (Skills expandido con progressive loading y link al deep-dive), [[overview]], [[index]]

## [2026-05-14] ingest | AI Framework para Developer Experience — Jonathan Gelin (2026-01-12)
- AI Framework como artefacto organizacional central: 7 componentes (Agents, Skills, MCPs, Prompts, Templates, Rules, Examples); efecto compuesto: cada mejora beneficia todos los casos de uso
- 4 categorías de AI Tooling; 6 fases SDLC (Specify→Design→Develop→Validate→Release→Maintain) con aplicaciones concretas por fase
- Claude Code identificado como implementación de referencia del AI Framework: MCP, Skills, Agents, Prompts, Commands, Hooks, LSP
- Interview Mode como técnica de generación de specs: AI entrevista al developer para generar spec completa
- MCP (Model Context Protocol) explicado como capa de integración entre AI agents y herramientas externas
- BMAD Method: 5 agentes especializados (BA, PM, Architect, Developer, QA) para proyectos enterprise
- Pregunta abierta MCP marcada PARCIALMENTE RESPONDIDA; Tema 16 añadido al overview
- Páginas creadas: [[gelin-ai-framework]]
- Páginas actualizadas: [[concepts/spec-driven-development]] (Interview Mode, BMAD), [[overview]] (Tema 16, pregunta MCP), [[index]]

## [2026-05-14] ingest | AI-Driven SDLC — Ran Isenberg (2026-02-03)
- Tesis: adopción fragmentada de IA ralentiza equipos; gobernanza (MCP centralizado, prompt library, standardized skills) es condición necesaria para escalar
- SDD como fundamento del AI-SDLC: artefactos acumulativos por fase, sub-flujo Plan→Validar→Ejecutar, humano valida intención de negocio
- 5 capas de gobernanza: Standardized Skills, Team Extension Skills, Centralized MCP Broker, Shared Prompt Library, Incident Response Agents
- Flujo Jira→producción (10 pasos) con agentes especializados (FinOps, security, code quality) y validación humana en puntos clave
- Desafíos SDD en producción: overhead de proceso, specs más difíciles de revisar que código, agentes que ignoran specs, overkill para tareas pequeñas
- Gap identificado: no hay página de MCP (Model Context Protocol) en el wiki
- Páginas creadas: [[isenberg-ai-sdlc]]
- Páginas actualizadas: [[concepts/spec-driven-development]] (desafíos de producción, BMAD, Isenberg), [[overview]] (tensiones 16-18, pregunta abierta MCP), [[index]]

## [2026-05-14] ingest | AI in SDLC — Snyk / Stephen Thoemmes (2025-12-09)
- Nota: IBM URL (https://www.ibm.com/think/topics/ai-in-sdlc) solo cargó muro de cookies/consent frame — sin contenido extraíble; no se creó source-summary para esa URL
- Tabla comparativa Traditional vs AI-enhanced SDLC (9 dimensiones); IA por fase con nivel de madurez 2025
- Estrategia de adopción incremental: piloto 30 días → evaluar → expandir → escala; tiempos de integración por categoría (code completion 1-2w a architecture planning 8-12w)
- Gartner: 75% engineers enterprise usarán AI coding assistants para 2028; proyección: devs no reemplazados hasta 2040
- Riesgos nuevos: prompt injection en IDEs de codificación IA, secret leakage en código generado, sobredependencia y pérdida de habilidades
- Páginas creadas: [[snyk-ai-sdlc]]
- Páginas actualizadas: [[concepts/sdlc]] (nueva sección "SDLC potenciado por IA" con tabla e integración por categoría), [[overview]] (Tema 1 actualizado), [[index]]

## [2026-05-14] ingest | Control Vectors as Dispositional Traits — Calcagni (LessWrong)
- Reencuadra los control vectors como "rasgos disposicionales"; propone 12 traits en 4 categorías (Anti-Optimizer, Core Beliefs, Communication, Work Ethos) como mínimo para LLM seguro
- Variante PCA (vs. regresión logística de GCAV): ambas buscan el mismo eje semántico, son complementarias
- Técnica de validación: comparar vectores de conceptos cercanos ("honestidad" vs "honestidad fingida") para auditar qué aprendió el modelo
- Addendum crítico agosto 2025: Anthropic publicó Persona Vectors (arXiv 2507.21509) — control vectors en producción en frontier model
- Pregunta abierta "Control Vectors listos para producción" marcada RESPONDIDA
- Páginas creadas: [[calcagni-control-vectors]]
- Páginas actualizadas: [[prompt-injection-architecture]] (Sección 3 ampliada: PCA, validación, ortogonalidad, Persona Vectors), [[overview]] (pregunta marcada respondida), [[index]]

## [2026-05-14] ingest | GCAV — Control Vectors para LLMs (arXiv 2501.05764, Zhang et al.)
- Framework GCAV: entrena CAV con ~100 pares contrastivos vía regresión logística sobre activaciones del LLM; aplica steering dinámico (ε por input) durante inferencia sin modificar pesos
- Resultados: 51% reducción de toxicidad (Llama-2-7b-chat), best-in-class en sentimiento y multi-concepto
- Limitación crítica: requiere acceso a activaciones → solo modelos open-source on-premise; no aplicable a APIs cloud
- Pregunta abierta "Control Vectors listos para producción" marcada PARCIALMENTE RESPONDIDA
- Páginas creadas: [[gcav-paper]]
- Páginas actualizadas: [[prompt-injection-architecture]] (Sección 3 Control Vectors expandida con GCAV), [[overview]] (pregunta abierta actualizada), [[index]]

## [2026-05-14] ingest | RAG sobre Confluence — Caso de estudio WestTrain (Joshua Lee)
- Stack on-premise: Mistral vía Ollama + `intfloat/multilingual-e5-base` + ChromaDB + Confluence Cloud API
- Hallazgos clave: MMR > similarity_search para corpus con redundancia; similarity thresholding como anti-alucinación; chunk_size=400 overlap=100 (25%) para contenido multilingüe
- Técnica de debugging: cosine similarity entre query embedding y todos los vectores almacenados
- Páginas creadas: [[westtrain-rag-confluence]]
- Páginas actualizadas: [[concepts/rag]] (MMR, thresholding, on-premise, debugging), [[concepts/chunking]] (dato de producción multilingüe), [[langchain-confluence-loader]] (approach manual con requests+BeautifulSoup), [[index]]

## [2026-05-14] ingest | LLM Wiki — Patrón Karpathy + caso MariaDB Labs
- Alternativa a RAG para corpus ≤400k tokens: compile-time synthesis, query-time context load (sin retrieval step)
- MariaDB Labs (78 artículos / 265k palabras / ~1M tokens): P95 latencia 1100ms → 180ms (6×), costo $0.15 → $0.03 / 1000 queries (5×)
- 5 errores comunes documentados: no linting, wiki sin límite, alucinación en síntesis, falta de versionado, actualización infrecuente
- Meta-nota: este vault SDLC es la implementación viva del patrón descrito
- Páginas creadas: [[llm-wiki-karpathy]], [[concepts/llm-wiki]]
- Páginas actualizadas: [[overview]] (tema 15 + mapa de conceptos), [[index]]

## [2026-05-14] ingest | Prompt Engineering Guide — 9 técnicas fundamentales (DAIR-AI)
- Técnicas documentadas: Zero-shot (instruction tuning + RLHF), Few-shot (Brown 2020; Min 2022: formato > etiquetas), CoT (Wei + Kojima 2022; zero-shot CoT = "Let's think step by step"), Self-Consistency (Wang 2022; múltiples CoT + votación; +10-20 pts), Generated Knowledge (Liu 2022; dos fases), ToT (Yao 2023 + Long 2023; RL Controller), RAG (Lewis et al. 2021; paramétrico vs no-paramétrico), ART (Paranjape 2023; CoT + tools + Task Library; supera few-shot en BigBench + MMLU)
- Prompt Chaining: artículo sin contenido procesable (placeholder de traducción)
- Páginas creadas: [[promptingguide-techniques]], [[concepts/prompt-engineering]]
- Páginas actualizadas: [[concepts/planning]] (Self-Consistency T7, Generated Knowledge T8, ART T9; Kojima 2022 para zero-shot CoT; Long 2023 para ToT; tabla comparativa expandida), [[concepts/rag]] (Lewis et al. 2021 como origen del paradigma), [[overview]] (tema 3b Prompt Engineering; tema 4 Planning expandido; mapa actualizado; contador 51 fuentes / 87 páginas), [[index]]

## [2026-05-14] ingest | Prompt Engineering Guide — 2º lote (DAIR-AI): APE, PAL, ReAct, Reflexion, Multimodal CoT + otros
- Nuevas técnicas: APE (Zhou 2022; encontró "Let's work this out step by step" > "Let's think step by step" en MultiArith+GSM8K), PAL (Gao 2022; código Python como razonamiento → intérprete determinista), Active-Prompt (Diao 2023; CoT adaptativo por incertidumbre), DSP (Li 2023; policy LM genera hints para LLM congelado), Multimodal CoT (Zhang 2023; 1B parámetros > GPT-3.5 en ScienceQA), GraphPrompt (Liu 2023; GNNs)
- Correcciones: ReAct año 2022 (no 2023); Reflexion 3 componentes (Actor/Evaluator/Self-Reflection), AlfWorld 130/134
- ART: confirmación "in a zero-shot fashion" + "excede prompts artesanales con feedback humano"
- Páginas actualizadas: [[promptingguide-techniques]] (reescritura completa; 17 artículos, 17 papers), [[concepts/planning]] (PAL T10, APE T11; ReAct año fix + best-combo + ALFWorld/WebShop; Reflexion 3 componentes + AlfWorld 130/134; tabla expandida), [[overview]] (57 fuentes; Tema 4 Planning expandido a 11 técnicas), [[index]]

## [2026-05-14] ingest | Prompt Engineering Guide — Function Calling (DAIR-AI)
- Mecanismo: LLM fine-tuned detecta cuándo invocar → emite JSON con argumentos (JSON Schema) → aplicación ejecuta → LLM sintetiza respuesta final; distinción clave: el LLM no ejecuta, solo produce argumentos
- Casos de uso: conversational agents, NLU/extracción, math solving, API integration, knowledge retrieval
- Conexión con: ART (function calling como mecanismo de bajo nivel), ReAct (Actions vía function calls), Structured Generation (Instructor/TypeChat validan argumentos), Tool Use pattern (Ng)
- Páginas actualizadas: [[promptingguide-techniques]] (sección 18 añadida; tabla papers actualizada), [[overview]], [[index]]

## [2026-05-14] ingest | Prompt Engineering Guide — Context Caching con Gemini (DAIR-AI)
- Mecanismo: `caching.CachedContent.create()` sube contexto a Gemini API → servidor cachea KV states → queries reutilizan sin re-enviar tokens; configurable con TTL
- Caso demostrado: un año de papers ML en texto plano, consultas en lenguaje natural
- Conexión conceptual: Context Caching vs. LLM Wiki (KV cache server-side temporal vs. síntesis compilada persistente) vs. RAG (corpus dinámico)
- Páginas actualizadas: [[entities/gemini]] (sección Context Caching + comparativa vs. RAG vs. LLM Wiki; conexión a [[concepts/llm-wiki]]), [[promptingguide-techniques]] (sección 19 añadida; orden reajustado), [[overview]], [[index]] (contador actualizado)
