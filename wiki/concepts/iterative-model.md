---
title: "Modelo Iterativo"
type: concept
tags: [sdlc, iterativo, modelos, versiones]
sources: [2026-05-13_aws-sdlc.md]
updated: 2026-05-13
---

# Modelo Iterativo

## Descripción

El modelo Iterativo construye el software en **versiones sucesivas**, donde cada iteración entrega una versión mejorada del producto. A diferencia de Waterfall (que entrega todo al final), el modelo iterativo permite incorporar aprendizajes de cada versión en la siguiente.

## Fuente primaria

Cubierto principalmente por **AWS** como modelo independiente. IBM lo menciona brevemente como contraste con Waterfall. Atlassian no lo trata como modelo separado.

## Características

- El producto se construye incrementalmente, versión por versión
- Cada iteración pasa por todas las fases del SDLC (planificación, diseño, implementación, pruebas)
- Los requisitos se pueden refinar entre iteraciones
- El producto mejora progresivamente

## Ventajas (según AWS)

- **Gestión de riesgos flexible**: los problemas se identifican y corrigen iteración a iteración
- Permite ajustar el rumbo basándose en resultados reales de versiones anteriores
- El cliente ve evolución del producto en cada entrega

## Desventajas (según AWS)

- Riesgo de **subestimar los recursos** necesarios (el alcance total puede crecer iteración a iteración)
- Si no se gestiona bien, las iteraciones pueden volverse indefinidas

## Relación con otros modelos

El modelo iterativo es el **concepto base** del que derivan modelos más específicos:
- [[agile]] — iterativo con sprints, ceremonias y frameworks definidos
- [[spiral]] — iterativo con análisis de riesgo formal en cada ciclo

## Comparación

- **vs [[waterfall]]**: Waterfall entrega todo al final; Iterativo entrega versiones progresivas
- **vs [[agile]]**: Agile es iterativo con ceremonias, roles y feedback del cliente muy estructurado; Iterativo es el concepto general sin esas prescripciones
- **vs [[spiral]]**: Spiral agrega análisis de riesgo explícito; Iterativo es más genérico

## Conexiones

- [[sdlc]] — concepto padre
- [[agile]] — especialización de iterativo
- [[spiral]] — especialización de iterativo con gestión de riesgo
- [[waterfall]] — alternativa secuencial
- [[aws-sdlc]] — fuente primaria
