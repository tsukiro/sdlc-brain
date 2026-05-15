---
title: "Prompt Engineering Guide — Técnicas (DAIR-AI)"
type: source-summary
tags: [prompt-engineering, zero-shot, few-shot, cot, self-consistency, generated-knowledge, tot, rag, art, chain-of-thought, ape, pal, active-prompt, multimodal-cot, react, reflexion]
sources: []
updated: 2026-05-14
---

# Prompt Engineering Guide — Técnicas (DAIR-AI)

**Fuente:** https://www.promptingguide.ai/techniques  
**Organización:** DAIR-AI (Democratizing AI Research)  
**Procesado:** 17 artículos individuales (2 ingests, 2026-05-14)  
**Artículos sin contenido procesable:** Prompt Chaining (placeholder de traducción)

---

## 1. Zero-shot Prompting

El modelo responde directamente a la tarea sin ningún ejemplo previo.

**Por qué funciona:** La **instruction tuning** y el **RLHF** permiten que los LLMs modernos generalicen a tareas no vistas. Zero-shot no existía en GPT-2 — es resultado del entrenamiento post-pretraining moderno.

**Limitación:** Falla en tareas de razonamiento multi-paso complejo o knowledge de dominio estrecho.

---

## 2. Few-shot Prompting

**Origen principal:** Brown et al. 2020 (GPT-3) — demostró el **in-context learning (ICL)** a escala.

### Hallazgo clave: el rol de las etiquetas (Min et al. 2022)

**Los modelos mantienen gran parte de su performance con etiquetas aleatorias en los ejemplos few-shot.**

Lo que el modelo aprende del few-shot: el espacio de etiquetas, la distribución de inputs, el formato de la respuesta. El conocimiento de "qué etiqueta va con qué input" viene del **pretraining**, no del few-shot.

**Implicación:** El formato importa más que la "corrección" de las etiquetas.

---

## 3. Chain of Thought (CoT)

**Orígenes duales:**
- **Few-shot CoT** (Wei et al. 2022): ejemplos de razonamiento paso a paso en el prompt
- **Zero-shot CoT** (Kojima et al. 2022): simplemente añadir **"Let's think step by step"** — efectivo sin ejemplos

Ver técnicas completas en [[concepts/planning]].

---

## 4. Self-Consistency

**Origen:** Wang et al. 2022 — "Self-Consistency Improves Chain of Thought Reasoning in Language Models"

Reemplaza la decodificación greedy por votación mayoritaria sobre múltiples CoT paths independientes:

```
Query → CoT path 1 → Respuesta A
      → CoT path 2 → Respuesta A   ← votación → A (final)
      → CoT path 3 → Respuesta B
      → CoT path 4 → Respuesta A
```

**Resultado empírico:** +10–20 puntos sobre CoT greedy en GSM8K y benchmarks lógicos. Costo: N llamadas al LLM.

---

## 5. Generated Knowledge Prompting

**Origen:** Liu et al. 2022 — "Generated Knowledge Prompting for Commonsense Reasoning"

Dos fases: (1) LLM genera conocimiento relevante → (2) LLM usa ese conocimiento para predecir.

**Diferencia con RAG:** El conocimiento lo genera el mismo LLM, no se recupera de fuente externa.

---

## 6. Prompt Chaining

*(Artículo original sin contenido procesable — placeholder de traducción. La Sección 20 "Prompt Functions" documenta la implementación práctica del patrón de encadenamiento.)*

---

## 7. Tree of Thoughts (ToT)

**Orígenes:**
- **Yao et al. 2023** — BFS/DFS con evaluación de estados (`sure / maybe / impossible`)
- **Long 2023** — ToT Controller entrenado por RL

**Prompt mínimo** (sin implementación de árbol completa):
```
Imagina que tres expertos diferentes están respondiendo a esta pregunta.
Cada experto escribe 1 paso de su razonamiento y lo comparte con el grupo.
Si algún experto se equivoca, ese experto se va. La pregunta es: [PREGUNTA]
```

---

## 8. RAG como técnica de prompting

**Origen:** Lewis et al. 2021 — "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks" (NeurIPS 2021)

**Modelo conceptual:** memoria paramétrica (pesos del LLM) + memoria no-paramétrica (índice vectorial denso).

El paper original usó DPR sobre Wikipedia completa. Esta separación sigue siendo el modelo mental correcto para RAG.

Ver pipeline completo en [[concepts/rag]].

---

## 9. ART — Automatic Reasoning and Tool use

