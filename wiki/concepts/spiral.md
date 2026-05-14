---
title: "Modelo Spiral"
type: concept
tags: [sdlc, spiral, riesgo, modelos, iterativo]
sources: [2026-05-13_aws-sdlc.md]
updated: 2026-05-13
---

# Modelo Spiral (En Espiral)

## Descripción

El modelo Spiral combina iteraciones cíclicas con **análisis de riesgo continuo**. En cada ciclo (espiral), el equipo planifica, evalúa riesgos, desarrolla y evalúa resultados, antes de iniciar el siguiente ciclo con mayor alcance.

## Fuente primaria

Cubierto principalmente por **AWS**. IBM y Atlassian no lo detallan en sus artículos principales.

## Estructura de cada ciclo

1. **Planning** — definición de objetivos del ciclo
2. **Risk Analysis** — identificación y mitigación de riesgos
3. **Engineering** — desarrollo y pruebas del incremento
4. **Evaluation** — revisión por el cliente, retroalimentación

## Ventajas (según AWS)

- **Adecuado para proyectos complejos** con alto riesgo
- La evaluación de riesgo explícita en cada ciclo reduce sorpresas tardías
- Permite incorporar cambios ciclo a ciclo

## Desventajas (según AWS)

- **Costoso para proyectos pequeños**: el análisis de riesgo añade overhead significativo
- Requiere experiencia en gestión de riesgos
- Puede ser difícil de estimar el número total de ciclos

## Cuándo usarlo

- Proyectos grandes, complejos y con alta incertidumbre
- Sistemas de misión crítica donde el riesgo debe gestionarse explícitamente
- Proyectos donde los requisitos pueden cambiar entre ciclos

## Comparación

- **vs [[agile]]**: ambos son iterativos, pero Spiral enfatiza el análisis de riesgo formal; Agile enfatiza el feedback del cliente y la velocidad de entrega
- **vs [[iterative-model]]**: Spiral es un tipo específico de modelo iterativo con gestión de riesgo estructurada
- **vs [[waterfall]]**: Waterfall es un solo recorrido lineal; Spiral hace múltiples recorridos con análisis de riesgo

## Conexiones

- [[sdlc]] — concepto padre
- [[iterative-model]] — modelo relacionado (Spiral es un caso especial de iterativo)
- [[agile]] — otra alternativa iterativa
- [[aws-sdlc]] — fuente primaria (AWS es la fuente que lo detalla)
