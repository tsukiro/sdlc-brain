---
title: "SDD Practitioner's Guide — Deepak Babu Piskala"
type: source-summary
tags: [sdd, spec-driven-development, bdd, openapi, tdd, ai-coding, workflow, metodología]
sources: []
updated: 2026-05-14
---

# SDD Practitioner's Guide — Deepak Babu Piskala

**Fuente:** Paper académico — Deepak Babu Piskala  
**Subtítulo:** "Spec-Driven Development: A Comprehensive Guide for Practitioners"

## Perspectiva de esta fuente

Paper académico orientado a practicantes que provee el marco conceptual más completo y riguroso sobre SDD como **metodología de desarrollo de software**. Distingue claramente entre tres niveles de rigor de especificación y cuándo aplicar cada uno. Importante: en este paper "SDD" = *development workflow*, no *structured outputs* de LLMs (ver [[concepts/structured-generation]]).

---

## El problema central

> "Code has been king. Requirements drift. Design rots. Tests are afterthoughts. The code becomes the de facto truth."

El *code-first* tiene consecuencias: nuevos devs leen el código para entender qué debe hacer; stakeholders no pueden verificar si el sistema cumple requisitos; los AI coding assistants **adivinan** lo que el desarrollador quiere.

---

## El espectro de especificación (Figura 1)

```
Code-First ←————————————————————————————→ Spec-First

[Ad-hoc]  [Spec-First]  [Spec-Anchored]  [Spec-as-Source]
 Code 1st   Spec guía     Spec + código    Spec ES el código
 docs later  inicial       sincronizados    Nunca editar código
 drift       may abandon   tests enforced   generate only
```

### Nivel 1: Spec-First
- Se escribe la spec antes de codificar para guiar la implementación inicial
- Una vez que existe el código, la spec *puede* abandonarse
- Bajo mantenimiento — aceptable para features de una vez o prototipos
- **Sweet spot para AI coding assistants**: elimina la adivinanza del modelo

### Nivel 2: Spec-Anchored
- La spec se mantiene a lo largo del ciclo de vida del sistema
- Tests automatizados (BDD) aseguran sincronía spec-código: si divergen, el build falla
- La spec es documentación siempre actualizada y confiable
- **El sweet spot para sistemas de producción**; BDD (Cucumber) y API specs (OpenAPI) son los ejemplos canónicos

### Nivel 3: Spec-as-Source
- La spec es el **único artefacto que editan los humanos**; el código se genera y jamás se edita manualmente
- Requiere tooling de generación maduro y confiable
- Ya estándar en dominios específicos: OpenAPI → server stubs, Simulink → C certificado para automoción
- Herramientas emergentes: **Tessl** (general SW), **Amazon Kiro**

---

## El workflow SDD (Figura 2): Specify → Plan → Implement → Validate

### Fase 1: Specify — "¿Qué debe hacer?"
Produce una especificación funcional de comportamiento, requisitos y criterios de aceptación. Sin detalles de implementación ("qué" no "cómo").

**Características de una buena spec:**
- Behavior-focused, no implementación
- Testable: cada requisito es verificable
- Unambiguous: todos los lectores llegan a la misma interpretación
- Complete enough: cubre casos esenciales sin sobre-especificar

> "Write specs at the level of detail needed to remove ambiguity."

### Fase 2: Plan — "¿Cómo construirlo?"
Dado el spec funcional, produce un plan técnico: arquitectura, modelos de datos, interfaces, tecnologías. Codifica restricciones que la implementación debe respetar.

### Fase 3: Implement — "Construirlo"
Trabajar en incrementos pequeños y validados. Las specs actúan como "super-prompts" que descomponen problemas complejos en componentes modulares alineados con los context windows de los agentes.

### Fase 4: Validate — "¿Cumple la spec?"
Tests automatizados + escenarios BDD + review stakeholder. Si el código no cumple la spec → fix código. Si la spec era incorrecta → actualizar spec con acuerdo explícito.

---

## SDD y AI coding agents

> Empirical studies suggest human-refined specs reduce LLM-generated code errors by up to **50%**.

**Por qué las specs mejoran los LLMs:**
- Sin spec: el modelo adivina formato, permisos, límites de tamaño, storage — resultados inconsistentes (*"vibe coding"*)
- Con spec: el modelo tiene suficiente información para generar código que coincide con la intención

**Escalabilidad:** specs permiten ejecución paralela de agentes en tareas no superpuestas. Múltiples agentes implementan componentes simultáneamente sin interferencia.

**Self-spec method:** el LLM primero produce una spec desde un prompt de alto nivel → humano revisa y refina → el mismo u otro agente implementa contra ella. Separa explícitamente planificación de ejecución.

---

## Herramientas y frameworks (Tabla I)

