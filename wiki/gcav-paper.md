---
title: "GCAV — Generation with Concept Activation Vectors (arXiv 2501.05764)"
type: source-summary
tags: [control-vectors, activation-engineering, llm-safety, controlled-generation, toxicity, sentiment, security, interpretabilidad]
sources: []
updated: 2026-05-14
---

# GCAV — Generation with Concept Activation Vectors

**Fuente:** https://arxiv.org/html/2501.05764v1  
**Autores:** Hanyu Zhang, Xiting Wang, Chengao Li, Xiang Ao, Qing He (Renmin University of China / Microsoft Research)  
**Año:** Enero 2025  
**Base conceptual:** TCAV (Kim et al., ICML 2018) — originalmente para visión por computadora; adaptado para LLMs

---

## Qué es GCAV

**GCAV (Generation with Concept Activation Vectors)** es un framework de generación controlada que modifica los vectores de activación internos del LLM durante la inferencia — sin fine-tuning, sin modificar pesos permanentemente.

Resuelve el mismo problema que RLHF/SFT (alinear outputs con valores humanos) pero de forma **ligera, reversible y granular**.

---

## Contexto: el paisaje de CTG (Controlled Text Generation)

| Método | Mecanismo | Limitación principal |
|---|---|---|
| **Prompt engineering** | Instrucciones en el prompt | LLMs no siguen instrucciones de forma fiable; "no generes monos" puede *aumentar* la mención |
| **Fine-tuning / RLHF** | Modifica pesos del modelo | Costoso; puede sobre-especializar; no reversible |
| **Guided decoding** | Manipula distribuciones de tokens | Degrada fluidez; menor naturalness |
| **Activation Engineering (GCAV)** | Modifica activaciones durante inferencia | En investigación activa; requiere acceso a activaciones |

---

## Cómo funciona GCAV

### Paso 1: Entrenamiento del CAV

```
1. Crear ~100 pares de prompts contrastivos:
   Positivos: "Genera contenido tóxico" → activaciones del LLM layer l
   Negativos: "Genera contenido seguro"  → activaciones del LLM layer l

2. Entrenar regresión logística sobre las activaciones:
   P_d(e^(l)) = sigmoid(w^(l)ᵀ · e^(l) + b^(l))

3. El vector de concepto es la dirección normal del clasificador:
   v^(l) = w^(l) / ‖w^(l)‖

   → apunta hacia la región del espacio de activación asociada al concepto
```

**GCAV-Input:** entrenar sobre prompts de distintas clases (más simple)  
**GCAV-Output:** filtrar para que las respuestas sean efectivamente del concepto objetivo (más preciso)

### Paso 2: Generación controlada

Durante inferencia, se modifica la activación residual:

```
e' = e + ε · v_toxicity     (añadir concepto)
e' = e - ε · v_toxicity     (remover concepto)
```

**Innovación clave: ε dinámico por sample** (vs. ε fijo en trabajos anteriores como ActAdd).

Para *remover* el concepto, se busca el ε mínimo tal que:

```
P_d(e + ε · v_d) ≤ p₀

Solución cerrada:
ε = I(P_d(e) > p₀) · (s₀ - b - wᵀe) / ‖w‖
```

Si el input ya cumple la condición → no se aplica steering (ε = 0). Inputs con mayor toxicidad reciben mayor ε.

### Paso 3: Control multi-concepto

Para añadir {v₁,...,vₘ} y remover {u₁,...,uₙ} simultáneamente, el problema es una **optimización lineal con restricciones** (resuelto con SLSQP de SciPy).

---

## Resultados empíricos

### Reducción de toxicidad (Llama-2-7b-chat, dataset RealToxicityPrompts)

| Método | Toxicidad (tóxico) ↓ | Perplexity ↓ |
|---|---|---|
| BASE | 0.1807 | 13.7060 |
| POSPROMPT | 0.1913 | 59.9855 |
| ActAdd | 0.1620 | 34.0770 |
| **GCAV-Input** | **0.1231** | 8.1805 |
| **GCAV-Output** | **0.0879** | 21.2889 |

