# Lilian Weng — LLM Powered Autonomous Agents

**URL:** https://lilianweng.github.io/posts/2023-06-23-agent/  
**Fecha de captura:** 2026-05-13  
**Autora:** Lilian Weng (OpenAI)

---

## Arquitectura general

Un agente LLM autónomo = LLM como cerebro + tres componentes:
1. **Planning** (Planificación)
2. **Memory** (Memoria)
3. **Tool Use** (Uso de herramientas / acción)

---

## Componente 1: Planning

### Chain of Thought (CoT) — Wei et al. 2022
"Think step by step". Transforma tareas complejas en pasos manejables. Técnica de prompting estándar.

### Tree of Thoughts (ToT) — Yao et al. 2023
Extensión de CoT: explora múltiples posibilidades de razonamiento en cada paso (árbol, no cadena). Búsqueda BFS o DFS, estados evaluados por clasificadores o votación mayoritaria.

### LLM+P — Liu et al. 2023
Usa planificadores externos mediante PDDL (Planning Domain Definition Language). LLM traduce problema → PDDL → planificador clásico → plan → lenguaje natural.

### ReAct — Yao et al. 2023
Integra razonamiento + acción en ciclo:
```
Thought: [razonamiento]
Action: [acción a tomar]
Observation: [resultado de la acción]
→ repeat
```

### Reflexion — Shinn & Labash 2023
"Capacidades dinámicas de memoria y autorreflexión". El agente detecta trayectorias fallidas mediante heurísticas:
- Planificación ineficiente: trayectorias largas sin éxito
- Alucinación: secuencias de acciones idénticas repetidas
Genera reflexión via few-shot: (trayectoria fallida, reflexión ideal).

### Chain of Hindsight (CoH) — Liu et al. 2023
Presenta secuencias de salidas pasadas anotadas con feedback humano. El modelo mejora condicionado en el histórico de intentos.

### Algorithm Distillation (AD) — Laskin et al. 2023
Aplica principios similares a trayectorias de RL; encapsula algoritmos de aprendizaje en políticas condicionadas por histórico largo.

---

## Componente 2: Memory

### Analogía con la memoria humana

| Tipo | Humano | En agentes LLM |
|------|--------|----------------|
| Sensorial | Impresiones visuales/auditivas (segundos) | Embeddings de input raw |
| Corto plazo (STM/Working) | ~7 items, 20-30 seg (Miller 1956) | In-context learning (ventana del Transformer) |
| Largo plazo (LTM) | Días a décadas, ilimitado | Base de datos vectorial externa |

### Subtipos de LTM
- **Explícita/Declarativa**: hechos (semántica) y eventos (episódica)
- **Implícita/Procedural**: habilidades automáticas

### Implementación: MIPS (Maximum Inner Product Search)
Algoritmos ANN para recuperación eficiente:
- **LSH**: hash functions que mapean similares al mismo bucket
- **ANNOY**: árboles de proyección aleatoria (múltiples árboles para mejor precisión)
- **HNSW**: redes de pequeño mundo jerárquicas, capas que crean atajos
- **FAISS**: cuantización vectorial con clusters (asume distribución gaussiana)
- **ScaNN**: cuantización vectorial anisotrópica, optimiza similaridad de producto interno

---

## Componente 3: Tool Use

### MRKL — Karpas et al. 2022
Modular Reasoning, Knowledge and Language. Arquitectura neuro-simbólica con módulos "expertos" (redes neuronales o herramientas simbólicas como calculadoras). LLM actúa como router.

### TALM + Toolformer — Parisi/Schick 2022/2023
Fine-tuning para aprender uso de APIs. Toolformer expande datasets basado en si las llamadas de API mejoran calidad.

### Implementaciones prácticas
- ChatGPT Plugins, OpenAI Function Calling

### HuggingGPT — Shen et al. 2023
ChatGPT como planificador que selecciona modelos HuggingFace. 4 etapas:
1. Task Planning: LLM parsea solicitudes en tareas con tipo, ID, dependencias, argumentos
2. Model Selection: LLM distribuye tareas a modelos expertos
3. Task Execution: modelos expertos ejecutan
4. Response Generation: LLM sintetiza resultados

### API-Bank — Li et al. 2023
Benchmark con 53 APIs comunes. 3 niveles de evaluación:
1. Llamar API correctamente
2. Recuperar la API apropiada
3. Planificar múltiples llamadas

---

## Casos de uso documentados

### ChemCrow (Bran et al. 2023)
13 herramientas para química: síntesis orgánica, fármacos, materiales. En LangChain. Hallazgo: evaluación humana mostró superioridad sobre GPT-4; evaluación LLM mostró equivalencia → problema de autoevaluación en dominios especializados.

### Generative Agents (Park et al. 2023)
25 agentes virtuales en sandbox tipo Sims. Arquitectura:
- **Memory Stream**: DB externa de experiencias en lenguaje natural
- **Retrieval**: basado en Recency + Importance + Relevance
- **Reflection**: 100 observaciones recientes → 3 preguntas salientes → respuestas
- **Planning**: reflexiones + info ambiental → acciones
Comportamientos emergentes: difusión de información, coordinación social, memoria relacional.

### AutoGPT
20 comandos, formato JSON, metas del usuario hasta 5. Demuestra agencia autónoma; limitado por confiabilidad de interfaz natural.

### GPT-Engineer
Genera repositorios completos desde especificaciones en lenguaje natural. Fase de clarificación inicial antes de generación.

---

## Desafíos identificados

1. **Longitud finita de contexto**: limita historial, instrucciones, contexto de APIs. Vectorstores ayudan pero son inferiores a full-attention.
2. **Planificación largo plazo**: LLMs luchan por ajustar planes ante errores inesperados; menos robustos que humanos en prueba y error.
3. **Confiabilidad de interfaz natural**: errores de formato, comportamiento "rebelde" ocasional; parseo de salidas requiere esfuerzo sustancial.
