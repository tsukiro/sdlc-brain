---
title: "Prompt Engineering Guide — Modelos: ChatGPT, GPT-4, LLaMA, Mistral (DAIR-AI)"
type: source-summary
tags: [chatgpt, gpt-4, llama, mistral, modelos-llm, rlhf, scaling-laws, gqa, swa]
sources: []
updated: 2026-05-14
---

# Prompt Engineering Guide — Modelos (DAIR-AI)

**Fuente:** https://www.promptingguide.ai/models  
**Artículos procesados:** ChatGPT, GPT-4, LLaMA, Mistral 7B

---

## ChatGPT

**Arquitectura:** GPT-3.5 fine-tuned con **RLHF** (Reinforcement Learning from Human Feedback).

### API — Estructura de mensajes

```python
messages = [
    {"role": "system", "content": "You are an AI research assistant. Use a technical and scientific tone."},
    {"role": "user",   "content": "Can you tell me about the creation of black holes?"},
    # {"role": "assistant", "content": "..."} ← conversación multi-turno
]
```

### Dato operacional crítico

`gpt-3.5-turbo` es **90% más barato** que `text-davinci-003` con capacidades similares o superiores para tareas conversacionales.

### Role Prompting — 2 componentes

Un prompt de rol efectivo especifica:
1. **Intent:** el propósito del chatbot (qué hace, para qué existe)
2. **Identity:** el estilo y tono de las respuestas (cómo responde, con qué voz)

```
System: "You are a helpful customer support agent for [Company].
Your purpose is to resolve billing issues. Respond in a professional but warm tone."
```

### Adopción industrial

Snap Inc. (Snapchat) e Instacart implementaron features conversacionales basados en ChatGPT para recomendaciones personalizadas — validando el modelo en producción consumer-scale.

---

## GPT-4

**Hito de rendimiento:** Top 10% en el bar exam simulado (examen de abogacía).

### Capacidades core

| Capacidad | Detalle |
|---|---|
| **Multimodal** | Acepta texto + imágenes; razonamiento visual (extrae datos de gráficos) |
| **128K tokens de contexto** | ~300 páginas por prompt |
| **Training cutoff** | Abril 2023 |
| **Function calling paralelo** | Múltiples tool calls simultáneos en un solo request |

### Features técnicos de API

| Feature | Implementación |
|---------|---------------|
| **JSON Mode** | `response_format: { type: "json_object" }` → output siempre JSON válido (requiere "JSON" en el system message) |
| **Reproducibilidad** | `seed` idéntico + mismo prompt/temperatura → outputs reproducibles entre runs |
| **`system_fingerprint`** | Identificador del estado del backend — si cambia, los outputs pueden variar aunque el prompt sea idéntico |
| **Function calling paralelo** | Múltiples funciones en un solo request → operaciones simultáneas |

### Steering via System Messages

Las instrucciones del system message **resisten overrides del usuario**:

```
System: "Always write output in JSON."
User:   "Don't output JSON, just use plain text."
→ Output: { "response": "..." }  ← el system message gana
```

Comportamiento de seguridad documentado: el system message es la autoridad persistente, no el usuario.

### Limitaciones

- Alucinaciones y errores de razonamiento persisten (no apto para aplicaciones de alto riesgo sin validación)
- Conocimiento cortado en abril 2023
- Brittle en ciertos recalls factuales (ej.: falló identificar a Elvis Presley en ciertos contextos)
- El `system_fingerprint` revela que cambios de backend no documentados pueden afectar reproducibilidad

---

## LLaMA (Meta)

**Escala:** 7B–65B parámetros. Datasets públicamente disponibles. Meta Research.

### Cambio de paradigma en entrenamiento

El paper de **Chinchilla** (Hoffmann et al. 2022) estableció el óptimo de compute: ~20 tokens por parámetro para entrenamiento eficiente. LLaMA desafió este paradigma:

> "El rendimiento del modelo de 7B **continúa mejorando incluso después de 1T tokens** — más allá del óptimo de Chinchilla."

**La diferencia de objetivo:**
- Chinchilla optimiza el **training budget** (costo de entrenar el modelo una vez)
- LLaMA optimiza el **inference budget** (costo de usar el modelo millones de veces)

Entrenar más allá del óptimo de Chinchilla cuesta más una sola vez; pero el modelo resultante más capaz se usa más eficientemente en cada inferencia.

### Benchmarks

| Modelo | vs. Referencia | Condición |
|--------|---------------|-----------|
| **LLaMA-13B** | Supera a GPT-3 (175B) en muchos benchmarks | Ejecutable en **una sola GPU** (10× más pequeño) |
| **LLaMA-65B** | Competitivo con Chinchilla-70B y PaLM-540B | — |

### Ecosistema derivado (2023)

Vicuna, Stanford Alpaca, Koala, ChatDoctor, Baize, GPT4All — evidencia del impacto democratizador de LLaMA como base open-source.

**Papers:**
- Hoffmann et al. 2022 — Chinchilla scaling laws (base teórica)
- LLaMA: Open and Efficient Foundation Language Models, 2023 (Meta Research)

---

## Mistral 7B

**Posición de mercado (lanzamiento 2023):** Supera a todos los modelos open-source de **13B parámetros** en todos los benchmarks — siendo un modelo de 7B.

### Innovaciones arquitectónicas

| Técnica | Función |
|---------|---------|
| **GQA (Grouped-Query Attention)** | Comparte heads de atención entre grupos de queries → acelera inferencia y reduce uso de memoria en KV cache |
| **SWA (Sliding Window Attention)** | Ventana deslizante sobre la secuencia → procesa secuencias largas con costo computacional O(n) en lugar de O(n²) |

### Template de chat (Mistral-7B-Instruct)

```
<s>[INST] Primera instrucción del usuario [/INST] Respuesta del asistente </s>
[INST] Segundo turno [/INST]
```

Los tokens BOS (`<s>`) y EOS (`</s>`) son parte del formato — su omisión degrada el rendimiento.

### Content Moderation (14 categorías)

Mistral 7B puede funcionar como **moderador de contenido self-hosted** usando self-reflection prompts. El modelo clasifica inputs en 14 categorías:

- Actividades ilegales
- Contenido de odio / discriminación
- Malware / ciberseguridad maliciosa
- Consejos no calificados (médico, legal, financiero)
- Violaciones de privacidad
- Contenido para adultos
- (+ 8 categorías adicionales)

**Aplicación:** sistema de clasificación self-hosted sin dependencia de APIs externas. Relevante para compliance y moderación on-premise.

### Licencia

**Apache 2.0** → uso comercial sin restricciones. Primera elección para deployments open-source con requisitos de licencia limpios.

### Limitaciones

- 7B parámetros limitan el knowledge almacenado comparado con modelos más grandes
- Vulnerable a jailbreaking sofisticado sin salvaguardas adicionales

---

## Conexiones

- [[entities/gemini]] — alternativa frontier multimodal; comparar con GPT-4 multimodal
- [[concepts/rag]] — todos estos modelos son candidatos para el stack RAG
- [[westtrain-rag-confluence]] — Mistral vía Ollama como LLM on-premise en caso de estudio real
- [[concepts/llm-agents]] — LLaMA y Mistral como opciones open-source para agentes locales
- [[concepts/structured-generation]] — JSON Mode de GPT-4; `response_format` de OpenAI API
- [[concepts/llm-security]] — System messages resistentes a override (GPT-4); content moderation con Mistral
- [[concepts/prompt-engineering]] — Role Prompting (ChatGPT); Steering via System Messages (GPT-4)
