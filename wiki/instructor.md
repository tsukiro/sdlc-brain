---
title: "Instructor — Structured Outputs con Pydantic"
type: source-summary
tags: [structured-generation, instructor, pydantic, python, llm-pipelines, jason-liu]
sources: []
updated: 2026-05-13
---

# Instructor — Structured Outputs con Pydantic

**Fuente:** https://python.useinstructor.com  
**Autor:** Jason Liu  
**Repositorio:** https://github.com/jxnl/instructor

## Perspectiva de esta fuente

Documentación oficial de Instructor, la librería Python más popular para extraer datos estructurados de LLMs. Con más de **3 millones de descargas mensuales**, es el estándar de facto para implementar Spec-Driven Development (SDD) en Python. La librería parchea el cliente del LLM para forzar validación Pydantic automática.

---

## Propuesta de valor

> "The most popular Python library for working with structured outputs from LLMs."

En lugar de parsear texto libre con regex frágil, el desarrollador define un `BaseModel` de Pydantic que actúa como **contrato ejecutable** para la respuesta del LLM.

---

## Cómo funciona

```python
import instructor
from pydantic import BaseModel

class Person(BaseModel):
    name: str
    age: int

client = instructor.from_provider("openai/gpt-4o-mini")
person = client.create(
    response_model=Person,
    messages=[{"role": "user", "content": "John es ingeniero de 30 años"}]
)
# person.name == "John", person.age == 30  — garantizado por Pydantic
```

1. El desarrollador define el modelo Pydantic (la "especificación")
2. Instructor serializa el schema Pydantic como JSON Schema y lo envía al LLM
3. El LLM devuelve JSON; Pydantic valida e hidrata el modelo
4. Si la validación falla → **reintento automático** con el error de Pydantic como feedback al modelo

---

## Características clave

| Feature | Descripción |
|---|---|
| **Reintentos automáticos** | Usa el error de validación como contexto para corregir |
| **Streaming** | Respuestas parciales en tiempo real con tipado |
| **Multi-proveedor** | 15+ proveedores: OpenAI, Anthropic, Gemini, Mistral, DeepSeek, Ollama |
| **Type-safe** | Autocompletado completo en IDE, inferencia de tipos |
| **Zero friction** | `instructor.from_provider("openai/gpt-4o-mini")` — una línea |

---

## Relevancia para pipelines de producción

Instructor encarna la tesis del artículo de Jason Liu ([[jxnl-pipelines]]): **los pipelines de LLM son software**. El schema Pydantic es:
- Documentación ejecutable (si el tipo es `int`, no puede ser `"treinta"`)
- Contrato de API entre el LLM y el resto del sistema
- Punto de entrada para los reintentos (ingeniería de errores, no magia de prompts)

En arquitecturas multi-agente con LangGraph o CrewAI, Instructor garantiza que la comunicación entre agentes sea determinista.

---

## Conexiones

- [[concepts/structured-generation]] — el concepto que Instructor implementa
- [[entities/instructor]] — entidad completa de la librería
- [[jxnl-pipelines]] — principios filosóficos del mismo autor
- [[langgraph]] — Instructor se usa dentro de nodos LangGraph para outputs tipados
- [[crewai]] — Instructor compatible con CrewAI tools/agents
- [[entities/gemini]] — Gemini es uno de los proveedores soportados
