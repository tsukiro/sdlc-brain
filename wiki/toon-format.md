---
title: "TOON — Token-Oriented Object Notation"
type: source-summary
tags: [toon, json, token-efficiency, llm-prompting, formato-datos, optimización]
sources: []
updated: 2026-05-14
---

# TOON — Token-Oriented Object Notation

**Fuente:** Artículo "Why JSON is Inflating Your LLM Bill and How TOON Slashes Costs by 60%"

## Perspectiva de esta fuente

Artículo técnico que introduce TOON como alternativa a JSON para prompts de LLMs. Parte del análisis del "syntax tax" de JSON y el concepto de Tabular Eligibility para decidir cuándo usar cada formato.

---

## El problema: "Syntax Tax" de JSON

JSON fue diseñado para legibilidad humana + interoperabilidad de red. Cuando se usa como formato de prompt para LLMs, su sintaxis tiene un costo:

```
JSON (10 registros de logs):
{
  "id": "LOG-88291",
  "timestamp": "2026-01-04T20:15:00Z",
  "metadata": { "service": "auth-gateway", ... },
  "level": "ERROR",
  "message": "Connection timeout"
}
```

**Token breakdown por 10 registros:**
- Puntuación (`{`, `}`, `"`, `:`, `,`): ~15-20 tokens por registro
- **Claves repetidas 10 veces**: "id", "timestamp", "metadata" = ~30 tokens × 10
- Espacios (pretty-print): ~5-10 tokens
- **Total: ~800-1,000 tokens. ~60% = syntax noise.**

### Los 3 bottlenecks de JSON en LLMs

| Bottleneck | Descripción |
|---|---|
| **Context Switching Penalty** | JSON activa "modo data processor" — texto empático dentro de JSON resulta plano y robótico |
| **Token Inefficiency** | 20-40% del context window es sintaxis pura (claves repetidas, comillas, llaves) |
| **Distribution Shift** | Chain-of-Thought requiere "pensar en voz alta"; JSON es demasiado rígido para esto. Además, si el modelo debe llenar `confidence_score` pero no sabe la respuesta, la presión del schema lo fuerza a **alucinar un valor** |

---

## TOON: formato diseñado para token economy

> "TOON is the first data format designed from the ground up for Token Economy."

TOON = híbrido de YAML (indentación) + CSV (headers una vez):

```
# JSON — 10 registros:
[
  {"id": 1, "name": "Alice", "role": "admin"},
  {"id": 2, "name": "Bob", "role": "user"}
]
→ ~80-100 tokens

# TOON — mismos datos:
users[2]{id,name,role}:
  1,Alice,admin
  2,Bob,user
→ ~20-25 tokens
```

**Ahorro: ~60-75% de tokens en datos tabulares uniformes.**

---

## Sintaxis TOON

```
# Estructura básica
nombre[N]{campo1,campo2,...}:
  valor1,valor2,...
  valor1,valor2,...

# Ejemplo: logs
logs[10]{id,timestamp,level,service,message,metadata{ip,code}}:
  2001,2026-01-04T08:14:23Z,error,auth-api,Authentication failed,172.16.4.21,401
  2002,2026-01-04T08:15:12Z,warn,billing-worker,Retrying...,172.16.4.88,503
```

**Elementos clave:**
- `[N]` — declara exactamente cuántos registros esperar (guardrail anti-truncación)
- `{campos}` — schema declarado UNA vez, no repetido por fila
- `:` y `,` — delimitadores mínimos que los LLMs ya reconocen del código
- Indentación — hereda de YAML para mostrar jerarquía

---

## Métricas de rendimiento

| Métrica | JSON | TOON |
|---|---|---|
| Tokens (10 registros de logs) | ~450-500 | ~180-210 |
| Ahorro (datos tabulares) | — | **40-60%** |
| Accuracy (extracción, benchmarks) | 70% | **74%** |
| LLMs soportados | Todos | Todos (formato agnóstico) |

*Benchmarks en GPT-5 y Gemini 3.*

---

## Implementación Python

```bash
pip install toon-format tiktoken
```

```python
import json
from toon import encode
import tiktoken

data = {"logs": [{"id": i, "level": "ERROR", "msg": f"err-{i}"} for i in range(10)]}

json_str = json.dumps(data, separators=(',', ':'))
toon_str = encode(data)  # auto-detecta arrays uniformes y aplica header

enc = tiktoken.get_encoding("o200k_base")
json_tokens = len(enc.encode(json_str))
toon_tokens = len(enc.encode(toon_str))

print(f"JSON: {json_tokens} tokens")
print(f"TOON: {toon_tokens} tokens")
print(f"Ahorro: {100*(1-toon_tokens/json_tokens):.1f}%")
```

---

## Tabular Eligibility (TE): ¿cuándo usar TOON?

**TE** = porcentaje de datos que son arrays uniformes de objetos con las mismas claves.

| TE Score | Recomendación |
|---|---|
| > 80% | **TOON** — ahorro ~50%, mayor accuracy en extracción |
| 30-80% | Evaluar según latencia y costo |
| < 30% | **JSON minificado** — datos irregulares, TOON añade overhead |
| 0% (tabla plana) | **CSV** — más eficiente que TOON para datos sin nesting |

---

## Estrategia híbrida recomendada: "JSON-In, TOON-Between"

```
Base de datos → JSON
       ↓ encode()
    TOON (prompt al LLM)
       ↓ LLM procesa
    JSON (respuesta del LLM → fácil de parsear en backend)
```

- **Storage:** JSON en BD (estándar)
- **Prompting:** TOON (optimizado para tokens)
- **Output:** JSON (el backend ya sabe parsearlo)

---

## Cuándo NO usar TOON

| Situación | Usar en su lugar | Razón |
|---|---|---|
| Datos con anidamiento profundo e irregular | JSON minificado | El overhead de indentación supera el ahorro |
| Tablas 2D puras sin nesting | CSV | Más eficiente que TOON |
| Inferencia local con vLLM/Ollama | JSON | Aceleración C++ nivel hardware para JSON puede ganar en latencia |

---

## Ecosistema TOON

- **TypeScript/JS:** referencia implementation (Node.js edge functions)
- **Python:** estándar para RAG y data science pipelines
- **Go/Rust:** log processing de alto throughput
- **IDE Support:** plugins VS Code y JetBrains con syntax highlighting

---

## Conexiones

- [[entities/toon]] — entidad de la herramienta
- [[concepts/structured-generation]] — TOON como formato de input vs structured outputs como formato de output
- [[concepts/rag]] — TOON especialmente útil para RAG pipelines (más chunks en el mismo context window)
- [[concepts/llm-agents]] — agentes multi-turn pueden usar TOON para intercambio de estado
- [[jxnl-pipelines]] — eficiencia en pipelines LLM = token economy
