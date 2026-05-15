---
title: "Planning — Técnicas de Razonamiento y Planificación en Agentes"
type: concept
tags: [planning, cot, tree-of-thoughts, react, reflexion, self-consistency, generated-knowledge, art, agentes, razonamiento]
sources: [2026-05-13_lilian-weng-agents.md, 2026-05-13_andrew-ng-agentic-patterns.md, 2026-05-13_reflexion-paper.md, promptingguide-techniques.md]
updated: 2026-05-14
---

# Planning — Técnicas de Razonamiento y Planificación en Agentes

## Qué es el Planning en agentes

El Planning es la capacidad del agente de descomponer tareas complejas en pasos manejables, razonar sobre cómo ejecutarlos, y ajustar el plan basándose en resultados intermedios y errores. Es el componente más difícil de hacer funcionar de forma robusta.

---

## Técnica 1: Chain of Thought (CoT)

**Origen**: Wei et al. 2022

**Mecanismo**: Instruir al LLM con "think step by step" o mostrarle ejemplos de razonamiento paso a paso. Transforma el modelo de un sistema que genera respuestas directas a uno que construye razonamientos explícitos.

**Orígenes duales:**
- **Few-shot CoT** (Wei et al. 2022): proveer ejemplos de cadenas de razonamiento completas en el prompt
- **Zero-shot CoT** (Kojima et al. 2022): simplemente añadir **"Let's think step by step"** — efectivo sin ningún ejemplo; uno de los hallazgos más contraintuitivos de la literatura de prompting

**Cuándo**: tareas de razonamiento lógico, matemáticas, multi-step. Es la base de casi todas las demás técnicas.

**Limitación**: solo explora un camino de razonamiento lineal; si ese camino es incorrecto, no hay recuperación.

---

## Técnica 2: Tree of Thoughts (ToT)

**Orígenes**: Yao et al. 2023 (BFS/DFS con evaluación de estados) + Long 2023 (ToT Controller con RL)

**Mecanismo**: En cada paso del razonamiento, el modelo genera múltiples posibilidades (no solo una) y las evalúa antes de decidir cuál seguir. Forma un árbol donde cada nodo es un estado de razonamiento.

```
Problema
  ├── Razonamiento A ──► A1 ──► A1a (✓ explorar)
  │                  └──► A2 ──► A2a (✗ descartar)
  └── Razonamiento B ──► B1 ──► B1a (✓ explorar)
```

**Búsqueda**: BFS (anchura) o DFS (profundidad). Los estados se evalúan por el propio LLM o por votación mayoritaria.

**Cuándo**: problemas donde el espacio de solución es amplio y explorar múltiples caminos tiene valor (puzzles, planificación creativa). Computacionalmente más costoso que CoT.

**Long 2023 — variante RL**: Introduce un ToT Controller entrenado por RL para guiar la búsqueda, aprendiendo qué caminos son más prometedores cross-dominio.

**Prompt mínimo** (sin implementación de árbol completa):
```
Imagina que tres expertos diferentes están respondiendo a esta pregunta.
Cada experto escribe 1 paso de su razonamiento y lo comparte con el grupo.
Si algún experto se equivoca, ese experto se va.
La pregunta es: [PREGUNTA]
```

---

## Técnica 3: ReAct (Reasoning + Acting)

**Origen**: Yao et al. 2022 (arXiv:2210.03629)

**Mecanismo**: Ciclo iterativo que integra razonamiento y acción:

```
Thought: Necesito saber el precio actual de X
Action: search("precio X 2026")
Observation: El precio de X es $Y
Thought: Con ese dato, puedo calcular...
Action: calculate(Y * 1.2)
Observation: Resultado = $Z
...
Answer: La respuesta es $Z
```

**Por qué es poderoso**: combina el razonamiento interno del LLM con información del mundo real (via herramientas). El "Thought" hace el razonamiento explícito y trazable; el "Action" extiende las capacidades.

**Cuándo**: prácticamente cualquier agente con herramientas. Es el patrón de agente más implementado en producción. [[concepts/langgraph]] lo implementa nativamente con su ciclo nodo-agente → nodo-herramientas.

