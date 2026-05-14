---
title: "Instructor (librería Python)"
type: entity
tags: [structured-generation, instructor, pydantic, python, jason-liu, llm-pipelines]
sources: []
updated: 2026-05-13
---

# Instructor

**Tipo:** Librería Python open-source  
**Autor:** Jason Liu  
**Repositorio:** https://github.com/jxnl/instructor  
**Docs:** https://python.useinstructor.com  
**Descargas:** +3 millones mensuales (2026)

## Qué es

Instructor es la librería Python más popular para obtener datos estructurados y validados de LLMs. Usa modelos **Pydantic** como especificación de la respuesta esperada. Es el estándar de facto para implementar Spec-Driven Development (SDD) con APIs cloud de LLMs.

## Instalación

```bash
pip install instructor
```

## Uso mínimo

```python
import instructor
from pydantic import BaseModel

class UserInfo(BaseModel):
    name: str
    age: int
    role: str

client = instructor.from_provider("anthropic/claude-sonnet-4-6")
user = client.create(
    response_model=UserInfo,
    messages=[{"role": "user", "content": "Ana es desarrolladora senior de 32 años"}]
)
# user.name == "Ana", user.age == 32, user.role == "desarrolladora senior"
```

## Proveedores soportados

OpenAI · Anthropic · Google Gemini · Mistral · Groq · Ollama · DeepSeek · Cohere · Fireworks · Together AI · LiteLLM · y otros (15+ total)

## Características

| Feature | Descripción |
|---|---|
| **Reintentos automáticos** | `max_retries=N` — el error de Pydantic se pasa al LLM para autocorrección |
| **Streaming** | `client.create_partial(...)` — objeto hidratado progresivamente |
| **Hooks** | `on_error`, `on_retry` para observabilidad |
| **Multi-modal** | Funciona con inputs de imagen (modelos que lo soporten) |
| **Async** | `AsyncInstructor` para pipelines asíncronos |

## Posición en el stack

```
Aplicación
    ↓
Instructor (Pydantic schema → JSON Schema → prompt)
    ↓
API del LLM (OpenAI / Anthropic / Gemini / ...)
    ↓
Respuesta JSON → validación Pydantic → objeto Python tipado
```

Instructor no accede a los logits del modelo — opera sobre la API pública. Esto lo hace compatible con cualquier proveedor cloud sin requerir infraestructura de inferencia propia.

## Conexiones

- [[instructor]] — source-summary de la documentación
- [[jxnl-pipelines]] — filosofía del mismo autor
- [[entities/outlines]] — alternativa de nivel más bajo (logits) para modelos locales
- [[entities/typechat]] — equivalente en TypeScript/Microsoft
- [[concepts/structured-generation]] — el concepto que materializa
- [[langgraph]] — uso recomendado dentro de nodos LangGraph
