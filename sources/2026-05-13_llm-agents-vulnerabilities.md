# LLM Agents can Autonomously Exploit One-day Vulnerabilities

**URL:** https://arxiv.org/abs/2404.08144  
**Autores:** Fang et al.  
**Fecha de captura:** 2026-05-13

---

## Qué demuestra el paper

Los agentes LLM (específicamente GPT-4) pueden explotar autónomamente vulnerabilidades reales de software ("one-day" — con CVE publicado pero sin parche aplicado aún).

## Metodología

- Dataset: 15 vulnerabilidades one-day, incluyendo algunas categorizadas como críticas en sus CVE
- Modelos testeados: GPT-4, GPT-3.5, modelos open-source, herramientas tradicionales (ZAP, Metasploit)

## Resultados

| Configuración | Tasa de éxito |
|--------------|--------------|
| GPT-4 con descripción CVE | **87%** |
| GPT-4 sin descripción CVE | 7% |
| GPT-3.5 | 0% |
| Modelos open-source | 0% |
| ZAP (scanner tradicional) | 0% |
| Metasploit | 0% |

## Hallazgos clave

1. GPT-4 con acceso a la descripción del CVE puede explotar el 87% de las vulnerabilidades críticas de forma autónoma
2. Sin la descripción CVE, GPT-4 cae al 7% → la inteligencia del agente depende del conocimiento del contexto
3. Todos los demás modelos y herramientas tradicionales fallaron completamente
4. Las herramientas DAST/SAST tradicionales son insuficientes contra agentes que razonan sobre el código

## Implicaciones para DevSecOps

- Los agentes LLM son vectores de ataque de nueva generación que los scanners tradicionales no detectan
- El pipeline de CI/CD que usa agentes LLM introduce nuevos vectores de riesgo
- La brecha entre GPT-4 y el resto es extrema → la capacidad de explotar vulnerabilidades está concentrada en los modelos más capaces
- Principio de menor privilegio es crítico: los agentes no deben tener más acceso del necesario

## Relación con OWASP LLM Top 10

Este paper es evidencia directa de: LLM06 (Excessive Agency) y complementa LLM01 (Prompt Injection como vector de entrada).
