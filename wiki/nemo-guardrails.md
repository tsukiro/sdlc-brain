---
title: "NeMo Guardrails — NVIDIA (open-source)"
type: source-summary
tags: [seguridad, guardrails, nemo, nvidia, langchain, colang, rag, defensa]
sources: [2026-05-13_nemo-guardrails.md]
updated: 2026-05-13
---

# NeMo Guardrails (NVIDIA)

> Toolkit open-source para añadir controles programables a aplicaciones LLM.

## Qué es

NeMo Guardrails es un toolkit de NVIDIA que permite agregar capas de control ("rails") a aplicaciones conversacionales basadas en LLMs. Las rails son formas específicas de controlar el output del modelo — como "no hablar de política" o "responder de cierta manera a ciertos inputs".

A diferencia de técnicas de prompt engineering puro, NeMo implementa las mitigaciones como capas de software explícitas con un lenguaje de dominio específico.

---

## Los 5 tipos de rails

```
Flujo de una request con NeMo Guardrails:

Input → [Input Rail] → LLM → [Dialog Rail]
                               ↓
                 [Retrieval Rail] ← RAG
                               ↓
              [Execution Rail] ← Tool calls
                               ↓
              [Output Rail] → Usuario
```

| Rail | Posición | Qué controla |
|------|----------|-------------|
| **Input** | Antes del LLM | Valida/rechaza/modifica el input del usuario |
| **Dialog** | En el prompt del LLM | Estructura conversacional; respuestas predefinidas |
| **Retrieval** | En RAG | Filtra/altera chunks recuperados antes del prompt |
| **Execution** | En tool calls | Supervisa inputs/outputs de herramientas |
| **Output** | Antes del usuario | Valida/rechaza la respuesta generada |

El **Retrieval Rail** es especialmente relevante para prompt injection indirecta: intercepta chunks de fuentes externas antes de que lleguen al LLM.

---

## Colang — el lenguaje de control

```python
define user express greeting
  "Hello!"
  "Good afternoon!"

define flow
  user express greeting
  bot express greeting
  bot offer to help
```

Sintaxis similar a Python. Diseñado para flows conversacionales controlables. Disponible en versiones 1.0 y 2.0.

---

## Instalación e integración

```bash
pip install nemoguardrails
```

**Integración LangChain**: activar con `NEMOGUARDRAILS_LLM_FRAMEWORK=langchain`. Envuelve cadenas LangChain o las invoca dentro de configuraciones de guardrails.

**Uso básico**:
```python
from nemoguardrails import LLMRails, RailsConfig

config = RailsConfig.from_path("PATH/TO/CONFIG")
rails = LLMRails(config)
completion = rails.generate(
    messages=[{"role": "user", "content": "..."}]
)
```

API compatible con OpenAI Chat Completions (migración fácil).

---

## Cuándo usar NeMo Guardrails

- Sistemas RAG donde las fuentes externas son no-confiables (Retrieval Rail contra indirect injection)
- Aplicaciones que requieren restricciones de dominio estrictas (Dialog Rail)
- Pipelines con tool calls que necesitan supervisión (Execution Rail)
- Cuando se necesita control programático explícito más allá del system prompt

**Limitación**: agrega latencia. Cada rail adiciona una llamada o evaluación. Para sistemas de baja latencia, considerar implementación selectiva.

---

## Conexiones

- [[concepts/prompt-injection]] — NeMo como herramienta de mitigación estructural
- [[concepts/llm-security]] — capa de defensa adicional
- [[concepts/rag]] — Retrieval Rail es específico para pipelines RAG
- [[langgraph]] — NeMo puede integrarse con agentes LangGraph
- [[entities/nemo-guardrails]] — página de entidad (ver entidades)
