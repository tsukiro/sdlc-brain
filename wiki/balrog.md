---
title: "BALROG — Benchmarking Agentic LLM and VLM Reasoning On Games"
type: source-summary
tags: [evaluación, benchmark, agentes, vlm, planificación, largo-plazo, iclr]
sources: [2026-05-13_balrog.md]
updated: 2026-05-13
---

# BALROG — Benchmarking Agentic LLM and VLM Reasoning On Games

**Fuente:** https://arxiv.org/abs/2411.13543 (ICLR 2025)  
**Nota:** El ID original proporcionado (2408.03297) correspondía a otro paper. ID correcto: 2411.13543.

## Perspectiva de esta fuente

Paper académico aceptado en ICLR 2025. La fuente de evaluación agéntica más rigurosa del wiki: usa entornos de juego que requieren planificación genuina a largo plazo, algo que los benchmarks estáticos no pueden medir.

## Por qué importa

BALROG responde directamente a la pregunta abierta del wiki: *"¿Cómo se evalúa la calidad de un sistema agéntico en producción? El benchmark zero-shot no sirve."*

Su respuesta: evalúa mediante **episodios de juego** que requieren planificación secuencial, exploración y razonamiento espacial — capacidades que un prompt de una sola pregunta no mide.

## Entornos y dificultad

| Entorno | Dificultad | Tipo |
|---------|-----------|------|
| BabyAI | Baja | Navegación simple |
| TextWorld | Media-baja | Aventura de texto |
| Baba Is AI | Media | Puzzles de lógica (reglas cambiantes) |
| Crafter | Media-alta | Mundo abierto (tipo Minecraft) |
| MiniHack | Alta | Dungeon customizable |
| NetHack | Extrema | Roguelike (años de maestría para humanos) |

## Resultados

**Mejor modelo**: DeepSeek-R1 con 34.9% ± 2.1% de progreso promedio — ningún modelo supera el 35%.

**Hallazgo contraintuitivo**: Los VLMs rinden **peor** con input visual que los LLMs con solo texto en muchas tareas. Las deficiencias en toma de decisiones visual son severas aun en los modelos más capaces.

## Qué mide que otros benchmarks no miden

- Planificación a largo plazo (horizonte de cientos de pasos)
- Exploración y gestión de información desconocida
- Adaptación a cambios de entorno inesperados
- Razonamiento espacial en entornos dinámicos

**No mide**: calidad de respuesta en single-turn, recuperación de información, coding estático.

## Implicación para el SDLC de agentes

Los sistemas agénticos de producción deben evaluarse en **trayectorias completas**, no en outputs punto a punto. Ver [[concepts/agent-evaluation]].

## Conexiones

- [[concepts/agent-evaluation]] — evaluación de agentes en producción
- [[concepts/llm-agents]] — limitaciones de planificación confirmadas empíricamente
- [[concepts/planning]] — planning a largo plazo es el desafío #2 de Weng; BALROG lo cuantifica
- [[langsmith-evaluation]] — herramienta para trajectory evaluation en producción
