---
title: "Evaluación de Agentes LLM en Producción"
type: concept
tags: [evaluación, agentes, benchmark, trajectory, langsmith, balrog, producción]
sources: [2026-05-13_balrog.md, 2026-05-13_langsmith-evaluation.md, 2026-05-13_andrew-ng-agentic-patterns.md]
updated: 2026-05-13
---

# Evaluación de Agentes LLM en Producción

## El problema central

Evaluar agentes es fundamentalmente diferente a evaluar LLMs estáticos. La pregunta no es "¿generó la respuesta correcta?" sino "¿tomó las decisiones correctas a lo largo de múltiples pasos?".

> "Comparar modelos en benchmarks zero-shot es insuficiente para predecir su rendimiento en uso agéntico." — Andrew Ng (HumanEval: GPT-3.5 agente 95.1% > GPT-4 zero-shot 67%)

Y el caso opuesto también es cierto: benchmarks de una sola pregunta sobreestiman la capacidad agéntica en horizontes largos.

> "Los modelos logran éxito parcial en juegos fáciles pero fallan significativamente con tareas de largo plazo." — BALROG (mejor modelo: 34.9% en NetHack)

---

## Las tres dimensiones de evaluación

### 1. Output final
¿Completó la tarea correctamente? La métrica más simple pero más engañosa en solitario. Un agente puede llegar al resultado correcto por caminos frágiles que fallarán en casos ligeramente diferentes.

### 2. Pasos intermedios (Trajectory Evaluation)
¿Las decisiones individuales fueron correctas? Esto incluye:
- ¿Llamó a las herramientas correctas con los parámetros correctos?
- ¿El razonamiento intermedio fue coherente?
- ¿Cuántos steps tardó vs. el óptimo?
- ¿Hubo alucinaciones en los pasos intermedios?

**LangSmith — trajectory match**: compara la secuencia de tool calls contra una trayectoria de referencia. Determinista, sin LLM adicional, ideal para workflows bien definidos.

**LangSmith — LLM judge**: un LLM evalúa si la trayectoria fue apropiada. Más flexible, más costoso.

### 3. Comportamiento en horizontes largos (BALROG)
¿El agente mantiene coherencia y progresa en episodios de cientos de pasos? Esta dimensión no se puede evaluar con ejemplos de una sola pregunta.

BALROG mide "progreso promedio" en episodios de juego, no éxito binario. Un agente que llega al 34% del camino en NetHack es cuantitativamente mejor que uno que llega al 20%.

---

## Framework práctico: cómo evaluar un sistema agéntico

### Paso 1: Curar ejemplos base (10-20)
Ejemplos de alta calidad que cubren: escenarios comunes, edge cases críticos, casos de fallo conocidos. Definen qué es "bueno" para el sistema específico.

### Paso 2: Descomponer por componentes
Evaluar cada componente individualmente:

| Componente | Qué evaluar |
|-----------|-------------|
| Retrieval (RAG) | Recall@k, precisión de documentos recuperados |
| Tool selection | ¿Eligió la herramienta correcta? |
| Tool args | ¿Los parámetros son correctos? |
| Reasoning | ¿El chain-of-thought lleva al resultado correcto? |
| Output final | ¿Es correcto? ¿Está grounded en el contexto? |

### Paso 3: Trajectory evaluation
Para workflows bien definidos: trajectory match contra trayectoria de referencia.
Para workflows abiertos: LLM judge evaluando coherencia y corrección de cada paso.

### Paso 4: Evaluación en horizonte largo
Si el sistema opera en episodios largos (automatización de flujos complejos, Computer Use, pipelines multi-etapa): evaluar con escenarios de múltiples pasos que representen el uso real.

### Paso 5: Monitoring continuo en producción
- Tracear todos los pasos con LangSmith o equivalente
- Monitorear distribución de número de steps por tarea (aumento = regresión)
- Detectar tool calls fallidas recurrentes
- Medir tasa de HITL (interrupciones humanas) como proxy de confianza del sistema

---

## Hallazgo notable: VLMs vs. LLMs en BALROG

Los modelos de visión (VLM) rinden **peor** que los LLMs de texto en muchas tareas cuando se les proveen representaciones visuales. Esto es contraintuitivo: la información visual adicional degrada el desempeño.

**Hipótesis**: los modelos no han aprendido a ignorar información visual ruidosa o irrelevante para la toma de decisiones secuencial. La comprensión visual estática (describir una imagen) no transfiere directamente a la comprensión visual dinámica (decidir qué clic dar).

**Implicación práctica**: en sistemas de Computer Use y agentes visuales, no asumir que más información visual = mejor desempeño.

---

## Métricas de referencia

| Métrica | Herramienta | Para qué |
|---------|-------------|---------|
| Pass@1 | HumanEval | Coding en un intento |
| Progreso promedio | BALROG | Planificación larga |
| Trajectory match score | LangSmith AgentEvals | Reproducibilidad de workflow |
| Tool accuracy | LangSmith | Uso correcto de herramientas |
| Recall@k | RAG evaluators | Calidad de retrieval |
| MRR, NDCG | RAG evaluators | Ranking de retrieval |

---

## Conexiones

- [[balrog]] — benchmark académico para evaluación de horizonte largo
- [[langsmith-evaluation]] — plataforma práctica de evaluación en producción
- [[concepts/llm-agents]] — arquitectura que se está evaluando
- [[concepts/planning]] — planning a largo plazo es lo que BALROG mide que falla
- [[concepts/rag]] — evaluación de RAG como caso especial
- [[concepts/langgraph]] — LangSmith es nativo para LangGraph
