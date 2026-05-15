---
title: "Prompt Engineering Guide — Introducción: Settings, Basics, Elements, Tips (DAIR-AI)"
type: source-summary
tags: [prompt-engineering, llm-settings, temperature, top-p, prompt-elements, prompt-design, frequency-penalty, presence-penalty]
sources: []
updated: 2026-05-14
---

# Prompt Engineering Guide — Introducción (DAIR-AI)

**Fuente:** https://www.promptingguide.ai/introduction  
**Artículos procesados:** LLM Settings, Basics of Prompting, Elements of a Prompt, General Tips for Designing Prompts

---

## LLM Settings

Los parámetros de configuración del LLM controlan el comportamiento de la generación. La configuración correcta es tan importante como el prompt.

### Temperature

- **Bajo (→0):** resultados más deterministas — selecciona los tokens de mayor probabilidad
- **Alto (→1+):** mayor aleatoriedad y diversidad creativa
- **Guía:** temperatura baja para QA factual; temperatura alta para tareas creativas (poesía, brainstorming)

### Top P (Nucleus Sampling)

Solo se consideran los tokens que comprenden la masa de probabilidad `top_p`:
- **Bajo:** pool restringido → respuestas predecibles y confiadas
- **Alto:** pool amplio → outputs más diversos

**Regla crítica:** ajustar temperatura **OR** Top P — nunca ambos simultáneamente.

### Max Length

Número máximo de tokens generados. Controla costos y previene respuestas largas o irrelevantes.

### Stop Sequences

Strings que detienen la generación al aparecer. Ejemplo: añadir `"11"` como stop sequence limita una lista a exactamente 10 ítems.

### Frequency Penalty vs. Presence Penalty

| Parámetro | Tipo de penalidad | Efecto principal |
|-----------|-----------------|-----------------|
| **Frequency Penalty** | Proporcional a la frecuencia | Cuanto más aparece un token, más se penaliza; reduce repetición de palabras concretas |
| **Presence Penalty** | Uniforme/binaria (independiente de cuántas veces apareció) | Fomenta exploración de vocabulario diverso |

**Distinción frecuentemente confundida:** Frequency Penalty acumula penalidad por repetición; Presence Penalty aplica igual a la primera y la décima aparición.

**Regla crítica:** ajustar frequency **OR** presence penalty — nunca ambos simultáneamente.

---

## Basics of Prompting

**Formatos estándar:**

```
# Zero-shot
Clasifica el sentimiento: "La película fue excelente."
Sentimiento:

# Few-shot
Texto: "El servicio fue malo."      → Sentimiento: negativo
Texto: "Experiencia increíble."     → Sentimiento: positivo
Texto: "La película fue excelente." → Sentimiento:
```

**Insight clave:** Los modelos modernos infieren la intención desde la **estructura visual** del prompt sin necesitar labels explícitos ("Q:", "A:"). El formato comunica el patrón esperado; el modelo infiere que debe continuar el patrón.

---

## Elements of a Prompt

Un prompt efectivo puede componer hasta 4 componentes:

| Componente | Función | Obligatorio |
|---|---|---|
| **Instruction** | La tarea específica que el modelo debe realizar | Siempre |
| **Context** | Información externa que dirige al modelo hacia mejores respuestas | No (mejora consistencia) |
| **Input Data** | El dato o pregunta a procesar | Cuando aplica |
| **Output Indicator** | El tipo o formato del output esperado | No (ayuda al formato) |

**Ejemplo (clasificación de sentimiento):**
```
Instruction:     Classify the text into neutral, negative or positive.
Input Data:      I think the food was okay.
Output Indicator: Sentiment:
```

**Principio:** No todos los cuatro elementos son necesarios siempre. La estructura del prompt se adapta al caso de uso. La fórmula es flexible, no rígida.

---

## General Tips for Designing Prompts

### Tip 1: Empezar simple e iterar

El prompt engineering es iterativo. Comenzar con prompts básicos → añadir elementos → medir → refinar. Romper tareas complejas en subtareas antes de escalar.

### Tip 2: Instrucciones explícitas con verbos de comando

Colocar la instrucción al **inicio** del prompt. Verbos efectivos: Write, Classify, Summarize, Translate, Order, Explain.

Usar separadores claros entre instrucción y contexto:
```
### Instruction ###
Clasifica el texto en positivo, negativo o neutral.

Text: {input}
Sentiment:
```

### Tip 3: Especificidad sobre vaguedad

| ❌ Vago | ✓ Específico |
|---------|-------------|
| "Explain prompt engineering. Keep it short." | "Use 2-3 sentences to explain prompt engineering to a high school student." |
| "Summarize this article." | "Write a 3-bullet summary of the key findings for a technical audience." |

Especificaciones claras (número de oraciones, nivel de audiencia, formato) → resultados predecibles.

### Tip 4: Do vs. Don't — el efecto perverso de las prohibiciones

**Hallazgo empírico contraintuitivo:** Las prohibiciones ("DO NOT ask for X") frecuentemente **producen el comportamiento prohibido**. El modelo procesa la acción mencionada incluso en contexto negativo.

**Ejemplo documentado:** El prompt `"DO NOT ASK FOR INTERESTS"` llevó al agente a preguntar sobre intereses del usuario — exactamente lo contrario de lo instruido.

**Solución:** Reemplazar prohibiciones con instrucciones afirmativas que describan el comportamiento deseado.

| ❌ Prohibición (frágil) | ✓ Instrucción positiva (robusta) |
|------------------------|--------------------------------|
| `"DO NOT ASK FOR INTERESTS"` | `"Ask only for the user's name, then recommend a recipe based on common preferences."` |
| `"Don't make the explanation too long."` | `"Explain in exactly 2 sentences."` |

---

## Conexiones

- [[concepts/prompt-engineering]] — Zero-shot, Few-shot, ICL como fundamentos
- [[concepts/llm-settings]] — página de concepto para parámetros de LLM (árbol de decisión completo)
- [[promptingguide-techniques]] — técnicas avanzadas que construyen sobre estos fundamentos
- [[concepts/structured-generation]] — constrained decoding como formalización del control de formato