**Origen:** Paranjape et al. 2023 — "ART: Automatic multi-step reasoning and tool-use for large language models"

CoT + tool calls en LLM congelado via una **Task Library**. Selecciona 2-shot ejemplos automáticamente, pausa la generación para tool calls, reanuda con los resultados integrados.

**Extensión clave:** ART funciona en **zero-shot** para nuevas tareas (generaliza desde la Task Library). Supera a few-shot CoT y CoT automático en BigBench y MMLU. **Excede a prompts CoT artesanales cuando se incorpora feedback humano** a la task library.

---

## 10. APE — Automatic Prompt Engineer

**Origen:** Zhou et al. 2022 — "Large Language Models are Human-Level Prompt Engineers"

**Problema:** El prompt engineering manual es costoso e inconsistente. APE lo automatiza via black-box optimization.

**Mecanismo:**
1. Un LLM recibe output demonstrations → genera candidatos de instrucción
2. Las instrucciones se ejecutan en un target model
3. Se selecciona la instrucción con mejor evaluation score

**Hallazgo crítico:** APE descubrió un zero-shot CoT mejor que el humano "Let's think step by step":

> "Let's work this out in a step by step way to be sure we have the right answer."

Esta variante supera al CoT original de Kojima et al. en **MultiArith** y **GSM8K**.

**Implicación de fondo:** El espacio de prompts es vasto; los prompts óptimos no son necesariamente los que los humanos escribirían intuitivamente. La búsqueda automática descubre mejores soluciones.

**Técnicas relacionadas de optimización:**

| Técnica | Método | Ref |
|---------|--------|-----|
| OPRO | LLM optimiza sus propios prompts iterativamente; "Take a deep breath" mejora en matemáticas | 2023 |
| AutoPrompt | Gradient-guided search para crear prompts | 2020 |
| Prefix Tuning | Prefijo continuo entrenable; alternativa ligera a fine-tuning | 2021 |
| Prompt Tuning | Soft prompts aprendibles via backpropagation | 2021 |

---

## 11. Active-Prompt

**Origen:** Diao et al. 2023 — "Active Prompting with Chain-of-Thought for Large Language Models"

**Problema:** Los ejemplos CoT fijos pueden no ser los más efectivos para cada tarea.

**Mecanismo (adaptación activa):**
1. LLM genera k respuestas para un conjunto de preguntas de entrenamiento
2. Se calcula una métrica de **incertidumbre** (desacuerdo entre las k respuestas)
3. Las preguntas con mayor incertidumbre se seleccionan para **anotación humana**
4. Los nuevos ejemplos anotados se usan como demostraciones CoT para inferencia

**Principio:** Maximizar el valor informativo de la anotación humana seleccionando los casos donde el modelo es más incierto.

---

## 12. DSP — Directional Stimulus Prompting

**Origen:** Li et al. 2023 — "Guiding Large Language Models via Directional Stimulus Prompting"

Un **policy LM pequeño y entrenable** (vía RL) genera hints/estímulos que guían a un LLM grande congelado hacia el output deseado. Permite optimizar el comportamiento del sistema sin tocar el LLM grande.

---

## 13. PAL — Program-Aided Language Models

**Origen:** Gao et al. 2022 — "PAL: Program-Aided Language Models"

**Idea central:** En lugar de razonar en texto libre (CoT), el LLM genera **código Python** como pasos de razonamiento intermedio. La ejecución es delegada a un intérprete Python determinista.

```
Pregunta en lenguaje natural
    ↓
LLM genera código Python (razonamiento como programa)
    ↓
Intérprete Python ejecuta → resultado determinista y correcto
```

**Ejemplo concreto:**
```python
# Q: Nací exactamente hace 25 años. Hoy es 27 Feb 2023. ¿Cuándo nací?
today = datetime(2023, 2, 27)
born = today - relativedelta(years=25)
born.strftime('%m/%d/%Y')  # → 02/27/1998
```

**Diferencia clave con CoT:** CoT delega el razonamiento Y el cálculo al LLM → puede "calcular" mal. PAL separa razonamiento (LLM, semántico) de ejecución (intérprete, determinista) → garantía de correctitud en la ejecución.

**Extensibilidad:** El "símbolo de razonamiento" puede ser cualquier runtime: Python, Wolfram Alpha, SQL.

**Cuándo:** Aritmética precisa, manipulación de fechas, razonamiento lógico estructurado — cualquier tarea donde la ejecución determinista supera la "aritmética" del LLM.

---

## 14. ReAct — detalles y resultados

*(Técnica principal documentada en [[concepts/planning]])*

