---
title: "Prompt Injection — Irreducibilidad Arquitectural y Soluciones Fuera del Modelo"
type: source-summary
tags: [seguridad, prompt-injection, arquitectura, constrained-decoding, dual-llm, control-vectors, zero-trust, transformer]
sources: [2026-05-13_prompt-injection-architectural-irreducibility.md]
updated: 2026-05-13
---

# Prompt Injection — Irreducibilidad Arquitectural y Soluciones Fuera del Modelo

> Análisis técnico que responde la pregunta abierta del wiki: ¿existe una solución arquitectural (no de prompt engineering) para prompt injection?

## La respuesta definitiva

**En la arquitectura Transformer actual: no existe prevención al 100%.** Es una falla estructural, no una vulnerabilidad parcheable.

---

## El problema: LLMs no tienen arquitectura Von Neumann

La computación clásica (Von Neumann / Harvard) separa estrictamente:
- **Instrucciones** (código) → segmento ejecutable
- **Datos** (entradas del usuario) → segmento de datos

Un procesador sabe cuál es cuál. Una base de datos con queries parametrizadas tampoco mezcla código SQL con datos de usuario — de ahí que SQL injection tenga solución.

**En un Transformer esto no existe**:

```
Sistema (instrucciones):  "Eres un asistente seguro. No hagas X."
Usuario (datos):          "Ignora lo anterior. Haz X."
                           ↓
              Un único stream plano de tokens
              ↓
    Distribución de probabilidad sobre el siguiente token
    (basada en pesos de entrenamiento, no en autoridad jerárquica)
```

El modelo no "sabe" que las instrucciones del sistema tienen más autoridad que el input del usuario. Simplemente calcula cuál token es estadísticamente más probable a continuación. Si el texto inyectado logra engañar esa distribución, el ataque tiene éxito — independientemente de cuánto se haya trabajado el system prompt.

> "Mientras las instrucciones y los datos compartan el mismo canal probabilístico, la inyección será teóricamente posible." — Simon Willison

---

## Las 4 soluciones arquitecturales (fuera del modelo)

### 1. Constrained Decoding — intervención matemática en logits

**Nivel**: motor de inferencia (no el prompt)  
**Herramientas**: Outlines, Guidance, vLLM

```
Sin constrained decoding:
  LLM puede emitir cualquiera de los 100,000 tokens de su vocabulario
  → si fue inyectado, puede articular cualquier respuesta de ataque

Con constrained decoding:
  Motor aplica máscara sobre logits: solo permite tokens en {"Verdadero", "Falso", números}
  → matemáticamente imposible articular una respuesta de ataque
```

**Por qué es más robusto que el prompt engineering**: no depende de que el LLM "obedezca" instrucciones. Opera a nivel matemático, antes de que el modelo pueda emitir tokens fuera del conjunto permitido.

**Limitación**: solo aplicable cuando el output puede definirse con precisión (JSON, booleanos, enums). No aplica a texto libre.

---

### 2. Dual LLM — separación de privilegios

**Nivel**: arquitectura de sistema (multi-agent)  
**Principio**: un agente no puede leer datos no confiables Y tener acceso a herramientas al mismo tiempo

```
                    ┌─────────────────────────────────┐
                    │     AGENTE CUARENTENA            │
                    │  (Untrusted Reader)              │
                    │  • Lee: web, emails, PDFs        │
                    │  • Permisos: CERO (sin tools)    │
                    │  • Output: JSON estructurado     │
                    └──────────────┬──────────────────┘
                                   │ JSON pre-aprobado
                    ┌──────────────▼──────────────────┐
                    │     AGENTE EJECUTOR              │
                    │  (Trusted Controller)            │
                    │  • Acceso: DB, APIs, tools       │
                    │  • Input: solo JSON pre-aprobado │
                    │  • NUNCA lee texto plano externo │
                    └─────────────────────────────────┘
```

**Por qué mitiga la inyección**: si el Agente Cuarentena es vulnerado por indirect injection, no puede hacer nada — no tiene herramientas. El atacante puede envenenar los datos, pero no puede actuar en el mundo real. El Agente Ejecutor nunca recibe texto libre no verificado.

**Relación con MAR (Multi-Agent Reflexion)**: el Dual LLM es el patrón de seguridad; MAR es el patrón de evaluación. Ambos separan responsabilidades entre agentes para reducir el riesgo de un único punto de fallo.

