---
title: "Agentic Design Patterns — Diagramas Mermaid"
type: analysis
tags: [agentes, design-patterns, reflection, tool-use, planning, multi-agent, mermaid, diagramas]
sources: [2026-05-13_andrew-ng-agentic-patterns.md, 2026-05-13_lilian-weng-agents.md, 2026-05-13_reflexion-paper.md, 2026-05-13_langgraph.md, 2026-05-13_crewai.md]
updated: 2026-05-13
---

# Agentic Design Patterns — Diagramas Mermaid

> Visualización completa de los 4 patrones de Andrew Ng y su composición unificada.  
> Ver explicación textual en [[concepts/agentic-design-patterns]].

---

## Patrón 1 — Reflection

### Bucle básico de autorreflexión

```mermaid
flowchart TD
    A([Input]) --> B[LLM genera draft]
    B --> C["Autoevaluación<br/>¿Qué está mal? ¿Qué mejoraría?"]
    C --> D[LLM genera versión mejorada]
    D --> E{¿N iteraciones<br/>alcanzadas?}
    E -->|No| C
    E -->|Sí| F([Output final])

    W["⚠️ Confirmation Bias<br/>Mismo modelo: genera, evalúa y reflexiona<br/>Si razonó mal → reflexionará mal<br/>Solución → MAR: evaluador ≠ actor"]
    C -.-> W

    style W fill:#fff3cd,stroke:#f0ad4e
    style C fill:#e8d5f0,stroke:#9b59b6
```

### Variante: Reflexion — Shinn et al. (91% HumanEval)

```mermaid
flowchart TD
    A([Tarea]) --> B["Intento N"]
    B --> H{"¿Heurística de<br/>falla detectada?"}
    H -->|"Trayectoria demasiado larga<br/>o acciones repetidas"| R["Genera reflexión verbal<br/>'Fallé porque X.<br/>En próximo intento haré Y'"]
    H -->|Éxito| Z([Output final])
    R --> M[("Memoria de<br/>reflexiones")]
    M --> B2["Intento N+1<br/>+ contexto de reflexiones guardadas"]
    B2 --> H

    style M fill:#d5e8d4,stroke:#27ae60
    style R fill:#dae8fc,stroke:#2980b9
    style Z fill:#d5e8d4,stroke:#27ae60
```

---

## Patrón 2 — Tool Use

```mermaid
flowchart LR
    LLM["LLM<br/>(controlador)"] -->|"Function Call<br/>llamada estructurada"| D

    subgraph D["Herramientas disponibles"]
        T1["Búsqueda web<br/>supera corte de conocimiento"]
        T2["Ejecución de código<br/>cálculos precisos"]
        T3["APIs externas<br/>actuar en el mundo"]
        T4["RAG / Vector DB<br/>documentación propia"]
    end

    D -->|Resultado| LLM
    LLM --> O([Output final])

    style T1 fill:#dae8fc,stroke:#2980b9
    style T2 fill:#dae8fc,stroke:#2980b9
    style T3 fill:#dae8fc,stroke:#2980b9
    style T4 fill:#dae8fc,stroke:#2980b9
```

---

## Patrón 3 — Planning

### Chain of Thought (CoT) — razonamiento lineal

```mermaid
flowchart LR
    I([Input]) --> S1["Paso 1<br/>razona..."]
    S1 --> S2["Paso 2<br/>razona..."]
    S2 --> S3["Paso 3<br/>razona..."]
    S3 --> O([Output])

    L["'Think step by step'<br/>Base de todas las técnicas<br/>⚠️ Un solo camino: si falla, no hay recuperación"]
    style L fill:#fff3cd,stroke:#f0ad4e
```

### ReAct — el patrón default en producción

