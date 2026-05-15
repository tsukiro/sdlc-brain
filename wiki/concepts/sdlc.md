---
title: "SDLC — Software Development Lifecycle"
type: concept
tags: [sdlc, metodología, fases, modelos, fundamentos]
sources: [2026-05-13_ibm-sdlc.md, 2026-05-13_aws-sdlc.md, 2026-05-13_atlassian-sdlc.md]
updated: 2026-05-13
---

# SDLC — Software Development Lifecycle

## Definición

El SDLC es un **proceso estructurado y repetible** que los equipos de desarrollo usan para planificar, construir, probar, desplegar y mantener software de alta calidad de forma costo-efectiva. Su propósito central es minimizar riesgos mediante planificación anticipada y proveer un mapa de ruta que satisfaga las necesidades de stakeholders, requisitos del proyecto y expectativas del cliente.

Las tres fuentes coinciden en la esencia:
- **IBM**: "metodología estructurada e iterativa para construir, entregar y mantener sistemas de alta calidad"
- **AWS**: "proceso rentable y eficiente para diseñar y construir software de alta calidad"
- **Atlassian**: "proceso estructurado para planificar, construir, probar y mantener software"

---

## Fases

### Comparación entre fuentes

| # | AWS (6 fases)    | Atlassian (7 fases)         | Equivalencia                              |
|---|------------------|-----------------------------|-------------------------------------------|
| 1 | Plan             | Planning                    | Idénticas                                 |
| — | *(incluido en Plan)* | Feasibility Analysis   | Atlassian lo separa; AWS lo absorbe en Plan |
| 2 | Design           | System Design               | Idénticas                                 |
| 3 | Implement        | Implementation              | Idénticas                                 |
| 4 | Test             | Testing                     | Idénticas                                 |
| 5 | Deploy           | Deployment                  | Idénticas                                 |
| 6 | Maintain         | Maintenance                 | Idénticas                                 |

> **Tensión / Contradicción:** AWS enumera 6 fases y Atlassian enumera 7. La diferencia real es que Atlassian trata el **Análisis de Factibilidad** como una fase autónoma que AWS incorpora dentro de *Plan*. IBM no enumera fases específicas en su artículo principal. Esto no es una contradicción conceptual sino una diferencia de granularidad.

### Descripción de cada fase (síntesis AWS + Atlassian)

1. **Plan** — Análisis costo-beneficio, estimación de recursos, recopilación de requisitos, especificaciones
2. **Feasibility Analysis** *(Atlassian)* — Evaluación de viabilidad técnica, económica y operacional
3. **Design** — Análisis de requisitos, selección de arquitectura y módulos, diseño técnico
4. **Implement** — Escritura de código; los devs trabajan según tareas diarias
5. **Test** — Pruebas automatizadas y manuales; verificación de cumplimiento de requisitos
6. **Deploy** — Empaquetamiento y traslado a producción
7. **Maintain** — Corrección de errores, monitoreo de desempeño, soporte a usuarios

---

## Modelos

Todos los modelos son formas distintas de **organizar y secuenciar** las fases del SDLC. No cambian las fases en sí, sino cómo se relacionan entre ellas.

| Modelo            | Estructura       | Mejor para                              | Fuentes    |
|-------------------|------------------|-----------------------------------------|------------|
| [[waterfall]]     | Secuencial       | Proyectos pequeños, requisitos estables | IBM, AWS, Atlassian |
| [[agile]]         | Iterativa/sprints| Proyectos complejos, requisitos cambiantes | IBM, AWS, Atlassian |
| [[v-model]]       | Secuencial + testing por fase | Software con requisitos estables que requiere pruebas frecuentes | IBM |
| [[iterative-model]] | Versiones sucesivas | Gestión de riesgos flexible         | AWS        |
| [[spiral]]        | Ciclos + análisis de riesgo | Proyectos grandes y complejos   | AWS        |

---

## Distinciones conceptuales importantes

