---
title: "Prompt Engineering — Fundamentos"
type: concept
tags: [prompt-engineering, zero-shot, few-shot, in-context-learning, instruction-tuning, rlhf]
sources: [promptingguide-techniques.md]
updated: 2026-05-14
---

# Prompt Engineering — Fundamentos

## Qué es

El prompt engineering es el conjunto de técnicas para diseñar y optimizar los inputs a un LLM con el fin de obtener outputs de mejor calidad. Es el punto de entrada a todas las demás técnicas de razonamiento y planificación.

Jerarquía fundamental:

```
Zero-shot → Few-shot → Chain of Thought → [técnicas avanzadas de reasoning]
```

---

## Zero-shot Prompting

El modelo responde a la tarea sin ningún ejemplo previo. Depende de:
1. **Instruction tuning** — fine-tuning en datasets de instrucciones (el modelo aprende a seguir el formato "instrucción → respuesta")
2. **RLHF** — alineación con preferencias humanas que refuerza respuestas útiles y correctamente formateadas

```
Clasifica el sentimiento del siguiente texto:
Texto: "La película estuvo fenomenal."
Sentimiento:
```

**Cuándo funciona bien:** Tasks estándar (clasificación, resumen, traducción, Q&A) donde el modelo ha visto suficientes ejemplos durante pretraining e instruction tuning.

**Cuándo falla:** Razonamiento multi-paso complejo, formatos de output muy específicos, knowledge de dominio estrecho.

---

## Few-shot Prompting

**Origen:** Brown et al. 2020 — GPT-3 demostró que LLMs de suficiente escala pueden "aprender" en contexto (**in-context learning, ICL**) a partir de ejemplos en el prompt, sin actualización de pesos.

```
Texto: "El servicio fue terrible." → Sentimiento: negativo
Texto: "Experiencia increíble."   → Sentimiento: positivo
Texto: "Llegó a tiempo."          → Sentimiento:
```

Los ejemplos condicionan el modelo sobre el formato, la distribución y el espacio de etiquetas de la tarea.

### El rol real de las etiquetas (Min et al. 2022)

Hallazgo contraintuitivo: **los modelos mantienen gran parte de su performance incluso con etiquetas aleatorias en los ejemplos few-shot**.

Lo que el modelo aprende del few-shot:
1. **El espacio de etiquetas** — qué opciones son válidas
2. **La distribución de inputs** — de qué tipo son los ejemplos de entrada
3. **El formato de la respuesta** — cómo debe estructurarse el output

El conocimiento de "qué etiqueta va con qué input" viene del **pretraining**, no del few-shot.

**Implicación:** No es necesario tener ejemplos perfectamente etiquetados — sí es necesario que sean representativos del formato y la distribución del task. El formato importa más que la "corrección" de las etiquetas.

### Limitaciones

- Tokens de context window consumidos por ejemplos (costo de contexto)
- Sensible al orden de los ejemplos y a su elección
- Para tasks muy específicos puede ser insuficiente → considerar fine-tuning

---

## La jerarquía completa de técnicas

| Técnica | Fundamento | Ver |
|---------|-----------|-----|
| Zero-shot | Instruction tuning + RLHF | Esta página |
| Few-shot | ICL (Brown et al. 2020) | Esta página |
| CoT (zero-shot) | "Let's think step by step" (Kojima 2022) | [[concepts/planning]] |
| CoT (few-shot) | Ejemplos de razonamiento (Wei 2022) | [[concepts/planning]] |
| Self-Consistency | Múltiples CoT + votación (Wang 2022) | [[concepts/planning]] |
| Generated Knowledge | Genera contexto antes de predecir (Liu 2022) | [[concepts/planning]] |
| Tree of Thoughts | Árbol de razonamiento (Yao 2023 + Long 2023) | [[concepts/planning]] |
| ART | CoT + herramientas + Task Library (Paranjape 2023) | [[concepts/planning]] |
| RAG | Retrieval + generación (Lewis 2021) | [[concepts/rag]] |

---

## Cuándo usar qué

| Situación | Técnica recomendada |
|-----------|-------------------|
| Prototipado rápido, task estándar | Zero-shot |
| Formato específico o task poco común | Few-shot |
| Razonamiento complejo sin ejemplos | Zero-shot CoT |
| Máxima accuracy, latencia aceptable | Self-Consistency |
| Espacio de solución amplio, puzzles | ToT |
| Task multi-step con herramientas | ART o ReAct |
| Conocimiento externo o privado | RAG |

---

## Conexiones

- [[promptingguide-techniques]] — fuente primaria: 9 artículos con los papers de referencia
- [[concepts/planning]] — técnicas avanzadas de razonamiento (CoT, Self-Consistency, Generated Knowledge, ToT, ART, ReAct, Reflexion)
- [[concepts/rag]] — Lewis et al. 2021 como origen del paradigma RAG
- [[concepts/llm-agents]] — prompting como base del ciclo agente (Planning + Memory + Tools)
- [[concepts/agentic-design-patterns]] — Tool Use y Reflection como extensiones del prompting básico
- [[lilian-weng-agents]] — CoT/ToT desde perspectiva de planning agéntico
