---
title: "Prompt Engineering Guide — Técnicas Fundamentales (DAIR-AI)"
type: source-summary
tags: [prompt-engineering, zero-shot, few-shot, cot, self-consistency, generated-knowledge, tot, rag, art, chain-of-thought]
sources: []
updated: 2026-05-14
---

# Prompt Engineering Guide — Técnicas Fundamentales (DAIR-AI)

**Fuente:** https://www.promptingguide.ai/techniques  
**Organización:** DAIR-AI (Democratizing AI Research)  
**Procesado:** 9 artículos individuales (Zero-shot, Few-shot, CoT, Self-Consistency, Generated Knowledge, Prompt Chaining*, ToT, RAG, ART)  
\* Prompt Chaining: artículo sin contenido procesable en el momento del ingest (solo placeholder "needs translation").

---

## 1. Zero-shot Prompting

El modelo responde directamente a la tarea sin ningún ejemplo previo:

```
Clasifica el siguiente texto como positivo, negativo o neutral:
Texto: "Este viaje fue increíble."
Sentimiento:
```

**Por qué funciona:** La **instruction tuning** (fine-tuning en datasets de instrucciones) y el **RLHF** permiten que los LLMs modernos generalicen a tareas no vistas a partir de instrucciones en lenguaje natural. Zero-shot no existía como capacidad en GPT-2 — es un resultado directo del entrenamiento post-pretraining moderno.

**Limitación:** Falla en tareas que requieren razonamiento multi-paso complejo o conocimiento de dominio muy estrecho.

---

## 2. Few-shot Prompting

**Origen principal:** Brown et al. 2020 (GPT-3 paper) — demostró el **in-context learning (ICL)** a escala: LLMs suficientemente grandes pueden "aprender" tareas a partir de ejemplos en el prompt sin actualización de pesos.

```
El cielo es azul.    → positivo
La hierba es verde.  → positivo
Esto es horrible.    → negativo
El vuelo fue okay.   →
```

### Hallazgo clave: el rol de las etiquetas (Min et al. 2022)

Resultado contraintuitivo: **los modelos mantienen gran parte de su performance con etiquetas aleatorias en los ejemplos few-shot**.

Lo que el modelo aprende del few-shot:
1. **El espacio de etiquetas** — qué opciones son válidas como respuesta
2. **La distribución de inputs** — de qué tipo son los textos de entrada
3. **El formato de la respuesta** — cómo debe estructurarse el output

El conocimiento de "qué etiqueta es correcta" viene del **pretraining**, no del few-shot.

**Implicación práctica:** No es necesario tener ejemplos perfectamente etiquetados — sí es necesario que sean representativos del formato y la distribución del task.

---

## 3. Chain of Thought (CoT)

**Orígenes duales:**
- **Few-shot CoT** (Wei et al. 2022): añadir ejemplos de razonamiento paso a paso en el prompt
- **Zero-shot CoT** (Kojima et al. 2022): simplemente añadir **"Let's think step by step"** — sorprendentemente efectivo sin ningún ejemplo

El zero-shot CoT es uno de los hallazgos más contraintuitivos de la literatura: un solo string habilita razonamiento que sin él el modelo falla consistentemente.

Ver técnicas completas en [[concepts/planning]].

---

## 4. Self-Consistency

**Origen:** Wang et al. 2022 — "Self-Consistency Improves Chain of Thought Reasoning in Language Models"

**Mecanismo:** Reemplaza la decodificación greedy (un único path de razonamiento) por votación mayoritaria sobre múltiples paths independientes:

```
Query → CoT path 1 → Respuesta A
      → CoT path 2 → Respuesta A   ← votación mayoritaria → Respuesta A (final)
      → CoT path 3 → Respuesta B
      → CoT path 4 → Respuesta A
```

**Por qué mejora:** Los paths incorrectos de razonamiento tienden a divergir en sus respuestas; los paths correctos convergen. La votación marginaliza los caminos de razonamiento, no solo las respuestas finales.

