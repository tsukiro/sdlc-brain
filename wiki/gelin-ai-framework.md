---
title: "AI Framework para Developer Experience — Jonathan Gelin (smartsdlc.dev)"
type: source-summary
tags: [ai-framework, sdlc, developer-experience, mcp, claude-code, skills, gobernanza, platform-engineering, compound-effect]
sources: []
updated: 2026-05-14
---

# AI Framework para Developer Experience — Jonathan Gelin (smartsdlc.dev)

**Fuente:** https://smartsdlc.dev/blog/ai-powered-sdlc-building-an-ai-framework-for-developer-experience/  
**Autor:** Jonathan Gelin  
**Publicado:** 2026-01-12  
**Tesis central:** El poder del AI-SDLC no está en herramientas individuales — está en el **AI Framework** que encapsula el conocimiento organizacional. Se construye una vez y beneficia todos los casos de uso con efecto compuesto.

---

## El AI Framework — concepto central

> "Without an AI Framework, every AI interaction starts from zero. With an AI Framework, the AI becomes a team member who has read all the documentation and actually remembers it."

El AI Framework **no es un producto que se compra** — es un artefacto que se construye y mantiene internamente, adaptado al contexto específico de cada organización.

### Componentes del AI Framework

| Componente | Descripción | Ejemplo |
|---|---|---|
| **Agents** | Personas especializadas con expertise específico | PM Agent, Frontend Agent, QA Agent |
| **Skills** | Contexto auto-cargado según tipo de tarea | Design system skill, API conventions skill |
| **MCPs** | Conexiones a datos y herramientas externas | Nx MCP, Jira MCP, Git MCP |
| **Prompts** | Instrucciones reutilizables para tareas comunes | "Al generar un componente, usa siempre nuestro Button primitive" |
| **Templates** | Estructuras estándar para outputs comunes | PR template, ADR template, user story format |
| **Rules** | Restricciones arquitecturales y convenciones | "Feature modules no pueden importar de otros features" |
| **Examples** | Implementaciones de referencia | "Así estructuramos un service típico" |

### El efecto compuesto

```
Framework inicial (bajo valor por interacción)
     ↓ añadir skills + rules + prompts + examples
Framework maduro (alto valor por interacción)
     ↓ cada mejora al framework beneficia TODOS los use cases inmediatamente
```

> "Discovered a better prompt for code reviews? Add it to the framework. New architectural pattern? Update the skills. Common mistake? Add a rule."

---

## Engineering Foundations — lo que el AI necesita para ser efectivo

El AI Framework consume conocimiento organizado. Cuanto más estructuradas las foundations, más potente el AI:

| Foundation | Contenido |
|---|---|
| **Codebase** | Patrones actuales, implementaciones de referencia |
| **Architecture** | Diseño de sistema, boundaries, constraints, ADRs |
| **Design System** | Componentes UI, design tokens, estándares de accesibilidad |
| **Dependency Graph** | Relaciones entre proyectos e impacto de cambios |
| **CI/CD Signals** | Build history, failures, flaky tests, bottlenecks |
| **Documentation** | Convenciones, onboarding guides, runbooks |
| **Methodologies** | Agile, Scrum, SAFe, definition of done |

> "Organizations with clear boundaries, ownership, and conventions naturally benefit more from AI — because AI has a *map*, not just files."

---

## 4 Categorías de AI Tooling

El AI Framework es consumido por múltiples herramientas — todas obtienen el mismo comportamiento porque comparten el mismo framework:

| Categoría | Descripción | Herramientas |
|---|---|---|
| **In-Context Coding Assistants** | "AI sentado junto a mí mientras escribo" | Cursor, Windsurf, GitHub Copilot |
| **Conversational & Reasoning Tools** | "AI con quien hablo cuando necesito pensar" | Claude.ai, ChatGPT, Gemini |
| **Automation & Agentic Tools** | "AI que hace trabajo por mí" | Claude Code, Gemini CLI, OpenCode |
| **Quality, Governance & Delivery** | "AI que vigila el sistema" | PR review bots, CI AI, architecture enforcement |

---

## AI por fase del SDLC (framework Gelin: Specify→Design→Develop→Validate→Release→Maintain)

### Specify

- **Task Writing**: AI escribe tasks desde descripciones rough; sugiere acceptance criteria; identifica gaps
- **Backlog Organization**: AI agrupa items relacionados, identifica duplicados, sugiere priorización
- **Interview Mode** ← técnica potente:
  ```markdown
  read this @SPEC.md and interview me in detail using the AskUserQuestionTool
  about literally anything: technical implementation, UI & UX, concerns, tradeoffs, etc.
  but make sure the questions are not obvious
  
  be very in-depth and continue interviewing me continually until it's complete,
  then write the spec to the file
  ```
  En lugar de escribir la spec desde cero → conversación dirigida por AI que genera la spec completa.

### Design

- Generación de diagramas (componentes, secuencia, datos, API contracts/OpenAPI)
- ADRs estructurados (capturan decisiones en formato que AI puede referenciar)
- UI/UX mockups con design tokens del design system
- Documentación viva sincronizada con el código

### Develop

- **Contextualized code generation**: AI con framework usa convenciones correctas, imports correctos, boundaries del módulo — no crea utilities genéricas si ya existe `@org/utils`
- **Codebase understanding**: "¿Qué hace este servicio?", "¿Qué romperá si cambio esto?"
- **Legacy modernization**: pattern detection, automated codemods, transformación incremental
- **Agentic tasks**: "Crea un feature module con service, component y tests"

