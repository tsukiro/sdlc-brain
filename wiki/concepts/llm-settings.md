---
title: "LLM Settings — Parámetros de Generación"
type: concept
tags: [llm-settings, temperature, top-p, nucleus-sampling, frequency-penalty, presence-penalty, stop-sequences]
sources: [promptingguide-introduction.md]
updated: 2026-05-14
---

# LLM Settings — Parámetros de Generación

Los parámetros de configuración controlan el comportamiento de la generación. La configuración correcta es tan importante como el prompt.

---

## Temperature

Controla la aleatoriedad del muestreo de tokens.

- **Bajo (→ 0):** el modelo selecciona tokens de mayor probabilidad → resultados más deterministas y consistentes
- **Alto (→ 1+):** distribución más aplanada → mayor aleatoriedad y diversidad creativa

**Guía práctica:**

| Tipo de tarea | Temperature recomendada |
|---------------|------------------------|
| QA factual, extracción de datos, SQL | 0.0 – 0.3 |
| Clasificación, resumen, traducción | 0.3 – 0.7 |
| Redacción, copywriting | 0.7 – 1.0 |
| Poesía, brainstorming, ficción | 1.0+ |

---

## Top P (Nucleus Sampling)

Solo se consideran los tokens que comprenden la masa de probabilidad acumulada `top_p`.

- **Bajo (ej. 0.1):** pool restringido a los tokens más probables → respuestas predecibles y confiadas
- **Alto (ej. 0.9):** pool más amplio → outputs más diversos

**Regla crítica:** ajustar temperatura **OR** Top P — nunca ambos simultáneamente. Cambiar los dos hace el comportamiento impredecible.

---

## Max Length (Max Tokens)

Número máximo de tokens generados.

- Controla costos directamente (tokens generados = costo)
- Previene respuestas largas o que se disparen del foco
- No hace que el modelo genere exactamente esa cantidad — es un techo

---

## Stop Sequences

Strings que detienen la generación al aparecer en el output.

**Ejemplo:** añadir `"11"` como stop sequence a una lista numerada limita automáticamente a exactamente 10 ítems, sin necesitar instrucciones adicionales sobre longitud.

Útiles para controlar formato cuando el output sigue patrones estructurados (listas numeradas, secciones marcadas, código).

---

## Frequency Penalty vs. Presence Penalty

La distinción más frecuentemente confundida entre los parámetros de LLM.

| Parámetro | Tipo de penalidad | Efecto principal |
|-----------|-----------------|-----------------|
| **Frequency Penalty** | Proporcional a la frecuencia | Cuanto más aparece un token, más se penaliza; reduce la repetición de palabras concretas de forma acumulativa |
| **Presence Penalty** | Uniforme/binaria (independiente de cuántas veces apareció) | Aplica la misma penalidad si el token apareció una o diez veces → fomenta exploración de vocabulario diverso |

**Diferencia clave:** Frequency Penalty *acumula* penalidad por cada aparición adicional. Presence Penalty penaliza *por existencia* del token en el output previo — una vez aparece, la penalidad es constante.

**Regla crítica:** ajustar frequency **OR** presence penalty — nunca ambos simultáneamente.

**Guía de uso:**

| Objetivo | Parámetro |
|----------|-----------|
| Reducir repetición literal de palabras | Frequency Penalty |
| Forzar vocabulario más diverso | Presence Penalty |
| Output predictable, sin penalizar repetición | Ambos a 0 |

---

## Árbol de decisión

```
¿Necesito resultados consistentes entre runs?
    → Sí: Temperature ≤ 0.2 (+ seed si disponible)
    → No: subir temperatura según creatividad requerida

¿El output repite mucho las mismas palabras?
    → Frequency Penalty (castiga la repetición acumulativa)

¿Quiero que el modelo explore más vocabulario?
    → Presence Penalty (castiga cualquier token ya usado)

¿Necesito controlar la longitud del output?
    → Max Length como techo + Stop Sequences para formato específico
```

---

## Conexiones

- [[promptingguide-introduction]] — fuente primaria: LLM Settings, Basics, Elements, Tips
- [[concepts/prompt-engineering]] — los parámetros complementan el diseño del prompt
- [[entities/gemini]] — `temperature`, `top_p`, `top_k` en la API de Gemini