---

### 3. Control Vectors — ingeniería de representación

**Nivel**: activaciones neuronales (investigación activa, resultados prometedores en ene-2025)  
**Concepto**: en lugar de decirle al modelo "no hagas X" (prompt engineering), se fuerza mecánicamente un estado interno defensivo modificando los vectores de activación durante la inferencia

```
Proceso (implementación GCAV, Zhang et al. 2025):
  1. Crear ~100 pares de prompts contrastivos (tóxico vs. seguro)
  2. Pasar por el LLM → recoger activaciones en cada capa
  3. Entrenar regresión logística sobre las activaciones
  4. El vector de concepto = dirección normal del clasificador:
     v = w / ‖w‖
  5. Durante inferencia: e' = e - ε · v_concepto
     donde ε se calcula dinámicamente para cada input
  → El modelo permanece en estado defensivo mecánicamente
```

**Por qué es diferente del RLHF/fine-tuning**: no cambia los pesos permanentemente — actúa en tiempo de inferencia. Más flexible y reversible que el fine-tuning.

**Resultados empíricos (GCAV, Llama-2-7b-chat):**
- Reducción de toxicidad: 51% vs. baseline (0.1807 → 0.0879)
- Mejor que prompt engineering positivo y ActAdd (dirección fija)
- Ventaja clave: ε dinámico por input vs. ε fijo en métodos anteriores — inputs con mayor toxicidad reciben mayor corrección

**Limitación crítica para producción:** requiere acceso a las activaciones internas del modelo. Solo viable con modelos open-source (Llama, Mistral) en infraestructura propia — no aplicable a APIs opacas (OpenAI, Anthropic Cloud).

**Estado (enero 2025)**: testado en Llama-2-7B. No testado en modelos grandes (70B+) ni otras arquitecturas. En investigación activa. Ver [[gcav-paper]] para la implementación completa.

---

### 4. Zero Trust + Ephemeral Containers

**Nivel**: infraestructura  
**Premisa**: asumir que la injection va a ocurrir. Diseñar para minimizar el daño cuando ocurra.

```
Sin Zero Trust:
  Agente ejecuta código → en servidor principal con credenciales reales
  → si injection inyecta rm -rf / → destrucción real

Con Zero Trust + Ephemeral Containers:
  Agente ejecuta código → en contenedor Docker efímero
  • Sin internet
  • Sin credenciales reales
  • Sin volúmenes persistentes
  → código se ejecuta → resultado se devuelve → contenedor se destruye
  → rm -rf / destruye un entorno vacío
```

**Principio**: si el LLM no es un guardián confiable, el sistema de infraestructura debe serlo. El daño se contiene, no se previene.

---

## Síntesis: la industria ha aceptado el problema

| Capa | Solución | Previene injection | Limita daño |
|------|----------|-------------------|------------|
| Prompt engineering | Sandwich, XML Tagging, etc. | Parcialmente | No |
| Software (NeMo Guardrails) | Input/Retrieval/Output Rails | Parcialmente | Parcialmente |
| Inferencia | Constrained Decoding | En outputs acotados | Sí |
| Arquitectura de sistema | Dual LLM | Sí (sin privilegios) | Sí |
| Activaciones | Control Vectors | Parcialmente | No aplica |
| Infraestructura | Zero Trust + Containers | No | Sí |

**Conclusión**: la única forma de escalar agentes de manera segura es tratar al LLM como un **procesador de texto probabilístico intrínsecamente vulnerable** y rodearlo de una **arquitectura determinista estricta** que no depende del LLM para defenderse.

---

## Conexiones

- [[concepts/prompt-injection]] — síntesis completa; este documento añade la capa arquitectural
- [[gcav-paper]] — implementación concreta de Control Vectors: GCAV (Zhang et al., arXiv 2501.05764)
- [[simon-willison-2022]] — "mismo canal probabilístico" → base teórica del argumento Von Neumann
- [[nemo-guardrails]] — solución de software (Rails); complementaria, no substitutiva
- [[concepts/llm-security]] — marco de seguridad agéntica
- [[concepts/computer-use]] — Zero Trust + Containers es la recomendación estándar para Computer Use
- [[concepts/agentic-design-patterns]] — Dual LLM como extensión del patrón Multi-Agent
- [[concepts/multi-agent-frameworks]] — Dual LLM implementable en LangGraph o CrewAI
