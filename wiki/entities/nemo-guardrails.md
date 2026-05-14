---
title: "NeMo Guardrails — NVIDIA"
type: entity
tags: [nvidia, guardrails, seguridad, open-source, langchain, colang]
sources: [2026-05-13_nemo-guardrails.md]
updated: 2026-05-13
---

# NeMo Guardrails (NVIDIA)

## Identidad

**Tipo**: Toolkit open-source de seguridad para LLMs  
**Organización**: NVIDIA  
**Repositorio**: `github.com/NVIDIA/NeMo-Guardrails`  
**Instalación**: `pip install nemoguardrails`  
**Lenguaje propio**: Colang (1.0 y 2.0)

---

## Qué hace

Añade capas de control programables (rails) a aplicaciones LLM. A diferencia del prompt engineering puro, las rails son código explícito que intercepta el flujo de la conversación antes y después del LLM.

---

## Los 5 tipos de rails

| Rail | Cuándo actúa | Caso de uso |
|------|-------------|------------|
| Input | Antes del LLM | Detectar/bloquear prompt injection directa |
| Dialog | En el prompt | Respuestas predefinidas, restricciones de dominio |
| Retrieval | En RAG | Filtrar chunks maliciosos (indirect injection) |
| Execution | En tool calls | Supervisar herramientas del agente |
| Output | Antes del usuario | Validar respuesta final |

---

## Integraciones

- **LangChain**: envuelve cadenas LangChain con rails
- **LLMs**: OpenAI, LLaMA-2, Falcon, Vicuna, Mosaic
- **API compatible con OpenAI** Chat Completions

---

## Posicionamiento

NeMo Guardrails resuelve el problema de prompt injection desde la capa de software — no desde el prompt. Es complementario a las técnicas de prompt engineering defensivo ([[learn-prompting-defense]]) y a los frameworks de agentes ([[langgraph]], [[crewai]]).

---

## Conexiones

- [[nemo-guardrails]] — resumen de la fuente
- [[concepts/prompt-injection]] — el problema que mitiga
- [[concepts/llm-security]] — marco de seguridad agéntica
- [[concepts/rag]] — Retrieval Rail específico para RAG
