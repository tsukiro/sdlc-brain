---
title: "LLM Agents can Autonomously Exploit One-day Vulnerabilities (Fang et al.)"
type: source-summary
tags: [seguridad, vulnerabilidades, agentes, gpt4, devsecops, cve, ofensivo]
sources: [2026-05-13_llm-agents-vulnerabilities.md]
updated: 2026-05-13
---

# LLM Agents can Autonomously Exploit One-day Vulnerabilities

**Fuente:** https://arxiv.org/abs/2404.08144 (Fang et al.)

## Perspectiva de esta fuente

Paper de investigación de seguridad ofensiva. Demuestra empíricamente que los agentes LLM más avanzados pueden explotar vulnerabilidades reales de forma autónoma, con implicaciones directas para DevSecOps y el ciclo de vida de desarrollo seguro.

## El hallazgo central

| Modelo | Con CVE | Sin CVE |
|--------|---------|---------|
| **GPT-4** | **87%** | 7% |
| GPT-3.5 | 0% | 0% |
| Open-source LLMs | 0% | 0% |
| ZAP (scanner DAST) | 0% | — |
| Metasploit | 0% | — |

15 vulnerabilidades one-day, incluyendo CVEs críticos.

## Por qué es relevante para el SDLC

### Nuevos vectores en CI/CD con agentes

Si un agente LLM tiene acceso al código, al entorno de ejecución o a las herramientas de CI/CD, y es comprometido via prompt injection, puede convertirse en un atacante interno que:
- Inyecta backdoors en el código
- Explota vulnerabilidades del entorno
- Exfiltra credenciales o secretos

### Las herramientas tradicionales son insuficientes

ZAP y Metasploit — los estándares del sector para DAST — tuvieron 0% de éxito. Los agentes LLM razonan sobre el código y el contexto de formas que los scanners basados en firmas no pueden detectar.

### La brecha modelo importa

La brecha GPT-4 (87%) vs. todo lo demás (0%) indica que la capacidad de explotar vulnerabilidades está altamente concentrada en los modelos frontier. A medida que más modelos alcancen esa capacidad, el riesgo se distribuirá.

## Relación con OWASP LLM Top 10

Este paper es evidencia empírica de:
- **LLM06 (Excessive Agency)**: un agente con demasiado acceso puede convertirse en atacante
- **LLM01 (Prompt Injection)**: vector de entrada que podría activar la explotación

Ver [[owasp-llm-top10]] y [[concepts/llm-security]].

## Implicaciones de diseño

1. **Principio de menor privilegio**: los agentes solo deben tener el acceso mínimo necesario
2. **Aislamiento**: los agentes de CI/CD deben correr en entornos con red restringida
3. **Auditoría**: todas las acciones de agentes deben ser trazadas (LangSmith/equivalente)
4. **HITL en acciones destructivas**: ningún agente debe poder modificar producción sin aprobación humana

## Conexiones

- [[concepts/llm-security]] — síntesis de seguridad para sistemas LLM
- [[owasp-llm-top10]] — marco de vulnerabilidades relacionado
- [[concepts/devsecops]] — DevSecOps debe adaptarse a agentes LLM
- [[computer-use]] — Computer Use amplifica el riesgo de Excessive Agency