**Origen:** Yao et al. **2022** (arXiv:2210.03629) — nota: frecuentemente citado como 2023, pero el paper es de octubre 2022.

**Mejor combinación identificada en el paper:** **ReAct + CoT + Self-Consistency** supera a cada técnica por separado. La sinergia: CoT (razonamiento interno) + ReAct (información externa) + Self-Consistency (votación mayoritaria).

**Resultados en decision-making:**
- **ALFWorld** (navegación en entorno textual): ReAct supera a Act-only — el razonamiento explícito es clave para descomponer subobjetivos
- **WebShop** (compras online simuladas): ReAct supera a Act-only
- Los humanos expertos siguen superando a los métodos prompting-based en estas tareas

---

## 15. Reflexion — arquitectura de 3 componentes

*(Técnica principal en [[concepts/planning]] y [[reflexion-paper]])*

**Origen:** Shinn et al. 2023 — "Reflexion: Language Agents with Verbal Reinforcement Learning"

La arquitectura formal de Reflexion tiene **3 modelos separados**:

| Componente | Función | Implementación |
|---|---|---|
| **Actor** | Genera texto y acciones; opera en el entorno | CoT o ReAct |
| **Evaluator** | Puntúa las trayectorias del Actor (reward) | LLM judge o heurísticas rule-based |
| **Self-Reflection** | Genera feedback verbal desde reward + trayectoria + memoria | LLM; almacena en LTM |

**Flujo:**
```
Actor genera trayectoria
    ↓ Evaluator puntúa
    ↓ Self-Reflection genera feedback verbal + guarda en LTM
    ↓ Actor usa feedback en siguiente episodio → mejora iterativa
```

**Resultado clave (AlfWorld):** ReAct + Reflexion completa **130/134 tareas** — sustancial mejora sobre ReAct solo.

**Cuándo usar Reflexion:**
- Agente necesita aprender de ensayo y error
- RL tradicional es impracticable (no fine-tuning)
- Feedback debe ser matizado y específico (no solo escalar)
- Interpretabilidad y memoria explícita son importantes

---

## 16. Multimodal CoT

**Origen:** Zhang et al. 2023 — "Multimodal Chain-of-Thought Reasoning in Language Models"

Extiende CoT al dominio multimodal (texto + visión) con un framework de dos etapas:

```
Etapa 1 — Generación de rationale:
[texto + imagen] → LLM genera razonamiento basado en información multimodal

Etapa 2 — Inferencia de respuesta:
[rationale generado] + [pregunta] → LLM infiere la respuesta final
```

**Resultado:** Modelo de **1B parámetros** supera a GPT-3.5 en **ScienceQA** — la arquitectura importa más que el tamaño para razonamiento multimodal.

---

## 17. GraphPrompt

**Origen:** Liu et al. 2023 (arXiv:2302.08043) — contexto GNN, relevancia tangencial para LLMs.

Framework que unifica pre-training y downstream tasks en Graph Neural Networks mediante un template común + prompt entrenable para localización de conocimiento específico. Analogía del paradigma prompting de LLMs aplicada al dominio de grafos.

---

## Jerarquía de técnicas

```
Prompting foundation
├── Zero-shot (instruction tuning + RLHF)
└── Few-shot / ICL (Brown et al. 2020; format > labels: Min et al. 2022)
    └── CoT (razonamiento explícito)
        ├── Few-shot CoT (Wei et al. 2022)
        ├── Zero-shot CoT ("Let's think step by step" — Kojima et al. 2022)
        ├── Self-Consistency (múltiples CoT + votación — Wang et al. 2022)
        ├── Generated Knowledge (genera conocimiento antes — Liu et al. 2022)
        ├── Active-Prompt (CoT adaptativo por incertidumbre — Diao et al. 2023)
        └── ART (CoT + herramientas + Task Library — Paranjape et al. 2023)

PAL            (código como razonamiento — Gao et al. 2022)
APE            (optimización automática de prompts — Zhou et al. 2022)
ReAct          (razonamiento + acción interleaved — Yao et al. 2022)
Reflexion      (RL verbal, 3 componentes — Shinn et al. 2023)
ToT            (árbol multi-rama — Yao et al. 2023 + Long 2023)
Multimodal CoT (texto + visión, 2 etapas — Zhang et al. 2023)
RAG            (recuperación + generación — Lewis et al. 2021)
```

---

## Papers clave

