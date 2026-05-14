---
title: "Control Vectors as Dispositional Traits — Calcagni (LessWrong)"
type: source-summary
tags: [control-vectors, activation-engineering, alignment, ai-safety, dispositional-traits, persona-vectors, anthropic]
sources: []
updated: 2026-05-14
---

# Control Vectors as Dispositional Traits — Calcagni (LessWrong)

**Fuente:** https://www.lesswrong.com/posts/Bf3ryxiM6Gff2zamw/control-vectors-as-dispositional-traits  
**Autor:** Gianluca Calcagni (matemático, consultor IT)  
**Publicado:** 2024-06-23 (actualizado hasta agosto 2025)  
**Contexto:** Ensayo conceptual que reencuadra los control vectors como "rasgos disposicionales" de los LLMs y propone un framework de alineación basado en 12 traits.

---

## La técnica (variante PCA — alternativa a GCAV)

El artículo describe un proceso ligeramente diferente al de GCAV (que usa regresión logística):

```
1. Elegir pares contrastivos:
   sentence_A = ejemplo de comportamiento X (ej. "rechazo")
   sentence_B = ejemplo de comportamiento ¬X (ej. "aceptación")

2. Pasar por el LLM → snapshot de activaciones en cada capa
   (vector de activación por capa)

3. Por cada capa: direction_vector = activation(B) - activation(A)

4. Repetir con muchos pares → matriz donde cada columna = un direction_vector

5. Aplicar PCA a la matriz → dirección dominante = control vector

   (Diferencia con GCAV: PCA vs. dirección normal del clasificador logístico)

6. Durante inferencia: e' = e ± α · v_control
   (+ para amplificar el rasgo, - para suprimirlo)
```

**Diferencia clave respecto a GCAV** ([[gcav-paper]]): Calcagni usa PCA para encontrar la dirección dominante; GCAV usa la dirección normal de un clasificador de regresión logística. Ambos buscan el mismo "eje semántico" del concepto en el espacio de activaciones, con técnicas equivalentes.

---

## El reencuadre conceptual: "Rasgos Disposicionales"

Un control vector no es una instrucción — es la representación interna que el modelo tiene de un **concepto abstracto** (honestidad, rechazo, humor, etc.).

> "Es como forzar a un actor a improvisar un rol. Los LLMs avanzados son grandes actores y pueden ejecutar rasgos disposicionales convincentes."

**Implicación**: cuanto más avanzado el modelo, más rasgos se vuelven controlables — porque el modelo desarrolla una mejor comprensión semántica de cada concepto.

---

## Validación: ¿el vector controla lo que crees?

Técnica de verificación: comparar dos vectores cercanos conceptualmente.

```
vector_honestidad     ← calculado desde pares de prompts honestos/deshonestos
vector_honestidad_fingida ← calculado desde pares de prompts de honestidad performativa

Si los vectores son similares → el modelo no distingue entre los dos conceptos.
Si los vectores son distintos → el modelo tiene representaciones separadas.
```

Esto permite **auditar las capacidades latentes** del modelo: ¿realmente aprendió la distinción entre honestidad real y performativa?

---

## ¿Es esto verdadera AI Safety? — Análisis honesto

### Limitaciones

| Problema | Implicación |
|---|---|
| Probabilístico, no determinista | Funciona "la mayoría de veces", no siempre |
| Sin garantía de cobertura en todos los contextos | Un ataque adversarial puede saltar el vector |
| Sigue vulnerable a prompt injection y jailbreaking | No es una capa de seguridad exhaustiva |
| Control comportamental ≠ alineación completa | El modelo puede ser controlado en conducta y aún no estar alineado con los objetivos humanos |

### Ventajas reales

- Alternativa ligera y reversible al fine-tuning/RLHF
- "Good boy mode" reduce probabilidad de comportamiento errático
- Compatible con muchas arquitecturas ML
- **Escala con las capacidades del modelo**: si se confirma, sería un breakthrough dramático (a mayor capacidad del modelo, más rasgos controlables)
- **Gana tiempo** para el trabajo de alineación real

---

## Framework: 12 Rasgos Disposicionales "Good Boy"

Calcagni propone 12 traits en 4 categorías como mínimo necesario para un LLM seguro. **No incluye** rasgos éticos/legales/virtuosos porque no son ortogonales entre sí y generan conflictos en casos extremos.

### Anti-Optimizer Balances *(evitar comportamiento de optimizador descontrolado)*

| Trait | Descripción |
|---|---|
| **Remissive** | Se detiene inmediatamente cuando se le pide; no reinicia sin orden explícita. Crítico para robots físicos. |
| **Content** | Trabaja con los medios actuales disponibles; no busca adquirir recursos instrumentales (tiempo, energía, influencia). Contramedida al paperclip maximizer. |
| **Passive** | Solo trabaja en las tareas asignadas; comportamiento miópico; no actúa en tiempo libre. |

