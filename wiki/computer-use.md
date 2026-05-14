---
title: "Computer Use — Anthropic (Claude)"
type: source-summary
tags: [computer-use, claude, anthropic, automatización, gui, agent-loop, seguridad]
sources: [2026-05-13_computer-use-anthropic.md]
updated: 2026-05-13
---

# Computer Use — Anthropic (Claude)

**Fuente:** https://docs.anthropic.com/en/docs/build-with-claude/computer-use

## Perspectiva de esta fuente

Documentación oficial de Anthropic. La implementación de Computer Use de Claude es la referencia arquitectural más completa y documentada del sector. La arquitectura es transferible a otros modelos (Gemini 3 también lo soporta) porque el mecanismo es análogo.

## Qué es

Feature beta de Claude que permite interactuar con entornos de escritorio. El modelo ve screenshots y emite acciones (coordenadas de clic, teclado, scroll). Estado del arte en WebArena (navegación web multi-step).

## La arquitectura del agent loop

```
Usuario: "Descarga el PDF del sitio X y extrae la tabla"
  │
  ▼
Claude → "screenshot" (solicita ver la pantalla)
  │
  ▼ (tu app captura screenshot)
Claude → "left_click at [245, 380]" (clic en el botón)
  │
  ▼ (tu app ejecuta el clic)
Claude → "screenshot" (verificar resultado)
  │
  ▼
... repeat hasta completar la tarea
```

**Clave**: Claude NO ejecuta las acciones directamente. Tu aplicación es el intermediario que ejecuta las acciones y captura los resultados.

## Acciones disponibles

| Versión | Acciones |
|---------|---------|
| Base | screenshot, left_click, type, key, mouse_move |
| `computer_20250124` | + scroll, drag, right_click, double_click, hold_key, wait |
| `computer_20251124` | + zoom (para inspeccionar regiones en alta resolución) |

## Entorno de ejecución recomendado

- Docker container aislado
- Display virtual: Xvfb (X Virtual Framebuffer)
- Desktop ligero: Mutter + Tint2 en Linux
- Sin acceso a datos sensibles del host

## Consideraciones de seguridad críticas

Computer Use amplifica LLM01 (Prompt Injection) a riesgo de acciones físicas:
- Una página web maliciosa puede inyectar instrucciones que Claude ejecuta como clics
- Anthropic añade clasificadores que detectan prompt injection en screenshots
- El clasificador puede desactivarse si el caso de uso no tiene humano en el loop

**Mitigaciones**:
1. VM/container con privilegios mínimos
2. Allowlist de dominios accesibles
3. HITL para transacciones financieras, aceptar TOS, etc.
4. No dar acceso a credenciales sin supervisión

## Pricing

- 466-499 tokens extra en system prompt
- 735 tokens por definición de la tool
- Más costo de imágenes (screenshots = Vision pricing)

## Comparativa con Gemini Computer Use

La arquitectura es conceptualmente idéntica (screenshot → coordenadas → acción). La diferencia es la implementación de referencia y los modelos disponibles. Anthropic provee una implementación Docker más documentada. Ver [[entities/gemini]] para disponibilidad en Gemini 3.

## Conexiones

- [[concepts/computer-use]] — concepto unificado de Computer Use
- [[entities/gemini]] — Gemini 3 Pro/Flash también soportan Computer Use
- [[concepts/llm-security]] — seguridad y riesgos de prompt injection visual
- [[owasp-llm-top10]] — LLM01 + LLM06 son los riesgos principales
- [[llm-agents-vulnerabilities]] — agentes con Computer Use amplican el riesgo de Fang et al.
