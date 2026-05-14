---
title: "SDD — The Power Inversion (filosofía y comandos speckit)"
type: source-summary
tags: [sdd, spec-driven-development, speckit, ai-coding, filosofía, workflow]
sources: []
updated: 2026-05-14
---

# SDD — The Power Inversion

**Fuente:** Artículo/blog — anónimo (contexto speckit)

## Perspectiva de esta fuente

Documento que articula la filosofía detrás de SDD como **inversión de poder**: las specs no sirven al código, el código sirve a las specs. También documenta en detalle los comandos `/speckit.specify`, `/speckit.plan`, y `/speckit.tasks` como implementación práctica de este paradigma.

---

## La inversión de poder

> "Spec-Driven Development inverts the power structure. Specifications don't serve code — code serves specifications."

**Antes (code-centric):**
- El código es la verdad. Los specs son scaffolding que se descarta.
- Mantener el software = modificar código; los docs se vuelven artefactos históricos no confiables.
- El gap entre spec e implementación es inevitable; solo se intenta reducir, nunca eliminar.

**Con SDD:**
- La spec es el artefacto primario. El código es su expresión en un lenguaje particular.
- Mantener software = evolucionar specs. El código se regenera.
- El gap se elimina *por construcción*: si el código viene de la spec, no puede divergir.

> "The specification becomes the primary artifact. Code becomes its expression in a particular language and framework."

---

## Por qué SDD es posible ahora — 3 tendencias

1. **Capacidades AI** han alcanzado el umbral donde specs en lenguaje natural generan código funcional de manera confiable
2. **Complejidad del software** crece exponencialmente — mantener alineación manual se vuelve imposible
3. **Velocidad del cambio** — los pivots son la norma. SDD transforma cambios de requisito en regeneraciones sistemáticas, no en reescrituras manuales

---

## Principios core de SDD (según este documento)

| Principio | Descripción |
|---|---|
| **Specs como Lingua Franca** | La spec es el artefacto primario; el código, su expresión |
| **Specs ejecutables** | Precisas, completas, sin ambigüedad suficiente para generar sistemas funcionales |
| **Refinamiento continuo** | Validación de consistencia = proceso continuo, no un gate único |
| **Research-driven context** | Agentes de research recopilan contexto técnico durante el proceso de spec |
| **Bidirectional feedback** | Métricas de producción, incidentes y aprendizajes → inputs para refinar specs |
| **Branching para exploración** | Múltiples implementaciones desde la misma spec para explorar trade-offs |

---

## Los comandos speckit

### `/speckit.specify`
Transforma una descripción de feature en un spec estructurado completo:
1. Escanea specs existentes → asigna número secuencial (001, 002, 003...)
2. Crea branch semántico automáticamente
3. Copia y personaliza template de spec con los requisitos
4. Crea estructura `specs/[branch-name]/`

### `/speckit.plan`
Desde el spec, crea el plan de implementación:
1. Lee y comprende requisitos, user stories y criterios de aceptación
2. Verifica alineación con principios arquitecturales del proyecto (constitution)
3. Traduce requisitos de negocio en arquitectura técnica
4. Genera documentos de soporte: data models, API contracts, test scenarios
5. Produce quickstart guide con escenarios clave de validación

### `/speckit.tasks`
Desde el plan, genera lista de tareas ejecutables:
1. Inputs: `plan.md` (requerido) + `data-model.md`, `contracts/`, `research.md` (si existen)
2. Convierte contratos, entidades y escenarios en tareas específicas
3. Marca tareas independientes como `[P]` (paralelizables)
4. Output: `tasks.md` en el directorio del feature

### Ejemplo: Chat system en 15 minutos vs 12 horas

```bash
# Enfoque tradicional: ~12 horas de documentación

# Enfoque SDD con speckit: ~15 minutos
/speckit.specify Real-time chat system with message history and user presence
# → Crea branch "003-chat-system"
# → Genera specs/003-chat-system/spec.md

/speckit.plan WebSocket for real-time messaging, PostgreSQL for history, Redis for presence
# → specs/003-chat-system/plan.md
# → specs/003-chat-system/research.md (comparación librerías WebSocket)
# → specs/003-chat-system/data-model.md
# → specs/003-chat-system/contracts/ (WebSocket events, REST endpoints)
# → specs/003-chat-system/quickstart.md

/speckit.tasks
# → specs/003-chat-system/tasks.md
```

En 15 minutos: spec completa con user stories, plan de implementación con tecnologías justificadas, contratos API y modelos de datos, escenarios de test, todo versionado en un feature branch.

---

## Cómo los templates guían al LLM hacia specs de mejor calidad

Los templates actúan como "prompts sofisticados" que restringen el output del LLM productivamente:

1. **Previenen detalles de implementación prematuros** — el template indica "❌ Avoid HOW to implement (no tech stack)"
2. **Fuerzan marcadores de incertidumbre** — `[NEEDS CLARIFICATION: specific question]` en lugar de suposiciones
3. **Checklists como unit tests de la spec** — la spec se autoverifica contra criterios de completud
4. **Constitutional compliance gates** — Phase -1 checks antes de implementar (Simplicity Gate, Anti-Abstraction Gate)
5. **Gestión jerárquica de detalle** — el plan permanece legible; el código de muestra va a `implementation-details/`
6. **Test-first thinking** — el orden de creación de archivos: contracts/ → tests → source
7. **Prevención de features especulativas** — todo feature debe trazar a una user story concreta

---

## La Constitución: principios arquitecturales inmutables

El documento introduce el concepto de `memory/constitution.md` — los "9 artículos" de principios inmutables que gobiernan cómo las specs se convierten en código:

| Artículo | Principio |
|---|---|
| I | Library-First: todo feature empieza como librería independiente |
| II | CLI Interface Mandate: toda librería expone CLI (stdin→stdout, JSON) |
| III | Test-First Imperative: NUNCA código antes de tests aprobados y en fase RED |
| VII | Simplicity Gate: máximo 3 proyectos iniciales; documentar cualquier complejidad |
| VIII | Anti-Abstraction Gate: usar framework directamente, sin wrappers innecesarios |
| IX | Integration-First Testing: preferir BD real sobre mocks; contract tests obligatorios |

**Por qué la constitución importa:** garantiza que código generado hoy y código generado el año siguiente sean arquitectónicamente compatibles, aunque se usen diferentes LLMs.

---

## Conexiones

- [[concepts/spec-driven-development]] — concepto principal de SDD como metodología
- [[sdd-piskala-paper]] — paper académico que formaliza el mismo paradigma
- [[concepts/structured-generation]] — SDD como constrained decoding (diferente uso del término)
- [[jxnl-pipelines]] — filosofía complementaria sobre LLM pipelines como software engineering
- [[concepts/agentic-design-patterns]] — self-spec method como extensión del patrón Reflection
