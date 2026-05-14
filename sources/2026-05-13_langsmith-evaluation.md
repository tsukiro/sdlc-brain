# LangSmith — Evaluating Agents

**URL:** https://docs.smith.langchain.com/evaluation/evaluating-agents → docs.langchain.com/langsmith  
**Fecha de captura:** 2026-05-13

---

## Qué es LangSmith

Plataforma de observabilidad, trazado y evaluación de LangChain para aplicaciones de LLM y agentes en producción.

## Estrategia de evaluación recomendada

1. **Identificar qué importa**: descomponer el sistema en componentes críticos (llamadas LLM, retrieval, tool invocations, formateo de output)
2. **Curar ejemplos manualmente**: 10-20 ejemplos de alta calidad que cubran escenarios comunes y edge cases — definen qué es "bueno"
3. **Evaluar componentes individualmente**: no solo el output final

## Evaluación de pasos intermedios

El evaluador recibe el objeto `Run` con todos los pasos intermedios del pipeline.

**Para RAG**:
- Evaluar retrieval: ¿se recuperaron los documentos correctos para el query?
- Evaluar generación: ¿se generó la respuesta correcta con los documentos recuperados?

## Trajectory Evaluation (AgentEvals)

Evalúa la trayectoria del agente: la secuencia exacta de mensajes y tool calls.

**Modos**:
- **Trajectory match**: compara contra trayectoria de referencia hardcodeada. Determinista, rápido, no requiere LLM adicional. Ideal para workflows bien definidos.
- **LLM judge**: un LLM evalúa si la trayectoria fue apropiada. Más flexible, más costoso.

## Observabilidad en producción

- Trazado de cada paso del agente
- Identificación de pasos que fallan frecuentemente
- Comparación de trayectorias entre versiones
- Evaluación de distribuciones de comportamiento

## Métricas relevantes para agentes

- Tasa de éxito en la tarea final
- Calidad de los pasos intermedios
- Número de steps necesarios (eficiencia)
- Tasa de hallucination en razonamiento intermedio
- Tool call accuracy (¿llamó a la herramienta correcta?)
