---
title: "Skills — El nuevo formato de paquete para AI (Gelin, smartsdlc.dev)"
type: source-summary
tags: [skills, ai-framework, package-format, claude-code, mcp, agents, composición, portable]
sources: []
updated: 2026-05-14
---

# Skills — El nuevo formato de paquete para AI (Gelin, smartsdlc.dev)

**Fuente:** https://smartsdlc.dev/blog/skill-is-the-new-package-format-for-ai/  
**Autor:** Jonathan Gelin  
**Publicado:** 2026-02-23  
**Tesis:** Los Skills son el npm para agentes AI — instrucciones reutilizables y portátiles que emergen como estándar en múltiples plataformas, permitiendo componer y compartir expertise en lugar de repetirlo.

> "Software packages contain code. Skills contain *knowledge*. They capture not just what to do, but how to think about problems. They're closer to mentoring than programming."

---

## El problema: cada agente empieza de cero

Trabajar con múltiples herramientas AI (Claude, Cursor, OpenClaw) significa re-explicar los mismos workflows, convenciones y criterios en cada herramienta. El conocimiento construido en un tool no transfiere a otro.

Analogía histórica: desarrollo web antes de npm. Copiar código entre proyectos, sin standards, sin discovery, sin versioning. npm cambió todo. Skills son la misma transformación para AI.

---

## Anatomía de un Skill

```yaml
---
name: property-search
description: Orchestrator for automated property search across France and Belgium
triggers: ["veille", "recherche bien", "property search", "nouveaux biens"]
---

# Property Search

[Instrucciones en markdown...]

## Search Criteria
[Parámetros detallados]

## Workflow
[Proceso paso a paso]

## Scripts
[Código de automatización bundleado]
```

**Componentes universales:**

| Componente | Función |
|---|---|
| **YAML frontmatter** | Metadata que ayuda al agente a decidir cuándo usar el skill |
| **Markdown instructions** | Guía human-readable que el agente sigue |
| **Optional resources** | Scripts, templates, reference data |
| **Triggers** | Keywords o patrones que activan el skill |

---

## Arquitectura técnica: Progressive Loading (3 niveles)

La solución al problema del context window:

```
Level 1: Metadata (siempre cargado)
├── Solo el YAML frontmatter: name + description
├── Ayuda al AI a decidir cuándo usar el skill
└── Impacto mínimo en context window

Level 2: Instructions (cargado cuando se activa el trigger)
├── El markdown body con guidance detallada
├── Cargado via filesystem reads on-demand
└── Uso moderado de contexto

Level 3: Resources (cargado bajo demanda)
├── Scripts, templates, reference data
├── Ejecutados sin cargar al contexto
└── Contenido bundleado ilimitado
```

**Resultado:** Skills de profundidad arbitraria sin saturar el context window.

---

## Convergencia cross-platform

Múltiples plataformas convergieron independientemente al mismo formato:

| Feature | OpenClaw | Claude Agent Skills | Cursor Rules |
|---|---|---|---|
| **File format** | SKILL.md | SKILL.md | .cursorrules |
| **Metadata** | YAML frontmatter | YAML frontmatter | Comment headers |
| **Instructions** | Markdown body | Markdown body | Markdown body |
| **Discovery** | Filesystem scanning | API/filesystem | Project scanning |
| **Triggers** | Keywords/patterns | Description matching | File patterns |

```bash
# Estructura de directorio (OpenClaw / Claude Skills)
.ai/skills/property-search/
├── SKILL.md          # Instrucciones principales
├── scripts/          # Automatización Python
└── data/             # Archivos de referencia
```

La convergencia no es accidental — todos resuelven el mismo problema: **cómo empaquetar y reutilizar expertise de AI**.

---

## Patrones de composición

### 1. Orquestador con sub-skills
```
property-search (orchestrator skill)
├── leboncoin-scraper (source skill)
├── immoweb-scraper (source skill)
└── property-scorer (scoring skill)
```
Cada skill foco en una responsabilidad; el orquestador los combina.

