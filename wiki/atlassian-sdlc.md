---
title: "Atlassian — What is SDLC? Complete Guide"
type: source-summary
tags: [sdlc, atlassian, agile, scrum, kanban, lean, xp, waterfall, jira]
sources: [2026-05-13_atlassian-sdlc.md]
updated: 2026-05-13
---

# Atlassian — What is SDLC? Complete Guide

**Fuente:** https://www.atlassian.com/agile/software-development/sdlc

## Puntos clave

Atlassian ofrece la perspectiva **más centrada en Agile** de las tres fuentes, lo cual tiene sentido dado su ecosistema (Jira, Confluence). Es la única fuente que desglosa los frameworks Agile (Scrum, Kanban, Lean, XP) y la única que incluye **Feasibility Analysis** como fase separada.

### Definición Atlassian

> "Proceso estructurado para planificar, construir, probar y mantener software con el fin de asegurar calidad y alineación con objetivos de negocio."

### Las 7 fases Atlassian

1. **Planning** — definición del alcance y objetivos
2. **Feasibility Analysis** — evaluación de viabilidad técnica, económica y operacional *(fase no listada por AWS ni IBM)*
3. **System Design** — arquitectura y diseño técnico
4. **Implementation** — desarrollo/codificación
5. **Testing** — validación y QA
6. **Deployment** — liberación a producción
7. **Maintenance** — soporte y evolución

> **Tensión con AWS:** AWS enumera 6 fases (Plan → Design → Implement → Test → Deploy → Maintain). La diferencia principal: Atlassian trata *Feasibility Analysis* como fase autónoma que AWS incorpora dentro de *Plan*. Ver [[concepts/sdlc#fases]].

### Waterfall según Atlassian

- Proceso de 5 etapas: requirements → design → implementation → verification → maintenance
- Recomendado para proyectos **pequeños y bien definidos**

### Agile según Atlassian

- Flexible, iterativo, basado en sprints
- Énfasis en: colaboración, adaptabilidad, feedback del cliente
- Recomendado para proyectos **grandes y complejos** con requisitos cambiantes

### Frameworks Agile (exclusivo de Atlassian)

| Framework               | Característica principal                                 |
|-------------------------|----------------------------------------------------------|
| **Scrum**               | Sprints con roles definidos (PO, SM, Dev Team)           |
| **Kanban**              | Flujo continuo, visualización en tablero                 |
| **Lean**                | Eliminar desperdicio, optimizar flujo                    |
| **Extreme Programming** | TDD, pair programming, integración continua              |

## Conexiones

- [[concepts/sdlc]] — página de síntesis principal
- [[concepts/agile]] — modelo Agile (Atlassian es fuente primaria para frameworks)
- [[concepts/waterfall]] — modelo Waterfall
- [[entities/jira]] — herramienta de SDLC recomendada por Atlassian
- [[ibm-sdlc]] — perspectiva IBM
- [[aws-sdlc]] — perspectiva AWS
