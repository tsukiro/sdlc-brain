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