*(Exclusivo de AWS — no cubierto por IBM ni Atlassian)*

- **SDLC** ≠ **Systems Development Lifecycle**: el segundo es más amplio e incluye hardware, procesos y cambios organizacionales, no solo software.
- **SDLC** ⊂ **ALM** (Application Lifecycle Management): ALM es el superconjunto que abarca todo el ciclo de vida de la aplicación, incluyendo períodos posteriores al desarrollo activo.

---

## Beneficios (síntesis de las tres fuentes)

- Estructura estandarizada y proceso repetible → consistencia en la calidad
- Mayor visibilidad para stakeholders en cada etapa
- Planificación y estimación más eficiente
- Gestión proactiva de riesgos (identificados temprano antes de que escalen)
- Mejor control de costos
- Énfasis en testing y QA como parte integral del proceso

---

## SDLC potenciado por IA

La integración de herramientas IA en el SDLC transforma cada fase con distinta profundidad y madurez:

| Fase | Impacto de IA | Madurez (2025) |
|---|---|---|
| **Requisitos** | NLP sobre user stories, extracción automática, priorización de backlog | Media |
| **Diseño** | Sugerencia de arquitecturas y patrones; generación de diagramas | Media-baja |
| **Implementación** | AI coding assistants (Copilot, etc.): generación, refactoring, documentación | Alta |
| **Testing** | Auto-generación de casos de prueba, anomaly detection en QA | Media |
| **Deployment** | Predicción de fallos, optimización de pipelines CI/CD | Media |
| **Monitoring** | Detección de anomalías, respuesta automática a incidentes | Media |

**Regla de adopción incremental (Snyk, 2025):** piloto (2-3 devs / 30 días) → evaluación → expansión → escala. Las herramientas de code completion tienen integración de 1-2 semanas; architecture planning requiere 8-12 semanas.

**Complejidad de integración:**
- Code completion: Baja / 1-2 semanas
- Code review: Media / 3-4 semanas
- Testing automation: Media / 4-6 semanas
- Architecture planning: Alta / 8-12 semanas

**Outlook (Gartner 2024):** 75% de engineers enterprise usarán AI coding assistants para 2028. La IA no reemplaza a los devs — los devs pasan a ser "conductores de IA" que aportan juicio contextual y ético.

**Desafíos principales:** secret leakage en código generado, deuda técnica por generación sin revisión, sobredependencia y pérdida de habilidades fundamentales.

Ver [[snyk-ai-sdlc]] para guía completa de implementación, tabla comparativa y outlook 2040.

---

## SDLC y Seguridad

Las prácticas [[devsecops]] integran seguridad en cada fase del SDLC en lugar de tratarla como un paso final. Incluye revisión de código, análisis arquitectónico y pruebas de penetración. *(Cubierto principalmente por AWS)*.

Con IA, la seguridad se vuelve **continua y transversal**: escaneo de vulnerabilidades mientras se escribe el código (no solo en reviews), detección automática de secrets, y reconocimiento de anti-patterns. Nuevo riesgo: prompt injection en IDEs de codificación IA.

---

## Conexiones

- [[waterfall]] — modelo secuencial clásico
- [[agile]] — modelo iterativo moderno con frameworks
- [[v-model]] — variante de Waterfall con testing paralelo por fase
- [[iterative-model]] — mejoras progresivas en versiones
- [[spiral]] — ciclos con análisis de riesgo continuo
- [[devsecops]] — integración de seguridad en el SDLC
- [[snyk-ai-sdlc]] — SDLC potenciado por IA: comparativa por fase, implementación incremental, seguridad, outlook 2028-2040
- [[analyses/sdlc-ia-flujo-recomendado]] — síntesis de SDLC con IA fase a fase
- [[ibm-sdlc]] — fuente IBM
- [[aws-sdlc]] — fuente AWS (más operacional, incluye distinciones SDLC/ALM)
- [[atlassian-sdlc]] — fuente Atlassian (más centrada en Agile y frameworks)
