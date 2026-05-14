---
title: "Reflexion — Language Agents with Verbal Reinforcement Learning"
type: source-summary
tags: [reflexion, reinforcement-learning, planning, memoria, agentes, paper]
sources: [2026-05-13_reflexion-paper.md]
updated: 2026-05-13
---

# Reflexion — Language Agents with Verbal Reinforcement Learning

**Fuente:** https://arxiv.org/abs/2303.11366  
**Autores:** Shinn, Cassano, Labash et al.

## Perspectiva de esta fuente

Paper original de Reflexion. Complementa y profundiza lo que Lilian Weng describe en su artículo. La contribución clave: un mecanismo concreto para que los agentes aprendan de errores **sin reentrenar el modelo**.

## El resultado central

**91% pass@1 en HumanEval** — superó a GPT-4 (~80%) en el momento de publicación (2023).

Este resultado complementa el de Andrew Ng (GPT-3.5 con agente iterativo → 95.1%): ambos muestran que los flujos iterativos superan los modelos base en modo zero-shot.

## El mecanismo: Verbal RL

En lugar de actualizar pesos (RL tradicional), Reflexion actualiza **texto en memoria**:
- El feedback se expresa en lenguaje natural
- Las reflexiones se almacenan en un buffer episódico
- Los intentos subsiguientes leen ese buffer como contexto adicional

```
Intento 1 → falla
  → LLM genera reflexión verbal: "El error fue X porque Y. Debería Z."
  → Reflexión → memoria episódica
Intento 2 → usa memoria como contexto → mejor resultado
```

## Ventaja vs. RL tradicional

- No requiere fine-tuning (sin costo de GPU/tiempo)
- Funciona con cualquier LLM mediante prompting
- Las reflexiones son interpretables por humanos
- Las memorias pueden inspeccionarse y corregirse manualmente

## Limitaciones identificadas (paper + investigación posterior)

1. **Confirmation bias**: el mismo modelo genera acciones, evalúa y reflexiona. Si razonó mal, probablemente reflexionará mal.
2. **Ruido de trayectoria**: reflexiones sobre trayectorias incorrectas pueden generar generalizaciones falsas sin base concreta (memoria contaminada).
3. **Costo de contexto**: las reflexiones ocupan tokens; el buffer puede saturarse.
4. **Multi-agent Reflexion** (investigación 2024-2025): separa al agente evaluador del agente actor para mitigar el confirmation bias.

## Relación con el wiki

Esta fuente enriquece [[concepts/planning]] (donde Reflexion está documentado a nivel conceptual) con los datos empíricos del paper original.

## Conexiones

- [[concepts/planning]] — Reflexion como técnica de planning; ver sección detallada
- [[concepts/llm-agents]] — Reflexion como componente de Memory + Planning integrados
- [[concepts/agentic-design-patterns]] — Reflection (Ng) tiene a Reflexion (Shinn) como implementación de referencia
- [[lilian-weng-agents]] — fuente que resume Reflexion entre otras técnicas
