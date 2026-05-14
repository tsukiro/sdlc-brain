# Reflexion: Language Agents with Verbal Reinforcement Learning

**URL:** https://arxiv.org/abs/2303.11366  
**Autores:** Shinn, Cassano, Labash, et al.  
**Fecha de captura:** 2026-05-13

---

## Qué es Reflexion

Marco para mejorar agentes de lenguaje mediante retroalimentación lingüística verbal en lugar de actualizar los pesos del modelo. Los agentes "reflexionan verbalmente sobre señales de feedback" y almacenan estas reflexiones en memoria episódica para mejorar decisiones futuras.

## Por qué se llama "Verbal Reinforcement Learning"

El RL tradicional requiere fine-tuning costoso (actualizar gradientes). Reflexion usa:
- Retroalimentación en forma de valores escalares o lenguaje libre
- Memoria episódica que almacena reflexiones textuales
- Múltiples intentos donde el agente aprende de errores sin reentrenamiento

## Arquitectura

Ciclo iterativo:
1. Agente intenta completar la tarea
2. Recibe señales de feedback (externas o autogeneradas)
3. Genera reflexiones verbales sobre el feedback
4. Almacena reflexiones en buffer de memoria episódica
5. Usa el buffer como contexto adicional en el siguiente intento

## Resultados principales

- **91% pass@1 en HumanEval** (coding) — superó GPT-4 que tenía ~80% en el momento del paper
- Mejoras en tareas de decisiones secuenciales
- Mejoras en razonamiento lingüístico

## Costo de múltiples intentos

El paper no detalla costos computacionales explícitamente, pero implícitamente cada intento adicional requiere una invocación adicional del LLM. El trade-off es: calidad vs. latencia y costo.

## Limitaciones

- El mismo modelo genera acciones, evalúa su propio comportamiento y produce reflexiones → riesgo de confirmation bias
- Las reflexiones pueden acumular "ruido" si se generan sobre trayectorias incorrectas sin evidencia concreta
- Limitado por el contexto: las reflexiones pasadas ocupan tokens del contexto

## Investigación posterior (2024-2025)

- **Multi-Agent Reflexion (MAR)**: resuelve el confirmation bias usando un agente separado para evaluar
- **Reflection grounding**: requiere que el agente cite evidencia episódica específica para cada reflexión — reduce las generalizaciones sin base
- **Memory contamination**: en sistemas multi-agente, las reflexiones erróneas de un agente pueden propagarse a otros via memoria compartida