### Validate

- **PR Automation**: AI analiza diffs y genera PR descriptions, lista componentes afectados, destaca riesgos
- **Code Review**: primera pasada que deja a los humanos enfocarse en diseño y lógica
- **Quality Gates**: bloquean PRs que violan module boundaries, dependencias no aprobadas, docs faltantes
- **Self-healing CI**:
  ```
  CI runs and fails
       ↓
  AI agent analyzes error logs with codebase context
       ↓
  Fix proposed as PR comment or auto-applied
  ```

### Release

- Changelog generation desde commits + PRs → conventional commits (`feat:`, `fix:`, `breaking:`) son el enabler
- Release communication adaptada por audiencia (stakeholders, customers, technical teams)
- Deployment validation, incident analysis post-deploy

### Maintain

- **Automated Refactoring**: rename patterns, extract shared code, update APIs a escala
- **Automatic Upgrades**: AI lee changelogs de dependencias, identifica breaking changes, propone migrations
- **Tech Debt Analysis**: no "tenemos deuda técnica" — lista priorizada con estimaciones de esfuerzo

---

## Claude Code como implementación del AI Framework

El artículo identifica Claude Code como pionero del concepto de AI Framework en la práctica:

| Primitiva Claude Code | Equivalente en AI Framework |
|---|---|
| **MCP (Model Context Protocol)** | Conexión a datos externos (Nx, Jira, Git, DBs) |
| **Skills** | Contexto auto-cargado por tipo de tarea |
| **Agents** | Personas especializadas invocables |
| **Prompts** | Instrucciones reutilizables |
| **Commands** | Slash commands (`/commit`, `/review`, `/deploy`) que disparan workflows consistentes |
| **Hooks** | Automatización event-driven: PreToolUse, PostToolUse, Stop |
| **LSP** | Integración con IDE en tiempo real |

> "Claude Code pioneered concepts like MCP and Skills that are becoming industry standards. Its plugin system maps directly to the AI Framework concept, making it ideal for organizational standardization."

**Otras CLIs mencionadas:**
- **Gemini CLI** — Google's entry, MCP support, similar conventions
- **OpenCode** — open-source, multi-provider (OpenAI, Anthropic, Google, local models)

---

## BMAD Method — agentes especializados por rol

Para proyectos enterprise-scale, BMAD provee una estructura con agentes especializados:

| Agente | Responsabilidad |
|---|---|
| **Business Analyst** | Recopilación de requisitos, necesidades de stakeholders |
| **Product Manager** | Priorización, roadmap, user stories |
| **Architect** | Diseño de sistema, decisiones técnicas |
| **Developer** | Detalles de implementación, estructura de código |
| **QA** | Escenarios de test, edge cases, validación |

El output de un agente alimenta al siguiente. Documentación: [docs.bmad-method.org](https://docs.bmad-method.org/)

---

## MCP (Model Context Protocol) — la capa de integración

**MCP** conecta los AI agents con datos y herramientas externas. Es el mecanismo por el que el AI Framework accede al contexto organizacional en tiempo real:

```
AI Agent
   ↓ (via MCP)
├── Nx MCP → project graph, generators, affected analysis
├── Jira MCP → tickets, backlog, sprints
├── Git MCP → historia, diffs, blame
└── Custom MCPs → DBs, APIs internas, observability
```

**Riesgo sin gobernanza** (documentado en Isenberg + CyberArk): MCPs no controlados pueden exponer datos organizacionales sensibles. El **Centralized MCP Broker** es la mitigación.

---

## Estrategia de inicio

```
1. Foundation: crear estructura /skills, documentar convenciones, configurar MCP
2. Primer caso de uso: self-healing CI | codebase Q&A | automated review
3. Medir: time-to-first-PR, CI resolution time, review turnaround
4. Expandir: añadir skills, agents y MCPs progresivamente
```

> "Start small, prove value, then expand. Training developers on prompting and building trust through gradual adoption is key."

---

## Tensiones / insights

- **"Tools are interchangeable, framework is not"**: Claude Code, Gemini CLI, Cursor — cualquiera puede consumir el mismo AI Framework. Invertir en el framework, no en el tool.
- **Living documentation**: cuando el código cambia, la documentación sigue — solo si está generada desde el código (no mantenida manualmente).
- **AI augments, not replaces**: "AI handles the repetitive; you focus on the creative." — Reafirma la posición de Ng, Weng y Snyk.

---

## Conexiones

- [[isenberg-ai-sdlc]] — misma problemática (gobernanza, SDD como base) con más detalle en el flujo organizacional; Gelin añade el concepto de AI Framework como artefacto central
- [[snyk-ai-sdlc]] — perspectiva táctica por fase; Gelin añade la capa de framework
- [[concepts/spec-driven-development]] — SDD es el enabler de la fase Specify; Interview Mode como técnica concreta
- [[sdd-power-inversion]] — speckit es la implementación de los Skills del AI Framework
- [[concepts/agentic-design-patterns]] — los 4 patrones de Ng se aplican en todas las fases del AI Framework
- [[concepts/multi-agent-frameworks]] — BMAD como orquestación multi-agent; LangGraph/CrewAI como alternativas técnicas
- [[concepts/langgraph]] — para implementar los agents especializados del AI Framework
- [[concepts/llm-security]] — MCP Broker centralizado como capa de seguridad; Skills como mitigación de hallucination (AI usa el contexto correcto)
- [[llm-wiki-karpathy]] — el AI Framework IS un LLM Wiki para la organización: conocimiento compilado que el AI consume en cada interacción
