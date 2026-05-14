---
title: "Outlines — Efficient Guided Generation for LLMs (Willard & Louf, 2023)"
type: source-summary
tags: [structured-generation, constrained-decoding, outlines, logit-masking, fsm, arxiv]
sources: []
updated: 2026-05-13
---

# Outlines — Efficient Guided Generation for LLMs (Willard & Louf, 2023)

**Fuente:** https://arxiv.org/abs/2307.09702  
**Autores:** Brandon T. Willard & Rémi Louf (Normal Computing)  
**Repositorio:** https://github.com/outlines-dev/outlines

## Perspectiva de esta fuente

Paper académico fundacional que provee la base matemática de la **Generación Estructurada** (Structured Generation) para LLMs. Define formalmente cómo transformar esquemas de datos (JSON Schema, Regex, gramáticas libres de contexto) en restricciones que operan directamente sobre el motor de inferencia del modelo.

---

## Contribución central

El paper reformula la generación de texto neural como **transiciones entre estados de una Máquina de Estados Finitos (FSM)**.

En lugar de dejar que el modelo elija entre los ~100,000 tokens del vocabulario completo en cada paso, el motor construye un **índice** sobre el vocabulario que mapea cada token a los estados FSM que permite transicionar. En tiempo de generación, aplica una **máscara binaria sobre los logits** (logit masking), asignando probabilidad cero a todos los tokens que violarían el esquema.

```
Esquema JSON:  { "edad": integer }
FSM compila a: solo tokens numéricos son válidos cuando el modelo está en el campo "edad"
Logit mask:    P("cuarenta") = 0, P("40") > 0
```

---

## Propiedades del enfoque

| Propiedad | Detalle |
|---|---|
| **Agnóstico al modelo** | Funciona con cualquier LLM que exponga logits |
| **Overhead mínimo** | El índice se construye una vez por esquema; generación normal + máscara |
| **Cobertura** | JSON Schema, Regex, gramáticas libres de contexto, Python types |
| **Garantía** | 100% de coincidencia con el esquema — no probabilístico |

---

## Significado para la industria

> "Significantly outperforms existing solutions" — los autores en el abstract.

Este paper es el fundamento teórico de la generación restringida que luego adoptaron:
- **Outlines** (la librería open-source de los mismos autores)
- **Guidance** (Microsoft)
- **vLLM** (soporte de constrained decoding)
- **OpenAI Structured Outputs** (agosto 2024) — implementación nativa en API

---

## Conexiones

- [[concepts/structured-generation]] — la técnica abstracta explicada en profundidad
- [[entities/outlines]] — la librería Python que implementa este paper
- [[concepts/prompt-injection]] — Constrained Decoding es Capa 3 de mitigación (para outputs acotados)
- [[prompt-injection-architecture]] — Von Neumann gap; CD como solución parcial estructural