```mermaid
flowchart TD
    I([Input]) --> T1["Thought<br/>Necesito saber el precio de X"]
    T1 --> A1["Action<br/>search('precio X 2026')"]
    A1 --> O1["Observation<br/>El precio es $Y"]
    O1 --> T2["Thought<br/>Con $Y puedo calcular el margen..."]
    T2 --> A2["Action<br/>calculate(Y × 1.2)"]
    A2 --> O2["Observation<br/>Resultado = $Z"]
    O2 --> CHK{¿Respuesta<br/>suficiente?}
    CHK -->|Sí| ANS([Answer: $Z])
    CHK -->|No| T3["Thought<br/>Necesito más datos..."]
    T3 --> An["Action..."]
    An --> On["Observation..."]
    On --> CHK

    style T1 fill:#e8d5f0,stroke:#9b59b6
    style T2 fill:#e8d5f0,stroke:#9b59b6
    style T3 fill:#e8d5f0,stroke:#9b59b6
    style A1 fill:#dae8fc,stroke:#2980b9
    style A2 fill:#dae8fc,stroke:#2980b9
    style An fill:#dae8fc,stroke:#2980b9
    style O1 fill:#d5e8d4,stroke:#27ae60
    style O2 fill:#d5e8d4,stroke:#27ae60
    style On fill:#d5e8d4,stroke:#27ae60
```

### Tree of Thoughts (ToT) — exploración de múltiples caminos

```mermaid
flowchart TD
    P([Problema]) --> A[Razonamiento A]
    P --> B[Razonamiento B]

    A --> A1["A1 ✓"]
    A --> A2["A2 ✗"]
    B --> B1["B1 ✓"]
    B --> B2["B2 ✗"]

    A1 --> A1a["A1a ✓<br/>explorar"]
    A2 --> A2a["A2a ✗<br/>descartar"]
    B1 --> B1a["B1a ✓<br/>explorar"]
    B2 --> B2a["B2a ✗<br/>descartar"]

    A1a --> Best([Mejor solución])
    B1a --> Best

    style A2 fill:#f8cecc,stroke:#c0392b
    style B2 fill:#f8cecc,stroke:#c0392b
    style A2a fill:#f8cecc,stroke:#c0392b
    style B2a fill:#f8cecc,stroke:#c0392b
    style A1a fill:#d5e8d4,stroke:#27ae60
    style B1a fill:#d5e8d4,stroke:#27ae60
    style Best fill:#d5e8d4,stroke:#27ae60
```

### Árbol de decisión: ¿qué técnica de Planning usar?

```mermaid
flowchart TD
    Start([¿Qué técnica de Planning?]) --> Q1{¿La tarea requiere<br/>herramientas externas?}

    Q1 -->|Sí| ReAct["ReAct<br/>Thought → Action → Obs<br/>Default en producción"]
    Q1 -->|No| Q2{¿Espacio de solución<br/>amplio / creativo?}

    Q2 -->|Sí| ToT["Tree of Thoughts<br/>BFS/DFS sobre ramas<br/>⚠️ Costo computacional alto"]
    Q2 -->|No| Q3{¿Múltiples intentos<br/>con feedback entre ellos?}

    Q3 -->|Sí| Ref["Reflexion<br/>Verbal RL + memoria persistente<br/>91% HumanEval"]
    Q3 -->|No| CoT["Chain of Thought<br/>'Think step by step'<br/>Base — siempre aplicable"]

    Q1 -->|Planificación formal<br/>con restricciones estrictas| LLMP["LLM+P<br/>Motor PDDL externo<br/>Solo problemas formalizables"]

    style ReAct fill:#d5e8d4,stroke:#27ae60
    style Ref fill:#d5e8d4,stroke:#27ae60
    style CoT fill:#dae8fc,stroke:#2980b9
    style ToT fill:#fff3cd,stroke:#f0ad4e
    style LLMP fill:#f8cecc,stroke:#c0392b
```

---

## Patrón 4 — Multi-Agent

```mermaid
flowchart TD
    subgraph H["Topología 1: Supervisión Jerárquica"]
        Sup[Supervisor] --> AgA["Agente A<br/>Investigador"]
        Sup --> AgB["Agente B<br/>Redactor"]
        Sup --> AgC["Agente C<br/>Crítico"]
    end

    subgraph Pe["Topología 2: Colaboración entre Pares"]
        P1["Agente 1"] <--> P2["Agente 2"]
        P2 <--> P3["Agente 3"]
        P1 <--> P3
    end

    subgraph Pi["Topología 3: Pipeline Secuencial"]
        S1["Agente 1<br/>Recopila"] --> S2["Agente 2<br/>Procesa"]
        S2 --> S3["Agente 3<br/>Genera"]
        S3 --> Out([Output])
    end

    style Sup fill:#e8d5f0,stroke:#9b59b6
    style P1 fill:#dae8fc,stroke:#2980b9
    style P2 fill:#dae8fc,stroke:#2980b9
    style P3 fill:#dae8fc,stroke:#2980b9
```

