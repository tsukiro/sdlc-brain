---
title: "Outlines (librería Python)"
type: entity
tags: [structured-generation, constrained-decoding, outlines, python, fsm, logit-masking]
sources: []
updated: 2026-05-13
---

# Outlines

**Tipo:** Librería Python open-source  
**Organización:** Normal Computing  
**Repositorio:** https://github.com/outlines-dev/outlines  
**Autores:** Brandon T. Willard & Rémi Louf

## Qué es

Outlines es la implementación de referencia de **Constrained Decoding** para LLMs. Implementa los algoritmos del paper *Efficient Guided Generation for LLMs* (arXiv 2307.09702) que garantizan matemáticamente que la salida de un LLM cumpla con un esquema predefinido.

## Cómo funciona

1. Compila el esquema (JSON Schema, Regex, gramática) en una **Máquina de Estados Finitos (FSM)**
2. Construye un índice que mapea tokens del vocabulario del LLM a transiciones válidas del FSM
3. En cada paso de inferencia, aplica una **máscara binaria sobre los logits** — tokens que violarían el esquema reciben probabilidad 0

## Esquemas soportados

- JSON Schema (incluyendo Pydantic models)
- Expresiones regulares (Regex)
- Gramáticas libres de contexto
- Python `Enum` types
- Tipos primitivos (`int`, `float`, `bool`)

## Propiedades

| Propiedad | Valor |
|---|---|
| **Garantía** | 100% coincidencia — matemática, no probabilística |
| **Overhead** | Mínimo — el índice se compila una vez por schema |
| **Compatibilidad** | Agnóstico al modelo (cualquier LLM con acceso a logits) |

## Posición en el ecosistema

Outlines opera a nivel del **motor de inferencia** — es más bajo en la stack que Instructor o TypeChat (que operan a nivel de API/post-processing). Está integrado en:
- **vLLM** — servidor de inferencia de alto rendimiento
- **llama.cpp** — inferencia local
- **Transformers** (Hugging Face) — via integración directa

## Diferencia clave con Instructor

| | Outlines | Instructor |
|---|---|---|
| **Nivel de operación** | Motor de inferencia (logits) | Capa de API (post-process + retry) |
| **Garantía** | Matemática (imposible violar el schema) | Probabilística (validación + retry) |
| **Requiere acceso a logits** | Sí | No (funciona con APIs externas) |
| **Uso con APIs cloud** | No directamente | Sí (OpenAI, Anthropic, Gemini) |

## Conexiones

- [[outlines-paper]] — paper fundacional que implementa
- [[concepts/structured-generation]] — el concepto que Outlines materializa
- [[entities/instructor]] — herramienta complementaria (APIs cloud)
- [[concepts/prompt-injection]] — Constrained Decoding como Capa 3 de mitigación
