# Andrew Ng — How Agents Can Improve LLM Performance

**URL:** https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance/  
**Fecha de captura:** 2026-05-13  
**Autor:** Andrew Ng (DeepLearning.AI / The Batch)

---

## Los cuatro Agentic Design Patterns

1. **Reflection**: el LLM examina su propio trabajo para encontrar formas de mejorarlo
2. **Tool Use**: acceso a búsqueda web, ejecución de código u otras funciones para recopilar información y procesar datos
3. **Planning**: el LLM elabora y ejecuta un plan multietapa para lograr un objetivo
4. **Multi-agent collaboration**: múltiples agentes trabajan juntos, dividiendo tareas y debatiendo soluciones superiores

## Impacto cuantificable: benchmark HumanEval

| Configuración | Score HumanEval |
|--------------|----------------|
| GPT-3.5 zero-shot | 48.1% |
| GPT-4 zero-shot | 67.0% |
| GPT-3.5 con flujo agente iterativo | **95.1%** |

**Insight central**: GPT-3.5 con arquitectura agente supera a GPT-4 zero-shot. La arquitectura del workflow importa tanto o más que el modelo subyacente.

## Analogía conceptual de Ng

Enfoque tradicional (no-agente): como escribir un ensayo sin poder retroceder, directamente del primer al último carácter.

Flujo agente: ciclos iterativos de revisión → resultados significativamente mejores, como un escritor que borra, edita y refina.

## Relación entre los cuatro patrones

- **Reflection** → el agente evalúa su propio output y lo mejora (base de los demás)
- **Tool Use** → amplía las capacidades con acceso a información y cómputo externo
- **Planning** → decompone tareas complejas en pasos; Reflection + Tool Use son subcomponentes del planning
- **Multi-agent** → escala horizontalmente; cada agente puede tener sus propios patrones de Reflection, Tool Use y Planning
