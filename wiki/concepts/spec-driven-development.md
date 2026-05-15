---
title: "Spec-Driven Development (SDD) — Metodología"
type: concept
tags: [sdd, spec-driven-development, bdd, tdd, metodología, ai-coding, workflow, especificación]
sources: []
updated: 2026-05-14
---

# Spec-Driven Development (SDD) — Metodología

> [!info]
> **Distinción importante:** "SDD" tiene dos usos distintos en este vault. Esta página cubre SDD como **metodología de desarrollo** (workflow y filosofía). Para SDD como *constrained decoding* de outputs de LLMs, ver [[concepts/structured-generation]].

---

## Tesis central

> "The specification becomes the source of truth and code is a generated or verified secondary artifact."

SDD invierte la jerarquía tradicional: el código no es la verdad — la especificación lo es. El código es la expresión de la spec en un lenguaje particular.

**El problema que resuelve:**
- En desarrollo code-first, los specs se crean y luego *se abandonan*. El código diverge. La documentación rota.
- Cuando un AI coding assistant recibe "Agrega photo sharing a mi app", adivina: ¿qué formato? ¿qué permisos? ¿qué límites de tamaño?
- Con una spec: "JPEG/PNG hasta 10MB, almacenados en S3 con claves prefijadas por user-ID, solo el uploader puede borrar, resize a 1024px max" → el agente puede generar código que coincide con la intención.

---

## El espectro de rigor (Piskala, 2025)

```
Code-First ←————————————————————————————→ Spec-First

[Ad-hoc]   [Spec-First]  [Spec-Anchored]  [Spec-as-Source]
 código 1°   spec guía      spec+código       spec ES el código
 docs after  inicial        sincronizados     jamás editar código
 drift       may abandon    CI enforced       generate only
```

### Nivel 1 — Spec-First
La spec se escribe antes de codificar para guiar la implementación inicial. Post-implementación, el código puede seguir siendo el artefacto primario.

- **Cuándo usar:** features con AI coding assistants, prototipos, one-off
- **Costo:** bajo (no hay mantenimiento continuo)
- **Riesgo:** drift a largo plazo

### Nivel 2 — Spec-Anchored *(sweet spot para producción)*
La spec se mantiene sincronizada con el código durante todo el ciclo de vida. Cambiar el comportamiento = actualizar spec + código. Los tests (BDD, contract tests) fallan si divergen.

- **Cuándo usar:** sistemas de producción, APIs con múltiples consumidores, equipos múltiples
- **Herramientas:** Cucumber (BDD), OpenAPI + Specmatic (APIs), Pact (contratos)
- **Costo:** moderado (disciplina + tooling)

### Nivel 3 — Spec-as-Source
La spec es el **único artefacto que editan los humanos**. El código se genera y nunca se edita manualmente. Drift = imposible por construcción.

- **Cuándo usar:** dominios con generación madura y confiable
- **Ejemplos actuales:** OpenAPI → server stubs, Simulink → C certificado (automotive ISO 26262)
- **Herramientas emergentes:** Tessl, Amazon Kiro
- **Costo:** alto (requiere generación madura y confiable)

---

## El workflow SDD: Specify → Plan → Implement → Validate

### Fase 1: Specify — *¿Qué debe hacer?*
Produce spec funcional: comportamiento, requisitos, acceptance criteria. Sin implementación.

**Características de una buena spec:**
- **Behavior-focused** — "qué", no "cómo"
- **Testable** — cada requisito es verificable
- **Unambiguous** — todos los lectores llegan a la misma interpretación
- **Minimum necessary** — elimina ambigüedad sin sobre-especificar

### Fase 2: Plan — *¿Cómo construirlo?*
Arquitectura, modelos de datos, interfaces, tecnologías, restricciones. Codifica el "cómo" dentro del "qué" de la spec.

### Fase 3: Implement — *Construirlo*
Incrementos pequeños validados. En SDD con AI, la spec actúa como "super-prompt" que reduce el non-determinismo del modelo.

