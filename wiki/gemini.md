---
title: "Gemini — Google AI"
type: source-summary
tags: [gemini, google, llm, multimodal, api, embeddings]
sources: [2026-05-13_gemini.md]
updated: 2026-05-13
---

# Gemini — Google AI

**Fuente:** https://gemini.google.com/ + https://ai.google.dev/gemini-api/docs/models  
**Nota:** gemini.google.com es el producto de consumo; ai.google.dev es la API para desarrolladores

## Perspectiva de esta fuente

Sitio oficial de Google / Google DeepMind. Posiciona a Gemini como la familia frontier en comprensión multimodal, reasoning y capacidades agénticas. La página de consumo (gemini.google.com) no provee detalles técnicos; el contenido útil viene de ai.google.dev y los blogs de DeepMind/Google.

## Estado de la familia Gemini (mayo 2026)

### Gemini 3 Pro — el más capaz
- **#1 en LMArena Leaderboard**: 1501 Elo
- Supera anteriores en reasoning, multimodalidad y coding
- Soporta **Computer Use tool** (gemini-3-pro-preview)
- Orientado a tareas complejas, análisis avanzado

### Gemini 3 Flash — equilibrio velocidad / calidad
- Reasoning Pro-grade con latencia y costo Flash
- **3x más rápido** que Gemini 2.5 Pro
- **Precio**: $0.50/1M input tokens, $3/1M output tokens
- Soporta Computer Use tool (gemini-3-flash-preview)
- Ideal para: análisis de video, extracción de datos, Visual Q&A, agentic workflows

### Gemini 2.x — generación anterior (aún disponible)
- Gemini 2.0 Flash, Flash-Lite, Pro Experimental

## Capacidades clave

**Multimodalidad completa**: texto, audio, imágenes, código, video en un solo modelo.

**gemini-embedding-2-preview**: Modelo de embeddings multimodal que acepta texto, imagen, video, audio y PDF. Reemplaza la necesidad de múltiples modelos de embedding especializados.

**Computer Use**: Capacidad agéntica de controlar interfaces de computadora (disponible en preview).

**Context windows largas**: Permite procesar documentos extensos sin chunking agresivo.

## Distinciones importantes

| Producto | URL | Para quién |
|---------|-----|-----------|
| Gemini (app) | gemini.google.com | Usuarios finales, chatbot |
| Gemini API | ai.google.dev | Desarrolladores, acceso programático |
| Gemini en Google Search | search + AI Mode | Búsqueda aumentada con IA |

## Conexiones

- [[entities/gemini]] — página de entidad completa
- [[concepts/rag]] — gemini-embedding-2-preview relevante para RAG multimodal
- [[concepts/multi-agent-frameworks]] — Gemini 3 Flash mencionado como LLM para agents
- [[langgraph]] — Gemini es uno de los LLMs configurables en LangGraph
- [[crewai]] — Gemini es uno de los LLMs configurables en CrewAI
