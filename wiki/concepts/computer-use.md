---
title: "Computer Use — Agentes que controlan interfaces gráficas"
type: concept
tags: [computer-use, gui, automatización, claude, gemini, agentes, screenshot, seguridad]
sources: [2026-05-13_computer-use-anthropic.md, 2026-05-13_gemini.md]
updated: 2026-05-13
---

# Computer Use — Agentes que controlan interfaces gráficas

## Qué es

Computer Use es la capacidad de un modelo LLM de **ver interfaces gráficas y ejecutar acciones** (clics, teclado, scroll) como lo haría un humano. El modelo observa capturas de pantalla y decide qué acción tomar a continuación.

Es la materialización del patrón Tool Use [[agentic-design-patterns]] en su forma más completa: en lugar de llamar a APIs estructuradas, el agente puede usar cualquier interfaz visual, sin importar si tiene una API.

---

## Cómo funciona: el agent loop visual

```
┌─────────────────────────────────────────────────────────┐
│                    AGENT LOOP                           │
│                                                         │
│  1. Claude solicita: "screenshot"                       │
│        │                                                │
│        ▼ (tu app captura pantalla)                      │
│  2. Claude ve la imagen → decide acción                 │
│        │                                                │
│        ▼                                                │
│  3. Claude emite: left_click at [x, y]                 │
│        │                                                │
│        ▼ (tu app ejecuta el clic)                       │
│  4. Claude solicita: "screenshot" → verificar           │
│        │                                                │
│        └──► repeat hasta completar la tarea             │
└─────────────────────────────────────────────────────────┘
```

**Clave arquitectural**: el modelo NO ejecuta las acciones directamente. Tu aplicación es el intermediario que:
1. Recibe la solicitud de acción de Claude/Gemini
2. La ejecuta en el entorno real (VM, container, browser)
3. Captura el resultado (screenshot) y lo devuelve al modelo

---

## Modelos con Computer Use (mayo 2026)

| Proveedor | Modelos | Estado |
|-----------|---------|--------|
| **Anthropic** | Claude Opus 4.7, Opus 4.6, Sonnet 4.6, Opus 4.5 | Beta |
| **Google** | Gemini 3 Pro, Gemini 3 Flash (gemini-3-pro-preview, gemini-3-flash-preview) | Preview |

La arquitectura es idéntica conceptualmente entre ambos proveedores. La implementación de referencia de Anthropic (Docker + Xvfb) es transferible.

---

## Acciones disponibles (Claude, versión más reciente)

| Categoría | Acciones |
|-----------|---------|
| **Vista** | screenshot, zoom (región específica) |
| **Mouse** | left_click, right_click, double_click, drag, scroll, mouse_move |
| **Teclado** | type, key, hold_key |
| **Coordinación** | wait |
| **Modificadores** | shift+click, ctrl+click, cmd+click via parámetro `text` |

---

## Entorno de ejecución recomendado

```
Docker Container (aislado)
├── Xvfb (display virtual X11)
├── Mutter + Tint2 (window manager ligero)
├── Aplicaciones instaladas (Firefox, LibreOffice, editores)
└── Agent loop (tu código que media entre Claude y el entorno)
```

El aislamiento en Docker es la primera capa de defensa de seguridad.

---

## Casos de uso

- **Automatización de testing**: navegar UIs que no tienen API
- **Scraping robusto**: interactuar con sitios web con JavaScript pesado
- **Automatización de flujos de trabajo** en aplicaciones de escritorio legacy
- **Migración de datos** entre sistemas sin API
- **QA automatizado**: reproducir bugs reportados por usuarios
- **Background information gathering**: navegar y recopilar información de múltiples fuentes

---

## Consideraciones de seguridad críticas

Computer Use amplifica dos vulnerabilidades OWASP:
- **LLM01 (Prompt Injection)**: una página web maliciosa puede instrur al agente a ejecutar acciones dañinas → el modelo "ve" las instrucciones inyectadas como parte del entorno
- **LLM06 (Excessive Agency)**: con acceso a un escritorio completo, el agente puede hacer prácticamente cualquier cosa

**Mitigaciones**:
1. Container con mínimos privilegios
2. Red restringida a allowlist de dominios
3. HITL para: transacciones financieras, aceptar TOS, acceso a credenciales
4. No almacenar credenciales en el entorno del agente
5. Clasificadores de prompt injection en screenshots (Anthropic los incluye)
6. Límites de steps para prevenir bucles

---

## Benchmark de referencia

**WebArena**: estado del arte para Claude en navegación web multi-step entre sitios reales (no simulados). Claude Opus 4.7 logra los mejores resultados entre sistemas single-agent.

---

## Coordinadas y resolución

Para pantallas de alta resolución, la API de Anthropic redimensiona las imágenes (máx. 1568px en el lado largo). El modelo devuelve coordenadas en el espacio de la imagen redimensionada. La app debe escalar las coordenadas de vuelta al espacio real antes de ejecutar.

**Excepción**: Claude Opus 4.7 soporta hasta 2576px con coordenadas 1:1, sin necesidad de escalar.

---

## Tensión: Computer Use + Seguridad

Computer Use convierte a un agente en un actor con acceso a toda la interfaz del sistema. Combinado con el hallazgo de Fang et al. (GPT-4 explota 87% de CVEs autónomamente), un agente con Computer Use sin restricciones es una amenaza potencial seria. El aislamiento no es opcional.

---

## Conexiones

- [[computer-use]] — fuente primaria (Anthropic docs)
- [[entities/gemini]] — Gemini 3 también soporta Computer Use
- [[concepts/llm-security]] — seguridad y riesgos
- [[owasp-llm-top10]] — LLM01 + LLM06
- [[concepts/agentic-design-patterns]] — Computer Use como extensión de Tool Use
- [[concepts/langgraph]] — LangGraph puede orquestar agentes con Computer Use