### 2. Workflow multi-step
```
write-blog-article skill:
research → outline → draft → review → assets → publish
```
El AI sigue el workflow automáticamente, cargando instrucciones por fase.

### 3. Skill de contexto puro
Sin workflow — solo conocimiento de dominio que el agente puede consultar:
- Procesos de notaría francesa
- Ratings de DPE inmobiliario
- Convenciones de arquitectura interna

---

## Ejemplo concreto: Code Review Skill

```yaml
---
name: code-review-checklist
description: Systematic code review focusing on maintainability, performance, and security
triggers: ["code review", "pr review", "review code"]
---

# Code Review Checklist

## Pre-Review Setup
1. Understand the context: What problem is this solving?
2. Check the scope: Is this focused or trying to do too much?
3. Review tests first: Do they clearly express intended behavior?

## Review Categories

### Architecture & Design
- [ ] Does this fit the existing patterns?
- [ ] Are abstractions at the right level?

### Security
- [ ] Input validation on user data?
- [ ] No secrets in code?
- [ ] Authentication/authorization correct?

## Output Format
1. **Overall Assessment**: Approve/Request Changes/Comment
2. **Strengths**: What's done well
3. **Areas for Improvement**: Specific, actionable feedback
```

Resultado: al decir "review this pull request", el AI carga el skill automáticamente y provee feedback estructurado y consistente.

---

## ClawHub — el registry de Skills

Análogo a npm para JavaScript:

```bash
clawhub search "property search"
clawhub install property-search-france
clawhub publish ./my-skill/
```

| Ecosistema | Registry | Paquetes |
|---|---|---|
| JavaScript | npm | Código reutilizable |
| AI agents | ClawHub | Skills reutilizables |

Resuelve los mismos problemas de discovery y distribución que hicieron a npm esencial.

---

## Por qué esto es importante: network effects

1. **Estandarización reduce fricción** → portabilidad → inversión en skills de calidad
2. **Conocimiento composable** → skills referencian otros skills → efecto compuesto
3. **Desarrollo comunitario** → compartir patrones en lugar de reinventarlos
4. **Marketplace potencial** → expertise especializado como modelo de negocio (Legal review skill, Financial analysis skill)

> "The best AI agents won't be the ones with the biggest models — they'll be the ones with the richest skill libraries."

---

## Desafíos

| Desafío | Descripción | Estado |
|---|---|---|
| **Seguridad** | Skills pueden ejecutar código y acceder a datos → sandboxing, permisos, trust | Activo |
| **Calidad** | Skills de baja calidad crean problemas reales (lección de npm) | Activo |
| **Fragmentación** | Formatos aún no idénticos entre plataformas | Disminuyendo |
| **Context window** | Límite duro; mitigado por progressive loading | Manejado |

---

## Próximos pasos en la evolución

- **Versioning**: semver para skills (skills 1.0, 2.0...)
- **Multi-agent collaboration**: agentes especializados cargando skills especializados
- **IDE integration**: instalar skills como extensiones
- **Skill marketplaces**: expertos monetizan conocimiento empaquetado

---

## Conexiones

- [[gelin-ai-framework]] — Skills es uno de los 7 componentes del AI Framework; este artículo es el deep-dive técnico
- [[concepts/agentic-design-patterns]] — Tool Use pattern: los skills son la forma de empaquetar tools y su contexto de uso
- [[concepts/spec-driven-development]] — Skills pueden encapsular workflows SDD completos
- [[concepts/multi-agent-frameworks]] — Orquestador + sub-skills es un patrón multi-agent sin framework pesado
- [[concepts/llm-wiki]] — Skills como LLM Wiki personal/organizacional: conocimiento compilado que el agente consulta
- [[sdd-power-inversion]] — speckit skills (/specify, /plan, /tasks) son exactamente este patrón
