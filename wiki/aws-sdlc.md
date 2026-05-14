---
title: "AWS — What is SDLC?"
type: source-summary
tags: [sdlc, aws, fases, devsecops, alm, modelos, herramientas]
sources: [2026-05-13_aws-sdlc.md]
updated: 2026-05-13
---

# AWS — What is SDLC?

**Fuente:** https://aws.amazon.com/what-is/sdlc/

## Puntos clave

AWS ofrece la cobertura **más operacional** de las tres fuentes: enumera las 6 fases con detalle de actividades, presenta una tabla comparativa de modelos con ventajas/desventajas explícitas, e introduce conceptos que las otras fuentes no cubren: **DevSecOps**, **ALM** y la distinción con *Systems Development Lifecycle*.

### Definición AWS

> "El proceso rentable y eficiente en tiempo que los equipos de desarrollo utilizan para diseñar y construir software de alta calidad."

### Las 6 fases AWS

1. **Plan** — costo-beneficio, estimación, requisitos, especificaciones
2. **Design** — análisis de requisitos, selección de soluciones y módulos
3. **Implement** — escritura de código según tareas diarias
4. **Test** — pruebas automatizadas + manuales; verificación de requisitos
5. **Deploy** — empaquetamiento y paso a producción
6. **Maintain** — corrección de errores, monitoreo, soporte a usuarios

> **Tensión con Atlassian:** AWS usa 6 fases; Atlassian usa 7 (agrega *Feasibility Analysis* entre Plan y Design). Ver [[concepts/sdlc#fases]].

### Tabla de modelos

| Modelo     | Ventaja                          | Desventaja                      |
|------------|----------------------------------|---------------------------------|
| Waterfall  | Disciplina, outputs tangibles    | Inflexible ante cambios         |
| Iterativo  | Gestión de riesgos flexible      | Riesgo de subestimar recursos   |
| Spiral     | Ideal para proyectos complejos   | Costoso para proyectos pequeños |
| Agile      | Identifica problemas temprano    | Exceso de cambios por feedback  |

### Distinciones conceptuales únicas

- **SDLC** ≠ **Systems Development Lifecycle**: el segundo incluye hardware, procesos y cambios organizacionales
- **SDLC** ⊂ **ALM** (Application Lifecycle Management): ALM abarca todo el ciclo de vida post-desarrollo

### DevSecOps

AWS es la única fuente que cubre **DevSecOps** en detalle: seguridad integrada en cada fase, colaboración entre devs, seguridad y operaciones. Ver [[concepts/devsecops]].

### Herramientas AWS

- [[entities/aws-codeguru]] — calidad de código con IA
- [[entities/aws-codepipeline]] — automatización de releases
- [[entities/aws-codebuild]] — compilación y empaquetamiento
- [[entities/aws-ecs]] — despliegue containerizado
- [[entities/amazon-managed-grafana]] — monitoreo y métricas

## Conexiones

- [[concepts/sdlc]] — página de síntesis principal
- [[concepts/devsecops]] — DevSecOps (cubierto principalmente aquí)
- [[concepts/spiral]] — modelo Spiral (cubierto principalmente aquí)
- [[concepts/iterative-model]] — modelo Iterativo (cubierto principalmente aquí)
- [[concepts/waterfall]] — modelo Waterfall
- [[concepts/agile]] — modelo Agile
- [[ibm-sdlc]] — perspectiva IBM
- [[atlassian-sdlc]] — perspectiva Atlassian