| Técnica | Paper | Año |
|---------|-------|-----|
| RAG | Lewis et al. (NeurIPS) | 2021 |
| Few-shot / ICL | Brown et al. (GPT-3) | 2020 |
| CoT (few-shot) | Wei et al. | 2022 |
| CoT (zero-shot) | Kojima et al. | 2022 |
| Self-Consistency | Wang et al. | 2022 |
| Generated Knowledge | Liu et al. | 2022 |
| Label role in few-shot | Min et al. | 2022 |
| PAL | Gao et al. | 2022 |
| APE | Zhou et al. | 2022 |
| ReAct | Yao et al. | 2022 |
| ToT (BFS/DFS) | Yao et al. | 2023 |
| ToT (RL controller) | Long | 2023 |
| ART | Paranjape et al. | 2023 |
| Active-Prompt | Diao et al. | 2023 |
| Reflexion | Shinn et al. | 2023 |
| Multimodal CoT | Zhang et al. | 2023 |
| GraphPrompt (GNNs) | Liu et al. | 2023 |
| Function Calling | OpenAI GPT-4/3.5 fine-tuning | 2023 |
| Context Caching | Google Gemini API | 2024 |
| Prompt Functions | Meta prompt pattern (sin paper formal) | 2023 |

---

## 20. Prompt Functions

**Fuente:** https://www.promptingguide.ai/applications/pf

### El Meta Prompt

El patrón encapsula un prompt con nombre, input y reglas usando un **meta prompt** que enseña al LLM a interpretar "llamadas a función":

```text
function_name: [Nombre de la función]
input: [Input]
rule: [Instrucciones sobre cómo procesar el input]
```

Una vez registradas las funciones, se invocan con sintaxis funcional:

```text
trans_word("婆罗摩火山...")
fix_english(expand_word(trans_word("婆罗摩火山...")))
```

El LLM aplica las reglas de la función al input y devuelve el output. La **composición** sigue el modelo funcional estándar: evaluación de adentro hacia afuera.

### Ejemplos definidos en el artículo

| Función | Regla |
|---------|-------|
| `trans_word(text)` | Traducir al inglés y corregir ortografía |
| `expand_word(text)` | Expandir texto sin cambiar el significado; más literario |
| `fix_english(text)` | Mejorar vocabulario y naturalidad; mantener significado |
| `pg(length, capitalized, lowercase, numbers, special)` | Generar contraseña con los parámetros dados |

### Por qué importa

1. **Reutilización**: el meta prompt se define una vez; las funciones se invocan en cualquier chat
2. **Composabilidad**: `f(g(h(x)))` — encadenamiento funcional legible
3. **Accesible**: no requiere código Python ni framework — solo lenguaje natural estructurado

### Evolución histórica del concepto

```
Prompt Functions (2023)
    → formato manual: function_name / input / rule
    → sin versionado, sin discovery, sin persistencia entre sesiones

Skills (Gelin, 2024-2025) — [[gelin-skills-format]]
    → YAML frontmatter + markdown body + triggers
    → Progressive loading (3 niveles: metadata → instructions → resources)
    → Discovery automático (filesystem scanning)
    → Registry público (ClawHub)
    → Portable entre plataformas (OpenClaw, Claude Agent Skills, Cursor)
```

El mismo problema de reutilización de expertise → soluciones de complejidad creciente. Prompt Functions son el "npm casero"; Skills son el npm real con registry.

### Relación con Prompt Chaining

Prompt Functions demuestran Prompt Chaining en la práctica: cada función es un paso; la composición `fix_english(expand_word(trans_word(x)))` es un pipeline de 3 pasos donde el output de cada función alimenta la siguiente.

---

## 19. Context Caching

**Fuente:** https://www.promptingguide.ai/applications/context-caching  
**Contexto:** Funcionalidad de la Gemini API que cachea un contexto grande server-side, evitando re-enviarlo en cada query.

### Mecanismo

```python
cache = caching.CachedContent.create(
    model="gemini-1.5-flash-001",
    name="ml-papers-cache",
    system_instruction="Eres un experto investigador de IA...",
    contents=[text_file],
    ttl=datetime.timedelta(minutes=15)
)
model = genai.GenerativeModel.from_cached_content(cache)
```

El contenido se procesa una vez (KV states cacheados en el servidor). Las queries sucesivas reutilizan el cache sin re-enviar tokens. El TTL define la duración del cache.

**Caso de uso:** DAIR-AI lo usó para analizar un año de papers ML almacenados en texto plano — consultas con lenguaje natural sobre el corpus sin re-enviarlo en cada request.

### Posición en el paisaje de técnicas

