---
title: "TOON (Token-Oriented Object Notation)"
type: entity
tags: [toon, formato-datos, token-efficiency, llm-prompting, json-alternativa]
sources: []
updated: 2026-05-14
---

# TOON — Token-Oriented Object Notation

**Tipo:** Formato de datos open-source  
**Propósito:** Reemplazar JSON en prompts de LLMs para reducir tokens 40-60%  
**Ecosistema:** Python (`toon-format`), TypeScript, Go, Rust

## Qué es

TOON es el primer formato de datos diseñado específicamente para la **token economy** de los LLMs. No está diseñado para legibilidad humana ni para interoperabilidad de red — está diseñado para maximizar la información útil por token en los prompts.

## Problema que resuelve

JSON cobra un "syntax tax": comillas, llaves y claves repetidas pueden representar el 40-60% de los tokens de un prompt, sin agregar información semántica. Con modelos que cobran por millón de tokens, esto infla costos y reduce el espacio disponible para datos reales.

## Sintaxis

```
nombre[N]{campo1,campo2,campo3}:
  valor,valor,valor
  valor,valor,valor
```

- `[N]` — guardrail: el LLM sabe exactamente cuántos registros esperar
- `{campos}` — schema declarado **una vez**, no por cada fila
- Indentación = jerarquía (de YAML)
- Separadores mínimos (`,`, `:`) que los LLMs ya conocen

## Métricas clave

| Formato | Tokens (10 logs) | Accuracy (extracción) |
|---|---|---|
| JSON (minified) | ~450-500 | 70% |
| **TOON** | **~180-210** | **74%** |
| CSV | ~100-120 | ~65% (sin guardrails) |

## Instalación

```bash
# Python
pip install toon-format

# Node.js
npm install toon-format
```

## Uso rápido (Python)

```python
from toon import encode
import json

data = {"users": [{"id": 1, "name": "Ana"}, {"id": 2, "name": "Bob"}]}
toon_str = encode(data)
# → users[2]{id,name}:\n  1,Ana\n  2,Bob
```

## Cuándo usar

- Arrays uniformes de objetos (logs, RAG chunks, catálogos) con TE > 80%
- Pipelines donde el context window es el cuello de botella
- Workflows agénticos multi-turn con estado compartido extenso

## Cuándo NO usar

- Datos altamente irregulares o anidados (TE < 30%) → JSON minificado
- Tablas 2D planas sin nesting → CSV
- Latencia crítica con inferencia local en vLLM → JSON (acelerado en C++)

## Conexiones

- [[toon-format]] — source-summary detallado con métricas y código
- [[concepts/rag]] — TOON permite incluir más chunks RAG en el mismo prompt
- [[concepts/structured-generation]] — TOON es formato de INPUT; structured gen es formato de OUTPUT
- [[concepts/llm-agents]] — estado entre agentes enviado en TOON reduce context pressure
