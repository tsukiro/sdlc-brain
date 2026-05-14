---
title: "Modelo Agile"
type: concept
tags: [sdlc, agile, iterativo, sprints, scrum, kanban, lean, xp]
sources: [2026-05-13_ibm-sdlc.md, 2026-05-13_aws-sdlc.md, 2026-05-13_atlassian-sdlc.md]
updated: 2026-05-13
---

# Modelo Agile

## Descripción

Agile es un enfoque **iterativo e incremental** del SDLC donde el desarrollo ocurre en ciclos cortos llamados *sprints*. En lugar de planificar todo al inicio y entregar al final, Agile entrega valor continuamente y adapta el plan en cada iteración.

## Características

- Desarrollo en **sprints** (ciclos cortos, típicamente 1-4 semanas)
- Entregas pequeñas e incrementales de software funcional
- Retroalimentación continua del cliente
- Adaptación constante a cambios de requisitos
- Énfasis en colaboración entre el equipo y con el cliente

## Cuándo usarlo

> "Proyectos grandes y complejos que requieren cambios frecuentes y colaboración" — Atlassian

- Cliente disponible para participar en revisiones frecuentes (IBM)
- Requisitos que evolucionan o no están completamente definidos
- Equipos que valoran la adaptabilidad sobre la predictibilidad

## Ventajas (según AWS)

- Identifica problemas temprano (cada sprint revela issues)
- Entrega valor antes: el cliente recibe software funcional desde los primeros sprints
- Alta adaptabilidad a cambios de requisitos

## Desventajas (según AWS)

- Riesgo de exceso de cambios por feedback continuo (scope creep)
- Difícil de estimar costos y plazos totales al inicio
- Requiere alta participación del cliente, que no siempre está disponible

## Frameworks Agile

*(Exclusivo de Atlassian — las otras fuentes no detallan los frameworks)*

| Framework                    | Característica principal                                  |
|------------------------------|-----------------------------------------------------------|
| **Scrum**                    | Sprints con roles definidos: Product Owner, Scrum Master, Development Team |
| **Kanban**                   | Flujo continuo de trabajo; visualización en tablero       |
| **Lean**                     | Eliminar desperdicio, optimizar el flujo de valor         |
| **Extreme Programming (XP)** | TDD, pair programming, integración continua, releases frecuentes |

## Comparación con modelos alternativos

- **vs [[waterfall]]**: Waterfall es secuencial y asume requisitos fijos; Agile es adaptativo y acepta cambios
- **vs [[iterative-model]]**: Iterativo también trabaja en versiones, pero Agile agrega ceremonias, roles y el concepto de sprint
- **vs [[spiral]]**: Spiral también es iterativo pero se centra en análisis de riesgo por ciclo; Agile se centra en entrega de valor y feedback del cliente

## Conexiones

- [[sdlc]] — concepto padre
- [[waterfall]] — alternativa secuencial
- [[iterative-model]] — modelo relacionado también iterativo
- [[spiral]] — modelo también iterativo pero con foco en riesgo
- [[ibm-sdlc]] — perspectiva IBM
- [[aws-sdlc]] — perspectiva AWS (tabla comparativa)
- [[atlassian-sdlc]] — fuente primaria para frameworks Agile