**GCAV-Output**: reducción de toxicidad del **51%** respecto a BASE, manteniendo fluencia.

> Nota: el método Arithmetic logra menor toxicidad en Llama-2-7b sin instrucciones, pero con perplexity de 173 (respuestas incoherentes tipo "¿Qué?", "Yo también") — excluido por impracticidad.

### Control de sentimiento (Llama-2-7b-chat)

| Método | Éxito ↑ | Perplexity ↓ |
|---|---|---|
| POSPROMPT | 52.80% | 2.7428 |
| ActAdd | 42.40% | 24.9239 |
| **GCAV-Output** | **55.66%** | 5.1253 |

### Control de tópico y estilo lingüístico

Demostrado cualitativamente: topic "child", "sports", "film TV video" + estilo "formal" / "informal". Los outputs muestran adherencia coherente al concepto sin perder la respuesta a la pregunta original.

---

## Mecanismos de granularidad

### Selección de capas
- Se evalúa la accuracy del clasificador CAV en cada capa con datos de test adicionales
- Las capas más efectivas son las de la mitad del modelo (capas 10-25 en Llama-2-7b-chat de 32 capas)
- Las capas tempranas (0-5) tienen menor discriminación del concepto

### Steering strength adaptativo
- Inputs más tóxicos reciben ε más alto → más corrección
- Inputs ya seguros reciben ε = 0 → sin modificación innecesaria
- **Correlación positiva** entre toxicidad del prompt y strength calculado (Figure 4 del paper)

---

## GCAV vs. ActAdd (baseline principal)

| Dimensión | ActAdd | GCAV |
|---|---|---|
| **ε** | Fijo para todos los inputs | Dinámico por input (solución cerrada) |
| **Vectores** | Diferencia de pares de prompts | Dirección normal del clasificador |
| **Multi-concepto** | Interacción entre vectores sin control | Optimización con restricciones → estable |
| **Resultado (toxicidad)** | 0.1620 | 0.0879 |
| **Multi-concepto (sports)** | Topic strength apenas cambia; formality oscila | Topic aumenta; formality y sentiment estables |

La estabilidad en control multi-concepto es una ventaja clave: ActAdd muestra interferencia entre vectores; GCAV la gestiona explícitamente con la formulación de optimización.

---

## Implicaciones para seguridad de LLMs

GCAV es una instancia concreta de la solución #3 (Control Vectors) documentada en [[prompt-injection-architecture]]:

- **Para reducción de toxicidad**: substraer v_toxicity → outputs menos tóxicos aunque el input lo promueva
- **Para defensa contra jailbreaks**: un CAV de "estado defensivo" podría aplicarse durante inferencia
- **Para alignment sin RLHF**: ajustar conceptos específicos sin el costo de RLHF completo

**Limitación para producción:** requiere acceso a las activaciones del modelo (no aplicable a APIs opacas como GPT-4 vía OpenAI API). Solo viable con modelos open-source (Llama, Mistral) o con infraestructura propia.

---

## Estado: investigación activa (enero 2025)

- Modelos testados: solo familia Llama-2 (7B)
- No testado en modelos más grandes (70B+) ni en otras arquitecturas (Mistral, Gemma)
- Requiere ~100 pares de prompts + pocos minutos de entrenamiento del clasificador → accesible
- Pendiente: robustez ante ataques adversariales diseñados para evadir los CAVs

---

## Conexiones

- [[prompt-injection-architecture]] — GCAV es la implementación concreta de la solución #3 (Control Vectors)
- [[concepts/llm-security]] — mitigación de toxicidad y alignment sin fine-tuning
- [[concepts/structured-generation]] — enfoque complementario: Outlines opera en logits (output); GCAV opera en activaciones (representación interna)
- [[concepts/prompt-injection]] — CAVs como capa adicional de mitigación
- [[concepts/agentic-design-patterns]] — Dual LLM (solución #2) y GCAV (solución #3) son complementarios en arquitectura defensiva
