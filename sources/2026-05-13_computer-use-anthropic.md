# Anthropic — Computer Use

**URL:** https://docs.anthropic.com/en/docs/build-with-claude/computer-use  
**Fecha de captura:** 2026-05-13  
**Estado:** Beta (requiere beta header en la API)

---

## Qué es Computer Use

Feature beta de Claude que permite interactuar con entornos de escritorio: ver pantallas (screenshots), mover el mouse, hacer clics y escribir. El modelo ve capturas de pantalla y emite acciones (coordenadas de clic, texto, teclas).

## Modelos compatibles

- Claude Opus 4.7, Opus 4.6, **Sonnet 4.6** (beta header: `computer-use-2025-11-24`)
- Claude Opus 4.5, Sonnet 4.5, Haiku 4.5 (beta header: `computer-use-2025-01-24`)

## Arquitectura: el Agent Loop

```
1. Usuario da instrucción
2. Claude solicita screenshot
3. Tu app captura screenshot y lo envía
4. Claude decide acción (clic, tipo, tecla)
5. Tu app ejecuta la acción
6. Claude solicita nuevo screenshot
7. Repeat hasta completar la tarea
```

El "agent loop" es el ciclo Claude → tool request → app ejecuta → result → Claude.

## Acciones disponibles

**Todas las versiones**: screenshot, left_click, type, key, mouse_move  
**computer_20250124**: + scroll, drag, right_click, double_click, triple_click, hold_key, wait  
**computer_20251124** (Opus 4.7/4.6, Sonnet 4.6): + zoom (inspeccionar región en alta resolución)

## Entorno de ejecución

Requiere entorno sandboxed:
- Display virtual (Xvfb — X Virtual Framebuffer)
- Desktop ligero (Mutter + Tint2 en Linux)
- Docker container con aplicaciones preinstaladas (Firefox, LibreOffice, editores)
- El modelo NO conecta directamente al entorno — tu app es el intermediario

## Consideraciones de seguridad

**Riesgos únicos**:
- **Prompt injection visual**: instrucciones en páginas web o imágenes pueden sobreescribir instrucciones del usuario
- El modelo puede seguir comandos encontrados en contenido de páginas web

**Mitigaciones recomendadas**:
1. VM o container con privilegios mínimos
2. No dar acceso a datos sensibles ni credenciales sin supervisión
3. Limitar acceso a internet (allowlist de dominios)
4. HITL para decisiones con consecuencias reales
5. Los clasificadores de Anthropic detectan prompt injection en screenshots automáticamente

## Benchmark

Estado del arte en WebArena (navegación web multi-step en sitios reales) entre sistemas single-agent.

## Pricing

- System prompt overhead: 466-499 tokens adicionales
- Definición de la tool: 735 tokens por tool
- Imágenes de screenshots: ver pricing de Vision

## Coordinate scaling

Para pantallas de alta resolución: la API limitia a 1568px en el lado más largo. Claude devuelve coordenadas en el espacio de la imagen redimensionada; la app debe escalar de vuelta al espacio de la pantalla real. Excepción: Claude Opus 4.7 soporta hasta 2576px con coordenadas 1:1.

## Relación con Gemini Computer Use

La arquitectura es idéntica conceptualmente: el modelo emite coordenadas basándose en capturas de pantalla. Gemini 3 Pro/Flash también soportan Computer Use tool. La implementación de referencia de Anthropic (Docker + Xvfb + agent loop) es aplicable a cualquier modelo que soporte la misma interfaz.
