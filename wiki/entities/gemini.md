---
title: "Gemini (Google DeepMind)"
type: entity
tags: [gemini, google, llm, multimodal, api, embeddings, computer-use]
sources: [2026-05-13_gemini.md]
updated: 2026-05-13
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
- [[concepts/rag]] — gemini-embedding-2-preview para RAG multimodal
- [[concepts/multi-agent-frameworks]] — Gemini como LLM en LangGraph / CrewAI
- [[concepts/langgraph]] — integración como LLM del grafo
- [[crewai]] — integración como LLM de agentes
