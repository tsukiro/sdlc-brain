---
title: "Prompt Engineering Guide — Aplicaciones: Datos, Código y Case Study (DAIR-AI)"
type: source-summary
tags: [generating-data, synthetic-rag, code-generation, case-study, few-shot, cot, bias]
sources: []
updated: 2026-05-14
---

# Prompt Engineering Guide — Aplicaciones: Datos, Código y Case Study (DAIR-AI)

**Fuente:** https://www.promptingguide.ai/applications  
**Artículos procesados:** Generating Data, Generating Synthetic Dataset for RAG, Generating Code, Graduate Job Classification Case Study

---

## Generación de Datos Sintéticos

Los LLMs pueden generar datasets etiquetados para experimentos y evaluaciones, habilitando la creación de datos de entrenamiento sin anotación humana.

**Técnica básica:**
```
Prompt: "Produce 10 exemplars for sentiment analysis.
Examples are categorized as either positive or negative.
Produce 2 negative examples and 8 positive examples."
```

**Propiedad crítica:** La distribución de etiquetas en el prompt controla directamente la distribución del dataset generado. El LLM produce lenguaje natural coherente con la etiqueta solicitada.

**Aplicación práctica:** Bootstrappear un clasificador sin dataset humano anotado — puente entre prompting básico y ML operacional.

---

## Synthetic Dataset para RAG (Dai et al. 2022)

**Problema:** Los proyectos ML enfrentan cuellos de botella por escasez de datos etiquetados para sistemas de recuperación. LLMs permiten prototipado rápido antes de procesos formales de etiquetado.

### Técnica

1. Preparar **15-20 ejemplos manuales** de pares (documento, query relevante)
2. Seleccionar aleatoriamente **2-8 por prompt** (la aleatorización aumenta diversidad del dataset)
3. LLM genera queries sintéticas para cada nuevo documento del corpus

```
# Estructura del prompt
Instrucción específica de tarea de recuperación
+ k ejemplos (documento + query anotada)
+ Nuevo documento
→ LLM genera queries relevantes para el nuevo documento
```

### Resultados (Dai et al. 2022)

| Métrica | Valor |
|---------|-------|
| Ejemplos manuales requeridos | **8** |
| Costo para 50,000 documentos | **~$55** con ChatGPT |
| Potencial de mejora | Doble dígito en métricas en días |
| Rendimiento | Near-SOTA con solo 8 ejemplos manuales + documentos no etiquetados |

**Principio:** La calidad de los ejemplos manuales determina directamente la calidad del dataset sintético. Representatividad, buen formato y especificaciones de longitud objetivo son críticos.

**Variación por dominio:** Distintas tareas definen "relevancia" diferente (ej.: recuperación de argumentos puede buscar apoyo O contraargumento). El prompt y los ejemplos se adaptan por tarea.

---

## Generación de Código

**Modelo de referencia en el artículo:** `gpt-3.5-turbo`

### Casos de uso y patrones

| Caso | Patrón de prompt |
|------|-----------------|
| Generación básica | `"Write Python code that asks the user for their name and says 'Hello'"` |
| Comentario → código | Bloque de comentarios en Python → código ejecutable |
| Completado de función | `def multiply(` → LLM completa parámetros y lógica |
| Generación SQL | Schema de tablas + query en lenguaje natural → SQL con JOINs |
| Explicación de código | Código existente → explicación en lenguaje natural |

### Failure mode crítico documentado

**El modelo puede omitir imports necesarios.** Ejemplo: código generado que usa `json.loads()` sin incluir `import json`.

**Consecuencia práctica:** Siempre testear el código generado antes de usarlo en producción.

### Workflow validado para SQL

```
1. Definir schema de tablas en el prompt
2. Generar datos dummy para testing
3. Generar queries SQL sobre el schema
4. Probar en SQLiteOnline u otro intérprete
```

### Best practices

- Incluir schemas y constraints en prompts para código de base de datos
- Usar system messages para definir lenguaje preferido y formato de output
- Verificar outputs contra comportamiento esperado antes del deployment

---

## Case Study: Graduate Job Classification (Clavié et al. 2023)

**Paper:** arXiv:2303.07142  
**Tarea:** Clasificar si una oferta laboral es genuinamente adecuada para recién graduados (sistema de producción real).  
**Modelo principal:** `gpt-3.5-turbo` vs. DeBERTa-V3 (baseline fuerte).

### Resultados cuantitativos

| Configuración | F1 Score |
|---------------|----------|
| Baseline de clasificación | 65.6 |
| Zero-CoT baseline | 81.4 |
| Versión optimizada final | **91.7** |
| **Mejora total** | **+26.1 puntos F1** |

**Métricas finales:** 86.9% precision, 97% recall.

### Hallazgos contraintuitivos

**1. CoT few-shot peor que Zero-shot:**

> "Few-shot CoT prompting tuvo peor rendimiento que Zero-shot prompting en todos los experimentos."

Para tareas que **no requieren conocimiento experto** (la clasificación de ofertas laborales es factual, no razonamiento complejo), el CoT añade ruido en lugar de valor. El razonamiento explícito solo ayuda cuando hay razonamiento a hacer.

**Implicación crítica:** Desafía la aplicación universal de CoT. La técnica no es siempre mejor — depende del tipo de tarea.

**2. Los templates reducen rendimiento:**

Forzar compliance con templates de output redujo el rendimiento en todos los casos. Este comportamiento desapareció en tests con GPT-4.

**3. Las modificaciones simples tienen impacto acumulativo:**

Entre las 13 modificaciones probadas:
- Dar un **nombre al modelo**: +0.6 puntos F1
- Claridad y **repetición de instrucciones**: mayor impacto individual
- Los efectos se combinan de forma **acumulativa** (no promediada)

| Configuración | F1 |
|---|---|
| Zero-CoT baseline | 81.4 |
| + cada modificación acumulada | ... |
| Versión final optimizada | **91.7** |

---

## Tensión con conocimiento previo

El hallazgo de Clavié et al. crea una tensión documentable:

> [[concepts/planning]] establece que CoT mejora el razonamiento multi-paso. Clavié et al. 2023 muestra que CoT few-shot **empeora** el rendimiento en tareas de clasificación que no requieren razonamiento experto.

**Resolución:** CoT es una técnica de razonamiento, no una mejora universal. Solo aporta valor cuando la tarea requiere descomposición de razonamiento complejo. Para clasificación directa, el ruido del razonamiento explícito supera al beneficio.

---

## Conexiones

- [[concepts/rag]] — Synthetic RAG Dataset: técnica de augmentation con Dai et al. 2022
- [[concepts/prompt-engineering]] — Design tips aplicados y validados cuantitativamente en el case study
- [[concepts/planning]] — Tensión: CoT few-shot vs. Zero-shot en tareas de clasificación
- [[promptingguide-introduction]] — Prompt elements y tips que fundamentan el case study
