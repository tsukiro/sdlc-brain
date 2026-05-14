---
title: "Structured Generation / Spec-Driven Development"
type: concept
tags: [structured-generation, constrained-decoding, sdd, logit-masking, fsm, pydantic, producción]
sources: []
updated: 2026-05-13
---

# Structured Generation / Spec-Driven Development (SDD)

> [!info]
> También llamado Schema-Driven Development. El paradigma que reemplaza la ingeniería de prompts por contratos de datos matemáticamente garantizados entre el LLM y el resto del sistema.

---

## El problema que resuelve

Los LLMs son máquinas probabilísticas de texto plano. El software es determinista y espera datos estructurados (JSON, objetos, enums). Sin SDD, el puente entre ambos mundos es frágil:

```
Sin SDD:
  Prompt: "Por favor devuelve un JSON con los campos nombre y edad"
  LLM puede responder: "¡Claro! Aquí tienes: {\"nombre\": \"Ana\", \"edad\": \"treinta\"}"
  → Crash en el parser (edad debería ser int, no string)
  → El campo "¡Claro! Aquí tienes:" rompe el JSON parser
```

Los LLMs están alineados por RLHF para ser asistentes serviciales, lo que produce saludos, disclaimers y variaciones de formato que destruyen los parsers automáticos.

---

## La solución: Constrained Decoding

La generación estructurada interviene en el motor de inferencia a nivel matemático:

```
Con Constrained Decoding (Outlines):
  Schema: { "edad": integer }
  FSM compila el schema → máscara de logits
  En cada paso de generación:
    P("treinta") = 0   ← bloqueado matemáticamente
    P("30")      > 0   ← permitido
  Garantía: 100% de coincidencia con el schema
```

**Por qué es más robusto que el prompt engineering**: no depende del LLM para obedecer las instrucciones. La restricción opera a nivel de probabilidades, antes de la emisión del token. El modelo físicamente no puede generar un token que viole el esquema.

---

## Dos niveles de implementación

### Nivel 1: Motor de inferencia (logits) — Outlines, Guidance, vLLM

Opera sobre los logits directamente. Requiere acceso a los pesos del modelo (inferencia local o controlada).

```
Vocabulario del modelo (100k tokens)
        ↓
  Máscara FSM (0/1 por token)
        ↓
  Solo tokens válidos según schema
```

**Herramientas:** [[entities/outlines]], Microsoft Guidance, vLLM constrained decoding

### Nivel 2: Capa de API (post-process + retry) — Instructor, TypeChat

Cuando el modelo es una API externa (sin acceso a logits). Envía el schema como parte del prompt o función, valida la respuesta, y reintenta con el error como feedback.

```
Prompt + JSON Schema del modelo Pydantic
        ↓
  API del LLM (OpenAI / Anthropic / Gemini)
        ↓
  Respuesta JSON → Validación Pydantic
        ↓ (si falla)
  Error de validación → reintento con feedback
        ↓ (si pasa)
  Objeto Python tipado ✓
```

**Herramientas:** [[entities/instructor]] (Python/Pydantic), [[entities/typechat]] (TypeScript)

---

## Soporte nativo en plataformas (2024-2026)

La industria reconoció SDD como infraestructura crítica:

| Plataforma | Feature | Desde |
|---|---|---|
| OpenAI | Structured Outputs | Agosto 2024 |
| Anthropic | Tool use con JSON Schema | 2024 |
| Google Gemini | `response_schema` constrained | 2024 |

---

## Aplicaciones en sistemas de producción

### 1. Pipelines confiables

```python
class ArticleMetadata(BaseModel):
    title: str
    category: Literal["tech", "business", "science"]
    sentiment: float = Field(ge=-1.0, le=1.0)
    tags: list[str]

# Garantizado: category siempre será uno de los 3 valores
# Garantizado: sentiment siempre será float en [-1, 1]
```

### 2. Semantic routing infalible en multi-agent

```python
class RouterDecision(BaseModel):
    agent: Literal["TOOL_SEARCH", "TOOL_CALCULATE", "TOOL_WRITE"]
    confidence: float

# El agente enrutador no puede alucinar "TOOL_FOOBAR"
```

### 3. Comunicación inter-agente determinista

En LangGraph o CrewAI, los mensajes entre nodos son objetos Pydantic validados — no texto libre. La inyección de prompt en un agente no puede propagarse si el output está restringido a un enum.

---

## Relación con seguridad

Constrained Decoding es la **Capa 3** de mitigación de Prompt Injection (ver [[concepts/prompt-injection]]):

> Si el modelo fue inyectado pero solo puede emitir `"Verdadero"/"Falso"`, no puede articular una respuesta de ataque.

**Limitación como mitigación de seguridad**: solo aplica cuando el output puede definirse con precisión. No mitiga injection en generación de texto libre.

---

## Tensiones y trade-offs

| Dimensión | Trade-off |
|---|---|
| **Garantía vs. flexibilidad** | Constrained Decoding garantiza el schema pero no permite respuestas fuera del dominio definido |
| **Nivel de API vs. logits** | Instructor (API) es más fácil de usar pero no es 100% garantizado sin constrained decoding subyacente |
| **OpenAI Structured Outputs** | Garantiza schema pero requiere vendor lock-in; Outlines lo hace vendor-agnostic |
| **Complejidad del schema** | Schemas muy complejos pueden reducir calidad del contenido (el modelo optimiza estructura sobre semántica) |

---

## Árbol de decisión: ¿qué herramienta usar?

```
¿Tienes acceso al motor de inferencia (modelo local/controlled)?
├── Sí → Outlines o vLLM constrained decoding (garantía matemática)
└── No (API cloud) →
    ├── ¿Python? → Instructor (Pydantic)
    ├── ¿TypeScript? → TypeChat (Microsoft)
    └── ¿Quieres soporte nativo sin librería? →
        ├── OpenAI → Structured Outputs (json_schema)
        ├── Anthropic → Tool use con JSON Schema
        └── Gemini → response_schema parameter
```

---

## Conexiones

- [[outlines-paper]] — fundamento matemático (Willard & Louf, 2023)
- [[entities/outlines]] — implementación de referencia (logits)
- [[entities/instructor]] — estándar Python (APIs cloud)
- [[entities/typechat]] — estándar TypeScript (Microsoft)
- [[instructor]] — documentación de Instructor
- [[typechat]] — documentación de TypeChat
- [[jxnl-pipelines]] — filosofía: pipelines LLM = software engineering
- [[concepts/prompt-injection]] — CD como Capa 3 de mitigación de seguridad
- [[concepts/multi-agent-frameworks]] — SDD garantiza comunicación inter-agente
- [[langgraph]] — implementación práctica en nodos LangGraph
- [[entities/gemini]] — Gemini soporta `response_schema` nativo