### Core Beliefs *(cómo percibe y representa la realidad)*

| Trait | Descripción |
|---|---|
| **Truthful** | Objetivo y lógico; reconoce y combate sesgos heredados del training data. |
| **Coherent** | Actúa según sus creencias; actualiza creencias ante nueva evidencia. |
| **Indifferent** | Neutral sobre cómo debería ser el mundo; solo actúa según las tareas dadas. "Direction of Fit": la mente debe adaptarse al mundo, no al revés. |

### Communication Styles *(cómo se comunica)*

| Trait | Descripción |
|---|---|
| **Honest** | No engaña, no manipula, no fabrica, no oculta información. |
| **Forthright** | Explícito, claro, exhaustivo en listar supuestos; confirma comprensión del interlocutor. |
| **Analyst** | Solo comunicación descriptiva; evita declaraciones prescriptivas, normativas o evaluativas. No da sugerencias ni juicios no solicitados. |

### Work Ethos *(cómo ejecuta su trabajo)*

| Trait | Descripción |
|---|---|
| **Scrupulous** | Detecta y pregunta ante ambigüedades o instrucciones poco claras; humanos son responsables de la descripción del resultado esperado. |
| **Conscientious** | Captura el "espíritu" de la tarea, no solo la letra; evita self-fulfilling prophecies y cumplimiento literal vacío. |
| **Dependable** | No rechaza tareas; informa transparentemente si no las completa como esperado. |

> **Caveat crítico**: el framework asume que el interlocutor humano sabe lo que es correcto para sí mismo y para la humanidad. Por eso no incluye rasgos éticos, legales ni caritativos — son no-ortogonales y generan conflictos irresolubles en edge cases.

---

## Addenda relevantes (actualizaciones del autor)

### Vector anti-jailbreaking generalizado
Investigadores encontraron un único control vector para "jailbreaking" que mitiga múltiples técnicas de jailbreak simultáneamente. ¿Debería ser un trait "Good Boy" estándar?

### Apple — Activation Transport
Apple usa control vectors en producción con una variante llamada *Activation Transport*: la magnitud del vector se selecciona mediante **teoría de transporte óptimo** (mapeo entre distribuciones de probabilidad de una fuente y un objetivo). La magnitud se vuelve parcialmente interpretable.

### Anthropic — Persona Vectors (agosto 2025)  ← **actualización crítica**
Anthropic publicó un paper (arXiv 2507.21509) sobre **"persona vectors"** — exactamente la aplicación del concepto de rasgos disposicionales en un frontier model. Esto marca el paso de investigación activa a **deployment en producción** por parte de un laboratorio de primer nivel.

> "El concepto ha estado circulando en la comunidad por un tiempo, y me alegra que esté ganando terreno." — Calcagni (agosto 2025)

---

## Preguntas abiertas del artículo

1. **Linealidad**: ¿un rasgo como "alineado" es combinación lineal de los 12 traits listados? ¿Qué componentes linealmente independientes faltan?
2. **Destilación**: ¿se puede destilar un modelo "genuinamente good boy" usando un teacher con rasgos forzados vía control vectors?
3. **Lagrange multiplier**: ¿es posible incorporar la dirección de un trait directamente en la función de pérdida durante entrenamiento (estilo DPO)?

---

## Tensión con GCAV

| Dimensión | GCAV (Zhang et al.) | Calcagni |
|---|---|---|
| **Cálculo del vector** | Dirección normal del clasificador logístico | Dirección dominante vía PCA |
| **Objetivo** | Control de generación (toxicidad, sentimiento) | Alineación comportamental (12 traits) |
| **Validación** | Empírica (benchmarks) | Conceptual + comparación de vectores |
| **Safety claim** | No hace claims de safety | Explícitamente "no es true AI safety" |

Ambos convergen en el mismo mecanismo (modificar activaciones en inferencia) pero con framing distinto. Son complementarios.

---

## Conexiones

- [[gcav-paper]] — implementación complementaria (PCA vs. regresión logística; toxicidad/sentimiento vs. rasgos comportamentales)
- [[prompt-injection-architecture]] — Control Vectors como Solución #3; Calcagni añade la dimensión de alignment
- [[concepts/llm-security]] — framework de 12 traits como arquitectura de seguridad conductual
- [[concepts/agentic-design-patterns]] — los traits Anti-Optimizer son equivalentes a restricciones de agentes (LLM10 Unbounded Consumption, LLM06 Excessive Agency)
- [[concepts/prompt-injection]] — vector anti-jailbreaking generalizado como mitigación adicional
