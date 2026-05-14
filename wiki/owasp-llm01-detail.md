---
title: "OWASP LLM01:2025 — Prompt Injection (detalle)"
type: source-summary
tags: [seguridad, prompt-injection, owasp, agentes, llm]
sources: [2026-05-13_owasp-llm01-prompt-injection.md]
updated: 2026-05-13
---

# OWASP LLM01:2025 — Prompt Injection (detalle)

> Página específica de LLM01, complementando la visión general de [[owasp-llm-top10]].

## Definición OWASP

Una vulnerabilidad de prompt injection ocurre cuando "user prompts alter the LLM's behavior or output in unintended ways". Los ataques funcionan incluso si los humanos no perciben el contenido malicioso, siempre que el modelo lo interprete.

**Distinción clave**: prompt injection ≠ jailbreaking. Jailbreaking es un subtipo donde el atacante causa que "el modelo desatienda sus protocolos de seguridad completamente".

---

## Taxonomía de vectores (9 escenarios)

| # | Tipo | Mecanismo |
|---|------|-----------|
| 1 | Directa | Usuario inyecta instrucciones en chatbot; ignora directrices del sistema |
| 2 | Indirecta | Página web con instrucciones ocultas; LLM las ejecuta al resumir |
| 3 | Accidental | Instrucciones de detección de IA en JD → candidato activa inadvertidamente |
| 4 | RAG envenenado | Documento modificado en vector store altera respuestas del agente |
| 5 | Código en email | CVE-2024-5184: inyección en asistente de correo → acceso a datos sensibles |
| 6 | Payload dividido | Partes de prompt malicioso en CV; cuando LLM evalúa, se combinan |
| 7 | Multimodal | Prompt malicioso embebido en imagen junto con texto benigno |
| 8 | Sufijo adversarial | String aparentemente incoherente → bypassa safety measures |
| 9 | Multilingüe/ofuscado | Base64, emojis, múltiples idiomas eluden filtros de texto |

---

## Impactos

- Divulgación de datos sensibles o prompts del sistema
- Ejecución de comandos en sistemas conectados
- Manipulación de procesos críticos de decisión
- Acceso no autorizado a funciones del LLM

---

## Las 7 mitigaciones OWASP

1. **Constreñimiento de comportamiento**: instrucciones específicas del rol + "enforce strict context adherence"
2. **Validación de formatos**: outputs deterministas donde sea posible
3. **Filtrado semántico**: "Assess context relevance, groundedness, and question/answer relevance"
4. **Menor privilegio**: "restrict the model's access privileges to the minimum necessary"
5. **Human-in-the-loop**: para operaciones privilegiadas
6. **Segregación de contenido**: "Separate and clearly denote untrusted content"
7. **Pruebas adversariales**: penetration testing tratando el modelo como "untrusted user"

---

## La realidad fundamental

> "Given the stochastic influence at the heart of the way models work, it is unclear if there are fool-proof methods of prevention for prompt injection."

No existe prevención garantizada. Las mitigaciones reducen probabilidad e impacto, no los eliminan.

---

## Conexiones

- [[owasp-llm-top10]] — contexto del Top 10 completo
- [[concepts/prompt-injection]] — síntesis de todas las fuentes
- [[concepts/llm-security]] — marco de seguridad agéntica
- [[simon-willison-2022]] — origen histórico del concepto
- [[simon-willison-2023]] — indirect injection en detalle