**Costo:** N llamadas al LLM (típicamente 5–40 samples con temperatura > 0) → mayor latencia y costo que CoT simple. Trade-off explícito: accuracy vs. costo.

**Resultado empírico:** Mejoras consistentes de 10–20 puntos sobre CoT greedy en benchmarks matemáticos (GSM8K) y de razonamiento lógico.

---

## 5. Generated Knowledge Prompting

**Origen:** Liu et al. 2022 — "Generated Knowledge Prompting for Commonsense Reasoning"

**Mecanismo:** Dos fases separadas:

**Fase 1 — Generar conocimiento:**
```
Genera hechos relevantes sobre: ¿Los golfistas obtienen más puntos
haciendo más golpes?
→ "En golf, el objetivo es completar el hoyo con el menor número de golpes..."
→ "Un birdie (un golpe menos que el par) es mejor que un par..."
```

**Fase 2 — Predecir con conocimiento:**
```
[conocimiento generado] + "¿Los golfistas obtienen más puntos
haciendo más golpes? Responde Sí o No."
→ "No"
```

**Dominio:** Razonamiento de sentido común donde el LLM tiene el conocimiento en sus pesos pero no lo activa espontáneamente. Benchmarks: NumerSense, CommonsenseQA, OpenBookQA.

**Diferencia con RAG:** El conocimiento es generado por el mismo LLM (no recuperado de fuente externa). Útil cuando no hay base de datos disponible pero el conocimiento existe en los pesos del modelo.

**Limitación:** Si la Fase 1 genera conocimiento incorrecto, contamina la predicción en la Fase 2.

---

## 6. Prompt Chaining

*(Artículo sin contenido procesable al momento del ingest — solo placeholder de traducción)*

El concepto refiere a encadenar múltiples prompts donde el output de uno es el input del siguiente. Técnica de descomposición de tareas complejas. Relacionado con [[concepts/planning]] (multi-step) y [[concepts/agentic-design-patterns]] (Reflection + Tool Use).

---

## 7. Tree of Thoughts (ToT)

**Orígenes:**
- **Yao et al. 2023** — versión principal: árbol de razonamiento con búsqueda BFS/DFS y evaluación explícita de estados
- **Long 2023** — variante con ToT Controller entrenado por RL

**Extensión de CoT:** En CoT el razonamiento es una cadena lineal; en ToT es un árbol donde cada nodo es un "pensamiento" (paso de razonamiento intermedio) y el modelo explora múltiples ramas.

**Evaluación de estados:** El LLM evalúa cada nodo como `sure / maybe / impossible`, guiando la búsqueda:
- **BFS:** explora todos los nodos a profundidad k antes de profundizar
- **DFS:** explora una rama hasta el final antes de hacer backtrack

**Prompt mínimo** (sin implementación compleja de árbol):
```
Imagina que tres expertos diferentes están respondiendo a esta pregunta.
Cada experto escribe 1 paso de su razonamiento y lo comparte con el grupo.
Si algún experto se equivoca en algún momento, ese experto se va.
La pregunta es: [PREGUNTA]
```

**Long 2023 — variante RL:** Introduce un ToT Controller entrenado por RL para aprender qué caminos son más prometedores cross-dominio, con mayor capacidad de generalización.

Ver implementación completa en [[concepts/planning]].

---

## 8. RAG como técnica de prompting

**Origen:** Lewis et al. 2021 — "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks" (NeurIPS 2021)

**Modelo conceptual fundamental:**
- **Memoria paramétrica:** el conocimiento en los pesos del LLM (fija, solo actualizable via fine-tuning)
- **Memoria no-paramétrica:** un índice vectorial denso consultable en tiempo de inferencia

El paper original usó **DPR (Dense Passage Retrieval)** sobre un índice vectorial completo de Wikipedia. Retrieve → Augment → Generate: el generador (seq2seq) recibe los pasajes recuperados como contexto y produce respuestas grounded.

