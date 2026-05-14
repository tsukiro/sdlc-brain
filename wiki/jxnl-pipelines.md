---
title: "Jason Liu — Good LLM Pipelines are just Good Software Engineering"
type: source-summary
tags: [llm-pipelines, software-engineering, instructor, jason-liu, producción]
sources: []
updated: 2026-05-13
---

# Jason Liu — Good LLM Pipelines are just Good Software Engineering

**Fuente:** https://jxnl.co/writing/2023/05/23/good-llm-pipelines-are-just-good-software-engineering/  
**Nota:** URL devuelve 404 (mayo 2026). Contenido recuperado de blog principal en https://jxnl.co/writing/ y del ecosistema de Instructor.  
**Autor:** Jason Liu — creador de [[entities/instructor]]

## Perspectiva de esta fuente

Artículo fundacional de Jason Liu que justifica filosóficamente el enfoque de Instructor. La tesis: los pipelines de LLM **no son magia** — las mismas prácticas que hacen robusto el software tradicional (tipado, contratos, validación, reintentos, observabilidad) son exactamente lo que hace robustos los pipelines de IA.

---

## Tesis central

> Los pipelines de LLM no requieren nuevas disciplinas de ingeniería. Requieren aplicar la ingeniería de software existente con rigor.

---

## Principios clave

### 1. Especificación antes que prompt

Define **qué datos esperas** (Pydantic/TypeScript) antes de escribir el prompt. El schema es la especificación. El prompt es la implementación. Si la especificación está mal, ningún prompt la arregla.

### 2. Tipos como documentación ejecutable

Una clase `BaseModel` describe el contrato del output del LLM de forma que el **runtime puede verificar**. No es un comentario — es código que falla si se viola.

### 3. Reintentos con feedback estructurado

Cuando el LLM falla la validación, el error del validador se pasa de vuelta al modelo como contexto. Es un loop de corrección, no un crash. El reintento no es fuerza bruta — es debugging guiado.

### 4. Observabilidad obligatoria

Los pipelines de LLM en producción requieren las mismas métricas que cualquier sistema distribuido:
- Latencia por llamada
- Tasa de error de validación
- Tokens consumidos
- Tasa de reintentos (signal de calidad del prompt)

### 5. Simple tools win

Complejidad arquitectural (multi-agent, RAG con embeddings densos) frecuentemente underperforma vs. un sistema simple bien diseñado. Ver "telephone game effect" en multi-agent.

---

## Insights adicionales del blog (jxnl.co/writing/)

Publicados post-2023, revelan cómo evoluciona el pensamiento de Liu:

| Artículo | Insight |
|---|---|
| **"No Multi-Agents"** | Multi-agent underperforma por "telephone game" — cada hop agrega ruido. Un agente único con buen contexto > pipeline complejo |
| **"Grep Beats Embeddings"** | Para búsqueda en código/texto estructurado, grep supera embeddings vectoriales en muchos escenarios reales |
| **"Only 6 Evals"** | Los sistemas de evaluación no requieren complejidad. 6 métricas bien elegidas son suficientes para medir calidad en producción |
| **"Systematically Improving RAG"** | RAG requiere metodología sistemática, no trial-and-error de chunking y retrieval |

---

## Tensión con el consenso del campo

| Consenso | Liu |
|---|---|
| Multi-agent es el futuro | Multi-agent introduce ruido; priorizar single-agent con buen contexto |
| RAG + embeddings siempre mejor | Grep/búsqueda exacta frecuentemente supera embeddings |
| Evaluar con LLM judge | Solo 6 evals bien elegidos; el LLM judge puede ser costoso e impreciso |

Estas tensiones son empíricas, no dogmáticas — Liu las sustenta con evidencia de sistemas en producción.

---

## Conexiones

- [[concepts/structured-generation]] — implementación técnica de la tesis
- [[entities/instructor]] — la herramienta que encarna estos principios
- [[instructor]] — documentación de la librería
- [[concepts/agent-evaluation]] — "Only 6 Evals" conecta con evaluación de agentes
- [[concepts/multi-agent-frameworks]] — tensión: "No Multi-Agents" vs. LangGraph/CrewAI
- [[langgraph]] — contexto donde aplicar el principio "simple tools win"
