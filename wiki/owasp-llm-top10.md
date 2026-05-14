---
title: "OWASP Top 10 for LLM Applications 2025"
type: source-summary
tags: [seguridad, owasp, llm, vulnerabilidades, prompt-injection, agentes]
sources: [2026-05-13_owasp-llm-top10.md]
updated: 2026-05-13
---

# OWASP Top 10 for LLM Applications 2025

**Fuente:** https://genai.owasp.org/llm-top-10/

## Perspectiva de esta fuente

El estándar de la industria para asegurar aplicaciones basadas en LLMs. OWASP es la autoridad de referencia en seguridad de aplicaciones. Esta lista es el equivalente para IA generativa del OWASP Top 10 clásico para aplicaciones web.

## Las 10 vulnerabilidades

| ID | Nombre | Criticidad en contextos agénticos |
|----|--------|----------------------------------|
| **LLM01** | Prompt Injection | 🔴 Crítica |
| **LLM02** | Sensitive Information Disclosure | 🟠 Alta |
| **LLM03** | Supply Chain | 🟠 Alta |
| **LLM04** | Data and Model Poisoning | 🟠 Alta |
| **LLM05** | Improper Output Handling | 🟡 Media |
| **LLM06** | Excessive Agency | 🔴 Crítica |
| **LLM07** | System Prompt Leakage | 🟠 Alta |
| **LLM08** | Vector and Embedding Weaknesses | 🟠 Alta |
| **LLM09** | Misinformation | 🟡 Media |
| **LLM10** | Unbounded Consumption | 🟡 Media-Alta |

## Descripción de las más críticas para sistemas agénticos

### LLM01: Prompt Injection
El vector de ataque más común en sistemas agénticos. Tipos:
- **Directa**: el usuario inyecta instrucciones maliciosas en el input
- **Indirecta**: contenido externo (páginas web, documentos, emails) inyecta instrucciones que el agente ejecuta

En Computer Use, el riesgo es máximo: una página web maliciosa puede instruir al modelo a ejecutar acciones dañinas.

### LLM06: Excessive Agency
El agente tiene demasiados permisos, herramientas o autonomía. Un agente comprometido via LLM01 con LLM06 puede causar daño real. El paper de Fang et al. es la evidencia empírica directa.

**Mitigaciones**: principio de menor privilegio, HITL para acciones irreversibles, auditoría de todas las acciones.

### LLM08: Vector and Embedding Weaknesses
Afecta directamente pipelines RAG:
- Envenenamiento de la base vectorial (insertar chunks maliciosos)
- Recuperación inadvertida de información sensible
- Ataques de inversión de embeddings para recuperar datos de entrenamiento

### LLM10: Unbounded Consumption
En sistemas agénticos con bucles, el agente puede consumir recursos ilimitados si no hay límites. Ver límite de recursión de LangGraph (1000 steps) como mitigación estructural.

## Relación con el SDLC

El OWASP Top 10 LLM debe integrarse en la fase de **Design** y **Test** del SDLC para sistemas que usan LLMs. DevSecOps debe incluir revisiones específicas para estas vulnerabilidades. Ver [[concepts/llm-security]] y [[concepts/devsecops]].

## Conexiones

- [[concepts/llm-security]] — síntesis de seguridad para sistemas LLM
- [[llm-agents-vulnerabilities]] — evidencia empírica de LLM06
- [[concepts/devsecops]] — integración de seguridad LLM en el SDLC
- [[computer-use]] — Computer Use amplifica LLM01 y LLM06
- [[concepts/rag]] — LLM08 afecta directamente a RAG