Esta separación paramétrico/no-paramétrico sigue siendo el modelo mental correcto para RAG: el LLM aporta razonamiento y síntesis; el índice aporta conocimiento actualizado, privado o especializado.

Ver pipeline completo en [[concepts/rag]].

---

## 9. ART — Automatic Reasoning and Tool use

**Origen:** Paranjape et al. 2023 — "ART: Automatic multi-step reasoning and tool-use for large language models"

**Mecanismo:** Combina CoT y tool calls en un LLM congelado (sin fine-tuning) via una **Task Library**:

```
Task Library: colección de tareas con demostraciones CoT + tool calls

Nueva tarea → selección automática de 2-shot ejemplos similares de la biblioteca
    ↓
LLM genera razonamiento CoT
    ↓ [si necesita herramienta, pausa la generación]
Tool call: search / calculate / execute → resultado insertado en contexto
    ↓ [reanuda generación]
LLM completa el razonamiento → respuesta final
```

**LLM congelado:** Funciona sin fine-tuning. La Task Library provee el formato CoT + herramientas via few-shot — no se modifican los pesos del modelo.

**Extensibilidad humana:** La Task Library es editable → añadir herramientas y corregir errores sin tocar el modelo base.

**Resultados:**
- Supera few-shot CoT en la mayoría de tareas de **BigBench** (reasoning, NLP)
- Supera few-shot CoT en **MMLU** (multidisciplinary knowledge)
- Comparable a fine-tuning en algunos benchmarks sin entrenamiento adicional

**Relación con ReAct:** Comparten el ciclo razonamiento + acción. ART añade la Task Library (reproducibilidad cross-task) y la pausa explícita de generación para tool calls como mecanismo formal.

---

## Jerarquía de técnicas

```
Prompting foundation
├── Zero-shot (instruction tuning + RLHF)
└── Few-shot / ICL (Brown et al. 2020; format > labels: Min et al. 2022)
    └── CoT (razonamiento explícito)
        ├── Few-shot CoT (Wei et al. 2022)
        ├── Zero-shot CoT ("Let's think step by step" — Kojima et al. 2022)
        ├── Self-Consistency (múltiples CoT + votación — Wang et al. 2022)
        ├── Generated Knowledge (genera conocimiento antes — Liu et al. 2022)
        └── ART (CoT + herramientas + Task Library — Paranjape et al. 2023)

Tree of Thoughts  (árbol multi-rama — Yao et al. 2023 + Long 2023)
RAG               (recuperación + generación — Lewis et al. 2021)
```

---

## Papers clave

| Técnica | Paper | Año |
|---------|-------|-----|
| Few-shot / ICL | Brown et al. (GPT-3) | 2020 |
| RAG | Lewis et al. (NeurIPS) | 2021 |
| CoT (few-shot) | Wei et al. | 2022 |
| CoT (zero-shot) | Kojima et al. | 2022 |
| Self-Consistency | Wang et al. | 2022 |
| Generated Knowledge | Liu et al. | 2022 |
| Label role in few-shot | Min et al. | 2022 |
| ToT (BFS/DFS) | Yao et al. | 2023 |
| ToT (RL controller) | Long | 2023 |
| ART | Paranjape et al. | 2023 |

---

## Conexiones

- [[concepts/prompt-engineering]] — Zero-shot y Few-shot como fundamentos
- [[concepts/planning]] — CoT, Self-Consistency, Generated Knowledge, ToT, ART: técnicas de razonamiento agéntico
- [[concepts/rag]] — Lewis et al. 2021 como origen del paradigma RAG
- [[lilian-weng-agents]] — ToT y ReAct desde perspectiva de agentes (Weng)
- [[andrew-ng-agentic-patterns]] — ART relacionado con el Tool Use pattern (Ng)
- [[reflexion-paper]] — Reflexion como evolución de CoT con memoria verbal y múltiples intentos