**Mejor combinación identificada:** **ReAct + CoT + Self-Consistency** supera a cada técnica por separado — CoT (razonamiento interno) + ReAct (información externa) + Self-Consistency (votación mayoritaria).

**Resultados en decision-making:** ALFWorld (navegación textual): ReAct supera a Act-only — el razonamiento explícito es clave para descomponer subobjetivos en pasos ejecutables. WebShop (compras simuladas): ídem.

---

## Técnica 4: Reflexion

**Origen**: Shinn & Labash 2023

**Mecanismo**: El agente detecta que su trayectoria actual está fallando mediante heurísticas y genera una reflexión verbal que informa el próximo intento.

```
Intento 1 → falla detectada por heurística
  → Reflexión: "Fallé porque X. En el próximo intento haré Y"
  → Reflexión guardada en memoria
Intento 2 → usa reflexión como contexto adicional → mejor resultado
```

**Heurísticas de falla**:
- Trayectoria demasiado larga sin progreso (planificación ineficiente)
- Mismas acciones repetidas (alucinación de bucle)

**Implementación**: few-shot con pares (trayectoria fallida, reflexión ideal).

**Arquitectura de 3 componentes** (Shinn et al.):
- **Actor**: Genera texto y acciones (implementado con CoT o ReAct); opera en el entorno y recibe observaciones
- **Evaluator**: Puntúa las trayectorias del Actor (LLM judge o heurísticas rule-based)
- **Self-Reflection**: Genera feedback verbal desde reward + trayectoria + memoria; almacena en LTM para episodios futuros

**Resultado AlfWorld:** ReAct + Reflexion completa **130/134 tareas** (vs. ~50% sin Reflexion).

**Diferencia con Reflection (Ng)**: Reflexion (Shinn) es una técnica específica con heurísticas y memoria persistente de reflexiones. Reflection (Ng) es el patrón general de autoevaluación.

**Resultado empírico** (paper original, Shinn et al. 2023): **91% pass@1 en HumanEval** — superó a GPT-4 (~80%) en el momento de publicación. Complementa el resultado de Ng (GPT-3.5 agente → 95.1%): ambos confirman que los flujos iterativos superan a los modelos base.

**Limitaciones documentadas** (investigación 2024-2025):
- **Confirmation bias**: el mismo modelo genera, evalúa y reflexiona — si razonó mal, reflexionará mal
- **Ruido de trayectoria**: reflexiones sobre trayectorias incorrectas generan generalizaciones sin base → memoria contaminada
- **Solución — Multi-Agent Reflexion (MAR)**: separar el agente evaluador del agente actor
- **Solución — Reflection grounding**: exigir que cada reflexión cite evidencia episódica concreta

**Cuándo**: tareas de múltiples intentos donde el feedback de cada intento puede informar el siguiente; agentes que operan en bucles.

---

## Técnica 5: LLM+P

**Origen**: Liu et al. 2023

**Mecanismo**: Delega el problema de planificación a un motor de planificación clásico (PDDL — Planning Domain Definition Language) en lugar de que el LLM planifique directamente.

```
Problema en lenguaje natural
  → LLM lo traduce a PDDL
  → Motor de planificación clásico genera plan óptimo
  → LLM traduce plan PDDL de vuelta a lenguaje natural
```

**Cuándo**: problemas de planificación bien formalizables donde los LLMs fallan (logística, puzzles con restricciones estrictas). No aplica a tareas abiertas.

---

## Técnica 6: Chain of Hindsight (CoH)

**Origen**: Liu et al. 2023

**Mecanismo**: Fine-tuning del modelo con secuencias de outputs pasados anotados con feedback humano. El modelo aprende a mejorar condicionado en el historial de intentos.

**Cuándo**: cuando se dispone de datos de feedback humano sobre outputs previos del modelo. Más propio de fine-tuning que de prompting en producción.

---

## Técnica 7: Self-Consistency

**Origen:** Wang et al. 2022 — "Self-Consistency Improves Chain of Thought Reasoning in Language Models"