> Empirical evidence: human-refined specs reduce LLM-generated code errors by up to **50%** (Piskala, 2025).

### Fase 4: Validate — *¿Cumple la spec?*
Tests automatizados + escenarios BDD + review. Si el código no cumple → fix código. Si la spec estaba mal → actualizar spec con acuerdo explícito. La spec sigue siendo la autoridad.

---

## SDD vs documentos de diseño tradicionales

| Dimensión | Docs tradicionales (HLD/LLD) | SDD |
|---|---|---|
| **Naturaleza** | Advisory — guían, no obligan | **Enforced** — tests fallan si divergen |
| **Drift** | Frecuente (stale by Sprint 3) | Detectado en CI automáticamente |
| **CI/CD** | No integrado | Spec validation en cada commit |
| **AI consumption** | Para lectores humanos | Estructurado para ser consumido por agentes |

> "Traditional specs are advisory. SDD specs are enforced." — El código ya existía con HLD; lo nuevo es la enforcement automática.

---

## SDD como catalizador para AI coding agents

### El problema del "vibe coding"
Sin spec, el agente adivina con prompts vagos → outputs inconsistentes y con suposiciones incorrectas.

### Specs como super-prompts
Las specs descomponen problemas complejos en componentes modulares alineados con los context windows de los agentes. Permiten:
- **Ejecución paralela** de múltiples agentes en tareas no superpuestas
- **Self-spec method:** el LLM primero genera la spec → humano revisa → el agente implementa. Separa planificación de ejecución.
- **Semantic routing infalible** (con [[concepts/structured-generation]]): si el router produce un Enum, no puede alucinar.

---

## Herramientas por categoría

### BDD Frameworks (Spec-Anchored)
- **Cucumber** (Ruby/Java/JS) — Gherkin: Given/When/Then
- **SpecFlow** → Reqnroll (.NET)
- **Behave** (Python)

```gherkin
Feature: Shopping Cart
  Scenario: Adding item to empty cart
    Given the cart is empty
    When I add item "Widget" to the cart
    Then the cart should contain 1 item
```

### API Specification Tools (Spec-Anchored o Spec-as-Source)
- **OpenAPI/Swagger** — REST APIs; genera stubs, SDKs, docs
- **GraphQL SDL** — schema como contrato frontend-backend
- **Protocol Buffers + gRPC** — tipado fuerte, generación de código
- **AsyncAPI** — event-driven architectures

### Contract Testing
- **Pact** — verifica que consumidores y proveedores cumplen contratos
- **Specmatic** — mock servers desde specs + validación en CI

### AI-Assisted SDD Tools
- **GitHub Spec Kit** — `/specify` → `/plan` → `/tasks` → implementación
- **Amazon Kiro** — requirements → design → tasks antes de cualquier código
- **Tessl** — spec-as-source radical; "specs as the new source code"
- **BMAD Method** — 5 agentes especializados (BA, PM, Architect, Developer, QA); output de cada agente alimenta al siguiente

### Técnica: Interview Mode para generación de specs

En lugar de escribir la spec desde cero, pedir al AI que te entreviste:

```markdown
read this @SPEC.md and interview me in detail about literally anything:
technical implementation, UI & UX, concerns, tradeoffs, etc.
but make sure the questions are not obvious

be very in-depth and continue interviewing me continually until it's complete,
then write the spec to the file
```

El AI hace preguntas profundas sobre edge cases, decisiones técnicas y UX → genera una spec completa desde la conversación. Separa el "saber qué se quiere" (humano) del "saber cómo formalizarlo" (AI).

---

## Árbol de decisión: ¿qué nivel usar?

```
¿Nuevo feature?
└── ¿Usa AI coding assistants o requisitos complejos?
    ├── No → Ad-hoc está bien
    └── Sí → ¿Sistema de larga duración o múltiples maintainers?
              ├── No → Spec-First
              └── Sí → ¿La generación de código es madura y confiable?
                        ├── No → Spec-Anchored ← sweet spot producción
                        └── Sí → Spec-as-Source
```

