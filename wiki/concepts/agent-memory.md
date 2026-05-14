---
title: "Memoria en Agentes LLM"
type: concept
tags: [memoria, agentes, vector-db, ann, rag, corto-plazo, largo-plazo]
sources: [2026-05-13_lilian-weng-agents.md]
updated: 2026-05-13
---

# Memoria en Agentes LLM

## El problema que resuelve

Los LLMs tienen un contexto finito: solo pueden "recordar" lo que está en su ventana de contexto activa. Para tareas largas, sesiones múltiples, o conocimiento de dominio extenso, esto es insuficiente. La memoria extiende esta capacidad.

---

## Taxonomía: la analogía con la memoria humana

Lilian Weng propone mapear la memoria humana a los mecanismos disponibles en agentes:

| Tipo humano | Duración | Capacidad | Equivalente en agente |
|-------------|---------|-----------|----------------------|
| **Sensorial** | Segundos | Baja | Embeddings de inputs crudos (imágenes, audio) |
| **Corto plazo (STM / Working)** | ~20-30 seg, ~7 items | ~7 chunks | Ventana de contexto del Transformer |
| **Largo plazo — declarativa** | Días a décadas | Ilimitada | Vector DB + retrieval (ANN) |
| **Largo plazo — semántica** | Ilimitada | Ilimitada | Documentos embebidos en vector DB |
| **Largo plazo — episódica** | Ilimitada | Ilimitada | Logs de conversaciones, historial de acciones |
| **Largo plazo — procedural** | Ilimitada | Ilimitada | Pesos del modelo (fine-tuning, LoRA) |

---

## Memoria de Corto Plazo (in-context)

**Qué es**: Todo lo que está dentro de la ventana de contexto del Transformer. Incluye el prompt del sistema, el historial de la conversación, los resultados de herramientas, y cualquier información pasada explícitamente.

**Ventajas**: acceso inmediato, full attention, el LLM "ve" todo simultáneamente.

**Limitaciones**: tamaño finito (aunque modelos modernos como [[entities/gemini]] tienen ventanas de millones de tokens), no persiste entre sesiones sin gestión explícita.

**En LangGraph**: el `State` del grafo es esencialmente la memoria de corto plazo. El campo `messages` con reducer `add_messages` es el patrón estándar.

---

## Memoria de Largo Plazo (externa)

**Qué es**: Base de datos vectorial que almacena información embebida para recuperación posterior. El agente la consulta cuando necesita información que no está en contexto.

**Implementación**: es exactamente el pipeline [[rag]]:
1. Documentos/experiencias → chunking → embeddings → almacenamiento en vector DB
2. En runtime: query → embedding → búsqueda ANN → recuperar chunks relevantes → insertar en contexto

**Casos de uso**:
- Conocimiento de dominio extenso (papers, documentos, manuales)
- Historial de conversaciones pasadas
- Resultados de acciones previas (Memory Stream de Generative Agents)
- Reflexiones y aprendizajes de intentos fallidos (Reflexion)

---

## Algoritmos ANN para recuperación eficiente

El cuello de botella de la memoria a largo plazo es encontrar el chunk más relevante entre millones. Los algoritmos **Approximate Nearest Neighbors (ANN)** resuelven el problema de **MIPS** (Maximum Inner Product Search):

| Algoritmo | Mecanismo | Característica |
|-----------|-----------|---------------|
| **LSH** | Hash functions que mapean vectores similares al mismo bucket | Simple, baja precisión |
| **ANNOY** | Árboles de proyección aleatoria; múltiples árboles para mejor recall | Rápido en construcción |
| **HNSW** | Grafo de pequeño mundo jerárquico; capas crean atajos | Balance velocidad/precisión; estándar en producción |
| **FAISS** | Cuantización vectorial con clusters (distribución gaussiana) | Alta escala; Facebook/Meta |
| **ScaNN** | Cuantización vectorial anisotrópica; optimiza producto interno | Alta precisión; Google |

**HNSW** es el algoritmo más usado en vector databases de producción (Pinecone, Qdrant, pgvector, Chroma lo implementan internamente).

---

## Ejemplo real: Generative Agents (Park et al. 2023)

El caso más elaborado de memoria en agentes: 25 agentes simulados con un sistema de memoria completo.

**Memory Stream**: base de datos que registra todas las experiencias del agente en lenguaje natural (observaciones, conversaciones, reflexiones).

**Retrieval**: cuando el agente necesita decidir qué hacer, recupera memorias basándose en tres señales:
1. **Recency** (decaimiento exponencial desde el último acceso)
2. **Importance** (el propio LLM puntúa qué tan importante es la memoria: 1-10)
3. **Relevance** (similitud semántica con la situación actual)

**Score combinado**: `score = w_recency * recency + w_importance * importance + w_relevance * relevance`

**Reflection**: periódicamente, el agente sintetiza sus últimas 100 memorias en observaciones de nivel superior ("¿Cuáles son las 3 preguntas más importantes que podría responder dado lo que observé?"), que se almacenan de nuevo en la Memory Stream.

---

## Tensión: STM vs LTM

| Dimensión | STM (contexto) | LTM (vector DB) |
|-----------|---------------|-----------------|
| Acceso | Full attention (el LLM ve todo a la vez) | Retrieval aproximado (puede perder información relevante) |
| Capacidad | Finita (aunque creciente en modelos modernos) | Prácticamente ilimitada |
| Persistencia | Solo durante la sesión | Entre sesiones |
| Costo | Tokens en el prompt | Almacenamiento + cómputo de embeddings |

> Los vectorstores extienden la memoria pero su poder representacional es inferior a la atención completa. — Lilian Weng

**Estrategia práctica**: usar LTM para recuperar el contexto relevante y meterlo en la STM del agente. El chunking de calidad es crítico: ver [[concepts/chunking]].

---

## Conexiones

- [[concepts/llm-agents]] — Memory como uno de los tres componentes del agente
- [[concepts/rag]] — LTM = pipeline RAG
- [[concepts/chunking]] — cómo dividir documentos para la LTM
- [[concepts/langgraph]] — State como STM; checkpointing como LTM entre sesiones
- [[crewai]] — 4 tipos de memoria nativos
- [[lilian-weng-agents]] — fuente primaria