**Mecanismo:** Extiende CoT reemplazando la decodificación greedy (un único path de razonamiento) por votación mayoritaria sobre múltiples paths independientes:

```
Query → CoT path 1 → Respuesta A
      → CoT path 2 → Respuesta A    →  votación mayoritaria → A (final)
      → CoT path 3 → Respuesta B
      → CoT path 4 → Respuesta A
```

**Por qué mejora:** Los paths incorrectos de razonamiento tienden a producir respuestas dispersas; los correctos convergen. La votación marginaliza los paths, no solo las respuestas finales.

**Costo:** N llamadas al LLM (típicamente 5–40) con temperatura > 0 → mayor latencia y costo que CoT simple. Trade-off explícito: accuracy vs. costo.

**Resultado empírico:** +10–20 puntos sobre CoT greedy en benchmarks matemáticos (GSM8K) y de razonamiento lógico.

**Cuándo:** tareas donde se puede tolerar mayor latencia/costo a cambio de máxima accuracy — evaluaciones críticas, matemáticas, razonamiento formal.

---

## Técnica 8: Generated Knowledge Prompting

**Origen:** Liu et al. 2022 — "Generated Knowledge Prompting for Commonsense Reasoning"

**Mecanismo:** Dos fases separadas:

```
Fase 1 — Generación de conocimiento:
"Genera hechos relevantes sobre: [pregunta]"
→ LLM produce afirmaciones de conocimiento implícito sobre el tema

Fase 2 — Predicción con conocimiento:
[conocimiento generado] + [pregunta original]
→ LLM responde usando el conocimiento explicitado en Fase 1
```

**Dominio:** Razonamiento de sentido común donde el LLM tiene el conocimiento en sus pesos pero no lo activa espontáneamente. Benchmarks: NumerSense, CommonsenseQA, OpenBookQA.

**Diferencia con RAG:** El conocimiento es generado por el mismo LLM — no recuperado de fuente externa. Útil cuando no hay base de datos disponible pero el conocimiento existe en los pesos del modelo.

**Limitación:** Si la Fase 1 genera conocimiento incorrecto, contamina la predicción.

**Cuándo:** razonamiento de sentido común, preguntas donde el modelo "sabe la respuesta" pero no la conecta con la pregunta directamente.

---

## Técnica 9: ART — Automatic Reasoning and Tool use

**Origen:** Paranjape et al. 2023 — "ART: Automatic multi-step reasoning and tool-use for large language models"

**Mecanismo:** Combina CoT y tool calls en un LLM congelado (sin fine-tuning) via una **Task Library**:

```
Task Library: colección de tareas con demostraciones CoT + tool calls

Nueva tarea → selección automática de 2-shot ejemplos similares
    ↓
LLM genera razonamiento CoT
    ↓ [si necesita herramienta, pausa la generación]
Tool call: search / calculate / execute → resultado insertado en contexto
    ↓ [reanuda generación]
LLM completa el razonamiento → respuesta final
```

**LLM congelado:** No requiere fine-tuning. La Task Library provee el formato CoT + herramientas via few-shot; los pesos del modelo no se modifican.

**Extensibilidad humana:** La Task Library es editable → añadir herramientas y corregir errores sin tocar el modelo base.

**Resultados:**
- Supera few-shot CoT en la mayoría de tareas de **BigBench** (reasoning, NLP)
- Supera few-shot CoT en **MMLU** (multidisciplinary knowledge)
- Comparable a fine-tuning en algunos benchmarks sin entrenamiento adicional

**Relación con ReAct:** Comparten el ciclo razonamiento + acción. ART añade la Task Library para reproducibilidad cross-task y la pausa explícita de generación para tool calls como mecanismo formal.

**Cuándo:** tareas multi-step que requieren herramientas externas (búsqueda, cálculo, APIs) sin querer modificar el modelo base.

---

## Técnica 10: PAL — Program-Aided Language Models

**Origen:** Gao et al. 2022

**Mecanismo:** En lugar de razonar en texto libre (CoT), el LLM genera **código Python** como pasos de razonamiento intermedio. El cálculo es delegado a un intérprete Python determinista.

