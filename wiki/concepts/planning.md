---
title: "Planning — Técnicas de Razonamiento y Planificación en Agentes"
type: concept
tags: [planning, cot, tree-of-thoughts, react, reflexion, agentes, razonamiento]
sources: [2026-05-13_lilian-weng-agents.md, 2026-05-13_andrew-ng-agentic-patterns.md, 2026-05-13_reflexion-paper.md]
updated: 2026-05-13
---

# Planning — Técnicas de Razonamiento y Planificación en Agentes

## Qué es el Planning en agentes

El Planning es la capacidad del agente de descomponer tareas complejas en pasos manejables, razonar sobre cómo ejecutarlos, y ajustar el plan basándose en resultados intermedios y errores. Es el componente más difícil de hacer funcionar de forma robusta.

---

## Técnica 1: Chain of Thought (CoT)

**Origen**: Wei et al. 2022

**Mecanismo**: Instruir al LLM con "think step by step" o mostrarle ejemplos de razonamiento paso a paso. Transforma el modelo de un sistema que genera respuestas directas a uno que construye razonamientos explícitos.

**Variantes**:
- **Zero-shot CoT**: simplemente añadir "Let's think step by step" al prompt
- **Few-shot CoT**: proveer ejemplos de cadenas de razonamiento completas

**Cuándo**: tareas de razonamiento lógico, matemáticas, multi-step. Es la base de casi todas las demás técnicas.

**Limitación**: solo explora un camino de razonamiento lineal; si ese camino es incorrecto, no hay recuperación.

---

## Técnica 2: Tree of Thoughts (ToT)

**Origen**: Yao et al. 2023

**Mecanismo**: En cada paso del razonamiento, el modelo genera múltiples posibilidades (no solo una) y las evalúa antes de decidir cuál seguir. Forma un árbol donde cada nodo es un estado de razonamiento.

```
Problema
  ├── Razonamiento A ──► A1 ──► A1a (✓ explorar)
  │                  └──► A2 ──► A2a (✗ descartar)
  └── Razonamiento B ──► B1 ──► B1a (✓ explorar)
```

**Búsqueda**: BFS (anchura) o DFS (profundidad). Los estados se evalúan por el propio LLM o por votación mayoritaria.

**Cuándo**: problemas donde el espacio de solución es amplio y explorar múltiples caminos tiene valor (puzzles, planificación creativa). Computacionalmente más costoso que CoT.

---

## Técnica 3: ReAct (Reasoning + Acting)

**Origen**: Yao et al. 2023

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

## Comparativa de técnicas

| Técnica | Tipo | Herramientas | Costo | Mejor para |
|---------|------|-------------|-------|-----------|
| **CoT** | Prompting | No | Bajo | Razonamiento multi-paso en un pase |
| **ToT** | Prompting | No | Alto | Problemas con espacio de solución amplio |
| **ReAct** | Prompting + Loop | Sí | Medio | Agentes con herramientas (default) |
| **Reflexion** | Memoria + Loop | Opcional | Medio | Tareas de múltiples intentos |
| **LLM+P** | Arquitectura híbrida | Sí (motor PDDL) | Medio | Planificación formal bien definida |

---

## El desafío fundamental del Planning

> Los LLMs fallan al ajustar planes ante errores inesperados y son menos robustos que los humanos en exploración por prueba y error. — Lilian Weng

El planning sigue siendo el componente más frágil de los agentes. Las técnicas anteriores mitigan pero no resuelven este problema. La robustez del planning es una de las fronteras activas de investigación.

---

## Conexiones

- [[concepts/llm-agents]] — Planning como uno de los tres componentes del agente
- [[concepts/agentic-design-patterns]] — Planning como uno de los cuatro patrones de Ng
- [[concepts/langgraph]] — implementa ReAct y multi-step planning nativamente
- [[crewai]] — planning via manager agent y task sequencing
- [[lilian-weng-agents]] — fuente primaria de técnicas