**The Golden Rule:** *"Use the minimum level of specification rigor that removes ambiguity for your context."*

---

## Pitfalls a evitar

| Pitfall | Solución |
|---|---|
| **Over-specification** (spec = pseudo-código) | Mantener separación "qué" vs "cómo" |
| **Specification rot** (spec diverge del código) | Tests automáticos que fallen si divergen |
| **Burocracia** (specs = formularios) | Mínimo necesario para eliminar ambigüedad |
| **False confidence** (test pasa ≠ software correcto) | La spec también puede estar mal; revisión humana obligatoria |

## Desafíos de SDD en producción (Isenberg, 2026)

Evidencia de adopción real con AI coding agents en entornos enterprise:

| Desafío | Descripción |
|---|---|
| **Overhead de proceso** | Specs demasiado largas → devs las evitan; requiere fine-tuning continuo del peso del proceso |
| **Selección de herramienta** | Tools son muy opinionated (spec-first vs. spec-anchored); difícil standardizar y cambiar de tool |
| **Review de specs > review de código** | Archivos `.md` son menos auditables; feedback más lento y de menor calidad |
| **Agentes ignoran specs** | AI no es 100% determinista; puede reinterpretar partes de la spec → validación humana continua esencial |
| **Overkill para tareas pequeñas** | Best practices de cuándo usar SDD no están claras; requiere trial-and-error por equipo |

**Regla emergente**: el sub-flujo SDD por etapa es **Plan → Validar con stakeholders → Ejecutar**. Esta estructura crea resultados predecibles y permite feedback temprano. Sin ella, los agentes optimizan para velocidad, no para intención de negocio.

**Nuevo tool mencionado**: [BMAD Method](https://github.com/bmad-code-org/BMAD-METHOD) — junto a Kiro y Spec-It/speckit, como implementación de SDD con agentes.

---

## Relación con prácticas existentes

| Práctica | Relación con SDD |
|---|---|
| **TDD** | SDD a nivel de unit — escribir test primero = micro-spec |
| **BDD** | Ancestro directo; Gherkin = specs ejecutables. SDD añade generación AI |
| **DDD** | Ubiquitous language → bases para specs significativas para todos |
| **Agile** | User stories + Acceptance criteria = specs; SDD las hace autoritativas, no advisory |

> Bryan Finster: "SDD is not a revolution… it's just BDD with branding." — Lo nuevo: tooling, CI/CD enforcement y AI como consumidor de specs.

---

## Tensiones

- **Spec completud vs velocidad inicial:** escribir specs toma tiempo; el beneficio se acumula en el ciclo de vida del sistema
- **Spec-anchored mantenimiento:** requiere disciplina real; sin enforcement automático, se convierte en docs tradicionales
- **False confidence:** una spec que pasa todos los tests puede estar especificando el comportamiento incorrecto

---

## Conexiones

- [[sdd-piskala-paper]] — paper académico fundacional de este concepto
- [[sdd-power-inversion]] — filosofía SDD + comandos speckit
- [[isenberg-ai-sdlc]] — SDD como fundamento del AI-SDLC gobernado; desafíos de producción con AI agents
- [[concepts/structured-generation]] — **diferente**: SDD como constrained decoding (outputs LLM)
- [[concepts/agentic-design-patterns]] — SDD + patterns agénticos (self-spec = Reflection pattern)
- [[jxnl-pipelines]] — filosofía complementaria: specs son la ingeniería de software correcta
- [[langsmith-evaluation]] — la fase Validate de SDD → evaluación de agentes
- [[concepts/multi-agent-frameworks]] — LangGraph/CrewAI para implementar el workflow SDD
- [[entities/toon]] — TOON como formato para prompts en pipelines SDD
