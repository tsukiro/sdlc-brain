---
title: "Agentic Design Patterns (Andrew Ng)"
type: concept
tags: [agentes, design-patterns, reflection, tool-use, planning, multi-agent, andrew-ng]
sources: [2026-05-13_andrew-ng-agentic-patterns.md, 2026-05-13_lilian-weng-agents.md]
updated: 2026-05-13
---

# Agentic Design Patterns

## Qué son

Los patrones de diseño agéntico son las estrategias arquitecturales que transforman un LLM pasivo en un sistema que itera, razona y actúa. Propuestos por Andrew Ng como marco unificador, responden a por qué los agentes superan a los LLMs en modo zero-shot.

## El resultado que lo motiva todo

```
GPT-3.5 zero-shot:               48.1%  HumanEval
GPT-4 zero-shot:                 67.0%  HumanEval
GPT-3.5 + flujo agente iterativo: 95.1%  HumanEval
```

Un modelo inferior con la arquitectura correcta supera al modelo superior sin ella. **La arquitectura del workflow es un multiplicador de capacidad.**

---

## Patrón 1: Reflection (Autorreflexión)

### Qué es
El LLM examina su propio output antes de entregarlo, buscando errores, inconsistencias y oportunidades de mejora. Es el patrón más simple — no requiere herramientas ni otros agentes.

### Cómo funciona
```
Input → LLM genera draft
      → LLM evalúa draft ("¿Qué está mal? ¿Qué mejorarías?")
      → LLM genera output mejorado
      → (repeat N veces)
      → Output final
```

### Variantes documentadas (Weng)
- **Reflexion** (Shinn & Labash): detecta trayectorias fallidas mediante heurísticas y genera reflexiones que mejoran el próximo intento
- **Chain of Hindsight**: mejora condicionada en historial de intentos con feedback anotado

### Cuándo usar
- Tareas de escritura, coding, razonamiento donde la calidad importa más que la latencia
- Siempre que el LLM pueda evaluar su propio output sin información externa

---

## Patrón 2: Tool Use (Uso de Herramientas)

### Qué es
El LLM tiene acceso a herramientas externas — búsqueda web, ejecución de código, APIs, bases de datos — para obtener información o ejecutar acciones más allá de su capacidad de generación pura.

### Por qué es necesario
Los LLMs tienen conocimiento estático (hasta el corte de entrenamiento), cometen errores aritméticos, y no pueden actuar en el mundo. Las herramientas resuelven cada uno de estos problemas.

### Herramientas comunes
- **Búsqueda web**: superar el corte de conocimiento
- **Ejecución de código**: cálculos precisos, manipulación de datos
- **Llamadas a APIs**: servicios externos (email, calendarios, bases de datos)
- **Retrieval (RAG)**: acceso a documentos propios → ver [[rag]]

### Implementación
Function Calling (OpenAI, Anthropic, Google) es el mecanismo estándar moderno: el LLM genera una llamada de función estructurada, el runtime la ejecuta y devuelve el resultado.

---

## Patrón 3: Planning (Planificación)

### Qué es
El LLM descompone una tarea compleja en un plan multietapa, ejecuta los pasos, y ajusta el plan basándose en resultados intermedios. Reflection y Tool Use son subcomponentes del Planning efectivo.

### Técnicas de planificación (de más simple a más sofisticada)

| Técnica | Cuándo |
|---------|--------|
| **Chain of Thought** | Razonamiento paso a paso en un solo pase |
| **ReAct** | Bucle Thought → Action → Observation; ideal cuando hay herramientas |
| **Tree of Thoughts** | Explorar múltiples ramas de razonamiento; para problemas con espacio de solución amplio |
| **LLM+P** | Planificación formal delegada a motor PDDL; para problemas de planificación bien definidos |

Ver [[planning]] para detalle completo.

### El challenge central
Los LLMs fallan más en planning que en cualquier otro componente: les cuesta ajustar planes ante errores inesperados y explorar el espacio de solución de forma robusta. Es el desafío #2 de Weng.

---

## Patrón 4: Multi-Agent Collaboration

### Qué es
Múltiples agentes especializados colaboran: dividen tareas según roles, debaten soluciones, se verifican mutuamente. Escala horizontalmente lo que un solo agente haría secuencialmente.

### Por qué es más poderoso que un solo agente con Reflection
- **Especialización**: cada agente puede ser optimizado para su rol (investigador, redactor, crítico, coder)
- **Paralelismo**: tareas independientes corren simultáneamente
- **Verificación cruzada**: un agente detecta errores del otro mejor que un agente evaluándose a sí mismo

### Topologías comunes

```
Supervisión jerárquica:
  Supervisor ──► Agente A
             └──► Agente B
             └──► Agente C

Colaboración entre pares:
  Agente 1 ◄──► Agente 2 ◄──► Agente 3

Pipeline secuencial:
  Agente 1 ──► Agente 2 ──► Agente 3 ──► Output
```

### Implementaciones
- [[concepts/langgraph]]: subgraphs para cada agente, supervisor como nodo de routing
- [[crewai]]: crews con roles definidos y manager agent opcional

---

## Relación entre los cuatro patrones

Los patrones no son independientes — se componen:

```
Multi-Agent (escala horizontal)
    └── cada agente puede usar:
            Planning (secuencia de pasos)
                └── Reflection (evaluar cada paso)
                └── Tool Use (extender capacidades)
```

En la práctica, un sistema robusto de producción combina los cuatro.

---

## Implicaciones para el diseño de sistemas

1. **Empieza simple**: Reflection es barato de implementar y a menudo da el mayor ROI inicial
2. **Itera**: añade Tool Use cuando el LLM necesita información externa; Planning cuando la tarea es multi-step; Multi-Agent cuando la especialización importa
3. **No asumas que más modelo = mejor**: un sistema con Reflection + Tool Use sobre un modelo menor puede superar a un modelo mayor sin ellos (ver HumanEval)
4. **El bottleneck suele ser Planning**: es el patrón más difícil de hacer funcionar de forma robusta

---

## Conexiones

- [[concepts/llm-agents]] — arquitectura base (3 componentes de Weng)
- [[planning]] — técnicas de planning detalladas
- [[agent-memory]] — memoria como sustento de Reflection y Planning
- [[concepts/multi-agent-frameworks]] — LangGraph vs CrewAI implementando Multi-Agent
- [[andrew-ng-agentic-patterns]] — fuente primaria
- [[lilian-weng-agents]] — fuente complementaria