```
Pregunta en lenguaje natural
    ↓
LLM genera código Python (razonamiento como programa)
    ↓
Intérprete Python ejecuta → resultado determinista y correcto
```

**Diferencia clave respecto a CoT:** CoT delega el razonamiento Y el cálculo al LLM → puede "calcular" mal. PAL separa razonamiento (LLM) de ejecución (intérprete) → garantía de correctitud.

**Extensibilidad:** El runtime puede ser cualquier sistema simbólico: Python, Wolfram Alpha, SQL.

**Cuándo:** Aritmética precisa, manipulación de fechas, razonamiento lógico estructurado — cualquier tarea donde la ejecución determinista supera la "aritmética" del LLM.

---

## Técnica 11: APE — Automatic Prompt Engineer

**Origen:** Zhou et al. 2022 — "Large Language Models are Human-Level Prompt Engineers"

**Mecanismo:** Automatiza la búsqueda del prompt óptimo como black-box optimization:
1. LLM recibe output demonstrations → genera candidatos de instrucción
2. Las instrucciones se ejecutan en un target model
3. Se selecciona la instrucción con mejor evaluation score

**Hallazgo crítico:** APE descubrió un zero-shot CoT mejor que el humano "Let's think step by step":
> "Let's work this out in a step by step way to be sure we have the right answer."

Esta variante supera al CoT original de Kojima et al. en MultiArith y GSM8K.

**Implicación:** El espacio de prompts es vasto; los prompts óptimos no son necesariamente los que los humanos escribirían intuitivamente.

**Cuándo:** Cuando el costo de prompt engineering manual es alto y existe un conjunto de evaluación para medir calidad de instrucciones.

---

## Comparativa de técnicas

| Técnica | Tipo | Herramientas | Costo | Mejor para |
|---------|------|-------------|-------|-----------|
| **CoT** | Prompting | No | Bajo | Razonamiento multi-paso en un pase |
| **Self-Consistency** | Prompting × N | No | Alto (N llamadas) | Máxima accuracy en razonamiento formal |
| **Generated Knowledge** | Prompting (2 fases) | No | Bajo | Sentido común, conocimiento implícito |
| **ToT** | Prompting + búsqueda | No | Muy alto | Espacio de solución amplio, puzzles |
| **ReAct** | Prompting + Loop | Sí | Medio | Agentes con herramientas (default) |
| **ART** | CoT + Tools + Library | Sí | Medio | Multi-step con herramientas, reproducible |
| **PAL** | CoT → código → intérprete | No (Python) | Bajo | Aritmética, fechas, razonamiento lógico formal |
| **APE** | Búsqueda automática de prompts | No | Variable | Optimizar instrucciones sin prompt engineering manual |
| **Reflexion** | Memoria + Loop (3 componentes) | Opcional | Medio | Tareas de múltiples intentos |
| **LLM+P** | Arquitectura híbrida | Sí (motor PDDL) | Medio | Planificación formal bien definida |

---

## El desafío fundamental del Planning

> Los LLMs fallan al ajustar planes ante errores inesperados y son menos robustos que los humanos en exploración por prueba y error. — Lilian Weng

El planning sigue siendo el componente más frágil de los agentes. Las técnicas anteriores mitigan pero no resuelven este problema. La robustez del planning es una de las fronteras activas de investigación.

---

## Conexiones

- [[concepts/llm-agents]] — Planning como uno de los tres componentes del agente
- [[concepts/agentic-design-patterns]] — Planning como uno de los cuatro patrones de Ng; ART relacionado con Tool Use
- [[concepts/prompt-engineering]] — Zero-shot y Few-shot como fundamentos de CoT y todas las técnicas
- [[concepts/langgraph]] — implementa ReAct y multi-step planning nativamente
- [[crewai]] — planning via manager agent y task sequencing
- [[lilian-weng-agents]] — fuente primaria de técnicas (Weng)
- [[promptingguide-techniques]] — fuente de Self-Consistency, Generated Knowledge, ART, Zero-shot CoT (Kojima)
- [[reflexion-paper]] — Reflexion como evolución de CoT con memoria persistente
