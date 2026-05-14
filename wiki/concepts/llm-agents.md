---
title: "Agentes LLM — Arquitectura y Componentes"
type: concept
tags: [agentes, llm, planning, memoria, herramientas, arquitectura]
sources: [2026-05-13_lilian-weng-agents.md, 2026-05-13_andrew-ng-agentic-patterns.md]
updated: 2026-05-13
---

# Agentes LLM — Arquitectura y Componentes

## Qué es un agente LLM

Un sistema donde un LLM actúa como **cerebro central** (controlador) que percibe el entorno, razona, planifica y ejecuta acciones — potencialmente en bucles iterativos — para lograr objetivos complejos que un LLM en modo zero-shot no podría alcanzar.

**Distinción clave**: un LLM convencional recibe un prompt y genera una respuesta. Un agente LLM puede: usar herramientas, mantener memoria, planificar múltiples pasos, iterar sobre su propio output, y coordinar con otros agentes.

## Por qué los agentes superan a los LLMs solos

El hallazgo empírico más impactante del campo (Andrew Ng, HumanEval):

| Configuración | Score |
|--------------|-------|
| GPT-3.5 zero-shot | 48.1% |
| GPT-4 zero-shot | 67.0% |
| **GPT-3.5 con flujo agente iterativo** | **95.1%** |

> **Implicación**: la arquitectura del workflow puede importar más que el modelo. Comparar modelos en benchmarks zero-shot es insuficiente para predecir su rendimiento en uso agéntico.

---

## La arquitectura: tres componentes + cuatro patrones

Dos marcos complementarios:

**Weng (2023)** — tres componentes estructurales:
```
AGENTE = LLM + Planning + Memory + Tool Use
```

**Ng (2024)** — cuatro design patterns:
```
Agentic Patterns = Reflection + Tool Use + Planning + Multi-Agent
```

La diferencia: Weng describe la arquitectura interna del agente; Ng describe los patrones de diseño que hacen que el agente sea efectivo. **Reflection** en Ng es la autoevaluación iterativa que Weng subsume dentro de Planning (Reflexion).

---

## Componente 1: Planning

El agente descompone tareas complejas en pasos manejables y razona sobre cómo ejecutarlos. Incluye la capacidad de detectar errores y ajustar el plan.

**Técnicas principales:**

| Técnica | Mecanismo | Origen |
|---------|-----------|--------|
| **Chain of Thought (CoT)** | "Think step by step"; cadena lineal de razonamiento | Wei et al. 2022 |
| **Tree of Thoughts (ToT)** | Árbol de posibilidades; BFS/DFS sobre razonamientos | Yao et al. 2023 |
| **ReAct** | Ciclo: Thought → Action → Observation (loop) | Yao et al. 2023 |
| **Reflexion** | Detecta fallas → genera reflexión → mejora en siguiente intento | Shinn & Labash 2023 |
| **LLM+P** | Delega la planificación formal a motor PDDL externo | Liu et al. 2023 |

Ver [[planning]] para detalle completo de cada técnica.

---

## Componente 2: Memory

La memoria del agente resuelve la limitación del contexto finito del Transformer. Mapeada a la memoria humana:

| Tipo humano | Equivalente en agente | Implementación |
|-------------|----------------------|----------------|
| Sensorial | Inputs crudos del entorno | Embeddings de imágenes, audio, texto |
| Corto plazo (STM) | In-context window | Ventana de contexto del Transformer (~7 items → miles de tokens) |
| Largo plazo — declarativa | Hechos y eventos recuperables | Vector DB + ANN (HNSW, FAISS, ANNOY, ScaNN) |
| Largo plazo — procedural | Habilidades automatizadas | Pesos del modelo (fine-tuning) |

**La memoria a largo plazo de un agente es esencialmente un sistema [[rag]]**: los documentos se embeden, se almacenan en vector DB, y se recuperan por similitud cuando el agente los necesita.

Ver [[agent-memory]] para detalle de algoritmos ANN y trade-offs.

---

## Componente 3: Tool Use

El agente puede invocar herramientas externas para superar las limitaciones del LLM puro:

**Por qué se necesitan herramientas:**
- LLMs no tienen información post-entrenamiento → búsqueda web
- LLMs cometen errores aritméticos → calculadoras / código
- LLMs no pueden actuar en el mundo → APIs, robótica

**Arquitecturas de tool use:**

| Arquitectura | Mecanismo |
|-------------|-----------|
| **MRKL** | LLM como router a módulos expertos (simbólicos o neuronales) |
| **Function Calling** | LLM genera llamadas a funciones predefinidas; el runtime las ejecuta |
| **HuggingGPT** | LLM planifica + delega a modelos especializados de HuggingFace |
| **Toolformer** | Fine-tuning que enseña cuándo y cómo usar APIs |

---

## Patrón 4: Multi-Agent (Ng)

Múltiples agentes especializados colaboran: dividen tareas, debaten soluciones, se revisan mutuamente. Cada agente puede tener sus propios tres componentes (Planning + Memory + Tools).

**Por qué es poderoso**: permite especialización, paralelismo y verificación cruzada. Un agente escritor + un agente crítico produce mejor output que un solo agente con Reflection.

Implementaciones: [[concepts/langgraph]] (grafos multi-agente), [[crewai]] (crews con roles).

---

## Casos de uso reales documentados

| Sistema | Capacidad demostrada |
|---------|---------------------|
| **ChemCrow** | 13 herramientas para síntesis química; supera a GPT-4 en evaluación experta |
| **Generative Agents** | 25 agentes en sandbox; comportamientos sociales emergentes (Park et al. 2023) |
| **AutoGPT** | Agencia autónoma con 20 comandos; prototipo conceptual |
| **GPT-Engineer** | Genera repositorios completos desde especificaciones en lenguaje natural |
| **Boiko et al.** | Diseño y ejecución de experimentos científicos; acceso a robótica e internet |

---

## Desafíos vigentes

1. **Contexto finito**: el vector store extiende la memoria pero no iguala la atención completa sobre todo el historial
2. **Planificación largo plazo**: los LLMs fallan al ajustar planes ante errores inesperados; menos robustos que humanos en prueba y error
3. **Confiabilidad de interfaz natural**: errores de formato en outputs, comportamiento ocasionalmente "rebelde"; parseo robusto requiere esfuerzo
4. **Autoevaluación sesgada**: los LLMs son malos evaluando dominios especializados que ellos mismos no dominan — el agente puede no detectar sus propios errores en esos dominios

---

## Cómo los frameworks implementan esta arquitectura

| Framework | Planning | Memory | Tools | Multi-Agent |
|-----------|----------|--------|-------|-------------|
| **LangGraph** | Conditional edges + Command | Checkpointing + vector store externo | Function calling | Subgraphs + supervisor |
| **CrewAI** | Manager agent + planificación | 4 tipos nativos | Cientos de herramientas OSS | Nativo (crews) |

---

## Conexiones

- [[planning]] — técnicas de planning: CoT, ToT, ReAct, Reflexion
- [[agent-memory]] — tipos de memoria y ANN
- [[agentic-design-patterns]] — los 4 patrones de Andrew Ng
- [[concepts/langgraph]] — implementación en grafos
- [[crewai]] — implementación en crews
- [[concepts/rag]] — LTM del agente = pipeline RAG
- [[lilian-weng-agents]] — fuente primaria (Weng)
- [[andrew-ng-agentic-patterns]] — fuente primaria (Ng)