| Técnica | Para qué sirve | Limitación |
|---------|---------------|-----------|
| Context Caching | Corpus estático, misma sesión, KV reuse | Time-limited (TTL); no sintetiza |
| LLM Wiki | Síntesis compilada; persistente entre sesiones | Requiere compilación previa |
| RAG | Corpus dinámico; solo partes relevantes | Retrieval imperfecto; pipeline complejo |

**Relación con LLM Wiki (Karpathy):** Ambos resuelven "carga estática + muchas queries". Context Caching opera a nivel de KV cache del servidor (dentro de una sesión, sin transformar el contenido); LLM Wiki sintetiza el conocimiento en markdown persistente y cross-referenced.

Ver comparativa detallada en [[entities/gemini]].

---

## 18. Function Calling

**Fuente:** https://www.promptingguide.ai/applications/function_calling  
**Contexto:** Capacidad de los LLMs modernos (GPT-4, GPT-3.5) fine-tuneados para invocar herramientas de forma estructurada.

### Mecanismo

Function calling no es prompting puro — es una capacidad emergente del fine-tuning. El LLM aprende a:
1. **Detectar** cuándo una query requiere una herramienta externa
2. **Emitir** un JSON estructurado con los argumentos necesarios para llamar a la función
3. La **aplicación** ejecuta la función real y devuelve el resultado al LLM
4. El LLM **sintetiza** la respuesta final con el resultado

**Distinción crítica:** El LLM no ejecuta la función — solo produce los argumentos. La ejecución es responsabilidad del código de la aplicación.

```
User query: "What is the weather like in London?"
    ↓
LLM detecta: necesita herramienta de clima
    ↓ emite JSON:
{"name": "get_current_weather", "arguments": {"location": "London", "unit": "celsius"}}
    ↓
Aplicación ejecuta get_current_weather(location="London", unit="celsius")
    ↓ resultado devuelto al LLM
LLM: "El tiempo en Londres es de 15°C con cielos nublados."
```

### Definición de función (JSON Schema)

```python
tools = [{
    "type": "function",
    "function": {
        "name": "get_current_weather",
        "description": "Get the current weather in a given location",
        "parameters": {
            "type": "object",
            "properties": {
                "location": {"type": "string", "description": "City and state, e.g. 'London, UK'"},
                "unit":     {"type": "string", "enum": ["celsius", "fahrenheit"]}
            },
            "required": ["location"]
        }
    }
}]
```

La `description` es crítica: el LLM la usa para decidir cuándo invocar la función. Funciona como un micro-prompt.

### Casos de uso

| Caso | Descripción |
|------|-------------|
| **Conversational agents** | Responder preguntas con datos en tiempo real (clima, bolsa, CRM) |
| **NLU / extracción** | Convertir lenguaje natural en JSON estructurado; NER, sentiment analysis |
| **Math problem solving** | Delegar cálculos complejos a funciones Python/Wolfram |
| **API integration** | Natural language → API calls válidas |
| **Knowledge retrieval** | Query reformulation para bases de conocimiento |

### Relación con otras técnicas

- **ART** ([[promptingguide-techniques]] sec. 9): formaliza CoT + tool calls con una Task Library; function calling es el mecanismo de bajo nivel que ART usa
- **ReAct** ([[concepts/planning]]): el ciclo Thought → Action → Observation implementa los "Actions" vía function calling
- **Structured Generation** ([[concepts/structured-generation]]): el JSON de función es structured output — Instructor/TypeChat pueden validar los argumentos antes de ejecutar
- **Tool Use pattern** ([[concepts/agentic-design-patterns]]): function calling es la implementación técnica del patrón de Ng

---

## Conexiones

- [[concepts/prompt-engineering]] — Zero-shot y Few-shot como fundamentos
- [[concepts/planning]] — CoT, Self-Consistency, Generated Knowledge, ToT, ART, PAL, APE, ReAct, Reflexion
- [[concepts/rag]] — Lewis et al. 2021 como origen del paradigma RAG
- [[lilian-weng-agents]] — ToT y ReAct desde perspectiva de agentes (Weng)
- [[andrew-ng-agentic-patterns]] — ART relacionado con el Tool Use pattern (Ng)
- [[reflexion-paper]] — Reflexion: arquitectura de 3 componentes, resultados AlfWorld, limitaciones
- [[entities/gemini]] — Context Caching: sección detallada con API, comparativa vs. RAG vs. LLM Wiki
- [[gelin-skills-format]] — Skills como evolución formal de Prompt Functions: YAML frontmatter + triggers + progressive loading + registry (ClawHub)
