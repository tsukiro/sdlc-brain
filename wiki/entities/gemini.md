---
title: "Gemini (Google DeepMind)"
type: entity
tags: [gemini, google, llm, multimodal, api, embeddings, computer-use, context-caching]
sources: [2026-05-13_gemini.md, promptingguide-techniques.md]
updated: 2026-05-14
---

# Gemini (Google DeepMind)

## Qué es

Familia de modelos de IA frontier de Google DeepMind. Multimodal de base: acepta texto, audio, imágenes, código y video. Disponible como producto de consumo (gemini.google.com) y como API para desarrolladores (ai.google.dev).

## Familia de modelos (estado mayo 2026)

### Gemini 3 Pro
- **Más capaz** de la familia
- LMArena Leaderboard: **1501 Elo** (top mundial)
- Supera anteriores en reasoning, multimodalidad y coding
- Soporta **Computer Use tool** (gemini-3-pro-preview)
- Orientado a: tareas de máxima complejidad, análisis profundo

### Gemini 3 Flash
- **Mejor balance velocidad / calidad / costo**
- Reasoning Pro-grade con latencia Flash
- **3x más rápido** que Gemini 2.5 Pro
- **Precio**: $0.50/1M tokens input, $3/1M tokens output
- Soporta Computer Use (gemini-3-flash-preview)
- Ideal para: análisis de video, extracción de datos, Visual Q&A, agentic workflows en producción

### Gemini 2.x (generación anterior)
- Gemini 2.0 Flash, Flash-Lite, Pro Experimental
- Aún disponibles; introducidos como "AI para la era agéntica" (dic 2024)

## Capacidades relevantes para desarrollo de software

| Capacidad | Relevancia |
|-----------|-----------|
| Multimodalidad (texto/imagen/video/audio/code) | Análisis de screenshots, diagramas, video demos |
| Context windows largas | Procesar codebases o documentos extensos sin chunking agresivo |
| Computer Use tool | Automatización de interfaces gráficas |
| Tool use / Function calling | Integración con APIs y herramientas externas |
| Gemini API | Integrable en LangGraph, CrewAI, y cualquier stack Python/JS |

## Modelo de embeddings

**`gemini-embedding-2-preview`**: Embedding multimodal que acepta texto, imagen, video, audio y PDF en un solo modelo. Elimina la necesidad de modelos de embedding especializados por tipo de contenido. Relevante para [[rag]] multimodal.

## Context Caching

Disponible vía Gemini API (1.5 Flash, 1.5 Pro, y modelos posteriores). Permite cachear un contexto grande en el servidor de Gemini — el contenido (texto, documentos) se procesa una vez y las queries reutilizan el KV cache del servidor sin re-enviarlo.

**API:**
```python
import datetime
from google.generativeai import caching

cache = caching.CachedContent.create(
    model="gemini-1.5-flash-001",
    name="ml-papers-cache",
    system_instruction="Eres un experto investigador de IA...",
    contents=[text_file],
    ttl=datetime.timedelta(minutes=15)   # time-to-live configurable
)
model = genai.GenerativeModel.from_cached_content(cache)
```

**Caso de uso demostrado (DAIR-AI):** Análisis de un año de papers ML (resúmenes en texto plano). El archivo se sube una vez, se cachea, y se consulta con lenguaje natural sin re-enviar el contenido en cada request.

**Cuándo usar context caching vs. alternativas:**

| Situación | Técnica recomendada |
|-----------|-------------------|
| Corpus grande, estático, mismo modelo, misma sesión | Context Caching |
| Corpus grande, persistente entre sesiones, síntesis requerida | LLM Wiki ([[concepts/llm-wiki]]) |
| Corpus grande, dinámico, solo partes relevantes por query | RAG ([[concepts/rag]]) |
| Corpus pequeño, una sola query | Carga directa en contexto |

**Relación con LLM Wiki:** Ambos resuelven "contexto estático + muchas queries" con una sola carga. La diferencia clave:
- Context Caching: server-side KV cache, time-limited (TTL), contenido sin transformar, dentro de una sesión
- LLM Wiki: síntesis compilada en markdown, persistente entre sesiones, cross-referenced, portable

**Relación con RAG:** Para corpus que caben en el context window y son estáticos, Context Caching elimina el pipeline de chunking + embeddings + retrieval — más simple y sin pérdida de información por retrieval imperfecto.

## Distinción: producto vs. API

| | gemini.google.com | ai.google.dev |
|--|--|--|
| Tipo | Chatbot de consumo | API para desarrolladores |
| Audiencia | Usuarios finales | Developers / empresas |
| Acceso | Gratuito / Gemini Advanced | API keys, pago por uso |
| Integración | No programática | SDK Python/JS, REST |

## Integración con frameworks de agentes

- **LangChain / LangGraph**: `ChatGoogleGenerativeAI` en langchain-google-genai
- **CrewAI**: configurable como LLM de cualquier Agent
- **Directo via API**: google-generativeai SDK

## Posición competitiva (mayo 2026)

Gemini 3 Pro lidera el LMArena Leaderboard con 1501 Elo, superando modelos anteriores de OpenAI y Anthropic en benchmarks públicos. Gemini 3 Flash se posiciona como la opción más costo-eficiente para agentic workflows de producción.

## Conexiones

- [[gemini]] — fuente primaria (source summary)
- [[concepts/rag]] — gemini-embedding-2-preview para RAG multimodal; Context Caching como alternativa a RAG para corpus estáticos
- [[concepts/llm-wiki]] — Context Caching y LLM Wiki resuelven el mismo problema desde ángulos distintos (KV cache vs. síntesis compilada)
- [[concepts/multi-agent-frameworks]] — Gemini como LLM en LangGraph / CrewAI
- [[concepts/langgraph]] — integración como LLM del grafo
- [[crewai]] — integración como LLM de agentes
