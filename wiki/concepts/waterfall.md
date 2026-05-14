---
title: "Modelo Waterfall"
type: concept
tags: [sdlc, waterfall, modelos, secuencial]
sources: [2026-05-13_ibm-sdlc.md, 2026-05-13_aws-sdlc.md, 2026-05-13_atlassian-sdlc.md]
updated: 2026-05-13
---

# Modelo Waterfall (Cascada)

## Descripción

El modelo Waterfall es el enfoque **secuencial y lineal** del SDLC: cada fase se completa antes de comenzar la siguiente, como el agua que cae en cascada. Es el modelo más antiguo y tradicional, y sirve como punto de referencia contra el cual se comparan los modelos modernos.

## Características

- Las fases se ejecutan en orden estricto: una termina antes de que la siguiente comience
- Los entregables de cada fase son la entrada de la siguiente
- Los requisitos se definen completamente al inicio del proyecto
- No hay ciclos de retroalimentación entre fases (o son costosos)

## Fases según Atlassian

requirements → design → implementation → verification → maintenance

## Ventajas

- Disciplina de proceso: cada fase tiene entregables claros y tangibles
- Fácil de gestionar y medir progreso
- Documentación exhaustiva
- Bueno para proyectos con requisitos completamente definidos desde el inicio

## Desventajas

- **Inflexible ante cambios**: incorporar un cambio de requisitos en medio del proyecto es costoso (AWS)
- Detecta problemas tarde: los errores de diseño se descubren en testing, no en diseño
- El cliente no ve el producto hasta etapas avanzadas

## Cuándo usarlo

> "Proyectos pequeños y bien definidos" — Atlassian

- Requisitos estables y bien documentados
- Equipo y cliente con baja tolerancia a la ambigüedad
- Contextos regulados donde la documentación es mandatoria

## Comparación con modelos alternativos

- **vs [[agile]]**: Agile permite cambios continuos; Waterfall asume requisitos fijos
- **vs [[v-model]]**: V-Model es Waterfall con pruebas explícitas por cada fase (más disciplina en QA)
- **vs [[iterative-model]]**: Iterativo permite refinar en versiones sucesivas; Waterfall entrega todo al final

## Conexiones

- [[sdlc]] — concepto padre
- [[agile]] — alternativa iterativa
- [[v-model]] — variante con testing paralelo por fase
- [[ibm-sdlc]] — perspectiva IBM sobre Waterfall
- [[aws-sdlc]] — perspectiva AWS (tabla comparativa)
- [[atlassian-sdlc]] — perspectiva Atlassian (criterios de selección)