| Categoría | Ejemplos | Rol en SDD |
|---|---|---|
| BDD Frameworks | Cucumber, SpecFlow, Behave | Specs ejecutables en lenguaje natural (Gherkin) |
| TDD Frameworks | RSpec, JUnit, pytest | Specs como unit tests |
| API Specification | OpenAPI/Swagger, GraphQL SDL, Protocol Buffers, AsyncAPI | Contratos; generan código y tests |
| Contract Testing | Pact, Specmatic | Verifican que implementaciones cumplan specs |
| AI-Assisted SDD | **GitHub Spec Kit**, **Amazon Kiro**, **Tessl** | Workflows AI estructurados spec→code |
| Model-Based Design | Simulink, SCADE | Specs visuales que generan código embebido |

### GitHub Spec Kit
Cuatro fases: `/specify` → `/plan` → `/tasks` → implementación. Revisión humana en cada fase.

### Amazon Kiro
Guía por requirements, design y task creation antes de cualquier generación de código.

### Tessl
Spec-as-source radical: specs son el artefacto mantenido; código se regenera. "Specs as the new source code."

---

## Casos de estudio

### CS1: API-First Microservices (Fintech)
- Problema: "integration hell" — incompatibilidades solo visibles en integration testing
- Solución: OpenAPI specs antes de cualquier código; Specmatic para mock servers + contract tests en CI
- Resultado: **75% reducción en cycle time** para cambios de API

### CS2: BDD para Enterprise (Project Management SW)
- Problema: desacuerdos sobre qué significa "done"; interpretaciones múltiples
- Solución: Cucumber para features; product managers escriben Gherkin; feature "done" = todos los escenarios pasan
- Resultado: lenguaje compartido; spec es la autoridad

### CS3: Model-Based Embedded (Automoción)
- Problema: certificación ISO 26262; trazabilidad código ↔ requisitos; manual = error-prone
- Solución: Simulink modela algoritmos; código C generado automáticamente por generador certificado; **nadie edita el código generado**
- Resultado: spec-as-source canónico; si cambia la lógica → cambia el modelo → regenerar

---

## Cuándo usar SDD (árbol de decisión)

**Usar SDD cuando:**
- Se usan AI coding assistants (specs eliminan la adivinanza)
- Requisitos complejos que stakeholders deben validar antes de codificar
- Múltiples maintainers (docs que sobreviven la rotación)
- Sistemas con muchas integraciones (API specs + parallel development)
- Dominios regulados (trazabilidad requisito → implementación)
- Modernización legacy (spec del comportamiento existente antes de reimplementar)

**SDD es overkill cuando:**
- Prototipos desechables
- Proyectos solo → corto plazo
- Exploración (se desconoce lo que se construye)
- CRUD trivial con requisitos obvios

> **The Golden Rule:** Use the minimum level of specification rigor that removes ambiguity for your context.

---

## SDD vs documentos de diseño tradicionales (HLD/LLD)

| Dimensión | Documentos tradicionales | SDD |
|---|---|---|
| **Naturaleza** | Advisory — guían, no obligan | Enforced — tests fallan si código diverge |
| **Drift** | Frecuente (stale by Sprint 3) | Detectado automáticamente en CI |
| **CI/CD integration** | No | Sí — spec validation en cada commit |
| **AI consumption** | Para humanos | Estructuradas para ser consumidas por AI agents |

> Bryan Finster: "SDD is not a revolution… it's just BDD with branding." — Pero el branding importa: recuerda que las specs deben ser autoritativas, no advisory.

---

## Pitfalls comunes

| Pitfall | Descripción | Solución |
|---|---|---|
| **Over-specification** | Spec se convierte en pseudo-código | Mantener separación "qué" vs "cómo" |
| **Specification rot** | Spec diverge del código sin detección | Tests automáticos que fallen si divergen |
| **Burocracia** | Specs = formularios, no herramientas | Mínimo necesario para eliminar ambigüedad |
| **Tooling complexity** | Se ahogan en artefactos generados | Empezar simple; añadir complejidad solo si ayuda |
| **False confidence** | Test pasa ≠ software correcto | La spec también puede estar mal; requiere revisión humana |

---

## Conexiones

- [[concepts/spec-driven-development]] — concepto sintetizado de SDD como metodología
- [[concepts/structured-generation]] — **diferente**: SDD como constrained decoding (no confundir)
- [[jxnl-pipelines]] — filosofía complementaria: LLM pipelines = software engineering
- [[langsmith-evaluation]] — evaluación de agentes en el contexto de validate phase
- [[concepts/langgraph]] — implementar SDD con LangGraph para routing y orquestación
- [[entities/toon]] — TOON como formato para prompts en pipelines SDD