**Ventajas sobre Reflection solo:**
- **Especialización**: cada agente optimizado para su rol
- **Paralelismo**: tareas independientes corren simultáneamente
- **Verificación cruzada**: elimina el confirmation bias del Patrón 1

---

## Diagrama Unificado — Composición de los 4 patrones

```mermaid
flowchart TD
    Input(["🎯 Tarea compleja<br/>GPT-3.5 zero-shot: 48.1%<br/>GPT-3.5 + agente: 95.1%"]) --> Orch

    subgraph MA["PATRÓN 4: Multi-Agent — escala horizontal"]

        Orch["Orquestador / Supervisor<br/>LangGraph o CrewAI"]

        subgraph AgA["Agente A — Especialista"]
            PA["PATRÓN 3: Planning<br/>ReAct / CoT / ToT<br/>Descompone la tarea"]
            RA["PATRÓN 1: Reflection<br/>Autoevalúa cada paso"]
            TA["PATRÓN 2: Tool Use<br/>Búsqueda · RAG · Código · APIs"]
            PA --> RA
            PA --> TA
            RA -->|"mejora iterativa"| PA
        end

        subgraph AgB["Agente B — Crítico / MAR"]
            PB["PATRÓN 3: Planning<br/>Evaluación de trayectoria"]
            RB["PATRÓN 1: Reflection<br/>Evaluación cruzada<br/>resuelve confirmation bias"]
            TB["PATRÓN 2: Tool Use<br/>Herramientas de validación"]
            PB --> RB
            PB --> TB
        end

        Orch --> AgA
        Orch --> AgB
        AgA <-->|"Verificación cruzada"| AgB
        AgB -->|"Feedback al orquestador"| Orch
    end

    MA --> Output(["✅ Output final"])

    style Input fill:#fff3cd,stroke:#f0ad4e
    style Orch fill:#e8d5f0,stroke:#9b59b6
    style PA fill:#dae8fc,stroke:#2980b9
    style PB fill:#dae8fc,stroke:#2980b9
    style RA fill:#e8d5f0,stroke:#9b59b6
    style RB fill:#e8d5f0,stroke:#9b59b6
    style TA fill:#d5e8d4,stroke:#27ae60
    style TB fill:#d5e8d4,stroke:#27ae60
    style Output fill:#d5e8d4,stroke:#27ae60
```

---

## Regla de composición

```mermaid
flowchart LR
    P4["Patrón 4<br/>Multi-Agent<br/>escala horizontal"] -->|"cada agente contiene"| P3
    P3["Patrón 3<br/>Planning<br/>secuencia de pasos"] -->|"apoya en"| P1
    P3 -->|"apoya en"| P2
    P1["Patrón 1<br/>Reflection<br/>evalúa cada paso"]
    P2["Patrón 2<br/>Tool Use<br/>extiende capacidades"]

    style P4 fill:#e8d5f0,stroke:#9b59b6
    style P3 fill:#dae8fc,stroke:#2980b9
    style P1 fill:#fff3cd,stroke:#f0ad4e
    style P2 fill:#d5e8d4,stroke:#27ae60
```

**Orden de adopción recomendado:**
1. **Reflection** — más barato, mayor ROI inicial
2. **Tool Use** — cuando se necesitan datos externos
3. **Planning/ReAct** — cuando la tarea es multi-step con herramientas
4. **Multi-Agent** — cuando la especialización o el paralelismo lo justifican

---

## Conexiones

- [[concepts/agentic-design-patterns]] — explicación textual completa de los 4 patrones
- [[concepts/planning]] — detalle de cada técnica de planning
- [[concepts/llm-agents]] — arquitectura base: Planning + Memory + Tools (Weng)
- [[reflexion-paper]] — fuente del 91% HumanEval con Reflexion
- [[concepts/multi-agent-frameworks]] — LangGraph vs CrewAI implementando Multi-Agent
- [[analyses/sdlc-ia-flujo-recomendado]] — aplicación de los patrones a cada fase del SDLC
