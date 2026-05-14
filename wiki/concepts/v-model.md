---
title: "V-Model"
type: concept
tags: [sdlc, v-model, testing, modelos, secuencial]
sources: [2026-05-13_ibm-sdlc.md]
updated: 2026-05-13
---

# V-Model (Modelo en V)

## Descripción

El V-Model es una extensión del [[waterfall]] donde **cada fase de desarrollo tiene su correspondiente fase de pruebas**. La "V" representa la forma visual: el lado izquierdo baja por las fases de desarrollo (especificación → diseño → implementación) y el lado derecho sube por las fases de pruebas correspondientes.

## Fuente primaria

Cubierto principalmente por **IBM**. AWS y Atlassian no lo detallan en sus artículos principales.

## Características

- Estructura lineal y secuencial (como Waterfall)
- Cada fase de desarrollo tiene una fase de pruebas explícita y paralela definida
- Las pruebas se **planifican** durante la fase de desarrollo correspondiente, aunque se **ejecutan** después de la implementación
- Elimina bugs temprano porque el plan de pruebas existe desde las primeras fases

## Ventajas

- Las pruebas frecuentes reducen la acumulación de bugs
- Mayor disciplina en QA integrada al proceso
- Bueno para software con **requisitos estables** que necesita verificación rigurosa

## Desventajas

- Tan **inflexible como Waterfall**: cambios de requisitos son costosos
- No adecuado para proyectos con requisitos en evolución
- La linealidad puede hacer el proceso lento

## Cuándo usarlo

> "Software con requisitos estables que requiere pruebas frecuentes" — IBM

- Sistemas embebidos, software médico, aeroespacial, o cualquier dominio donde la verificación es crítica
- Proyectos donde los requisitos no cambiarán durante el desarrollo

## Comparación

- **vs [[waterfall]]**: V-Model añade pruebas explícitas por fase; Waterfall las deja para el final
- **vs [[agile]]**: Agile permite cambios continuos; V-Model asume requisitos fijos como Waterfall

## Conexiones

- [[sdlc]] — concepto padre
- [[waterfall]] — modelo base del que deriva
- [[agile]] — alternativa flexible
- [[ibm-sdlc]] — fuente primaria (IBM es la fuente que lo detalla)
