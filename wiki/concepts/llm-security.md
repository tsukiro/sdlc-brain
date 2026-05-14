---
title: "Seguridad en Sistemas LLM y Agénticos"
type: concept
tags: [seguridad, llm, agentes, owasp, prompt-injection, vulnerabilidades, devsecops, computer-use, nemo-guardrails]
sources: [2026-05-13_owasp-llm-top10.md, 2026-05-13_llm-agents-vulnerabilities.md, 2026-05-13_computer-use-anthropic.md, 2026-05-13_owasp-llm01-prompt-injection.md, 2026-05-13_simon-willison-prompt-injection-2022.md, 2026-05-13_simon-willison-indirect-injection-2023.md, 2026-05-13_nemo-guardrails.md, 2026-05-13_learn-prompting-defensive-measures.md]
updated: 2026-05-13
---

# Seguridad en Sistemas LLM y Agénticos

## Por qué la seguridad de LLMs es una categoría nueva

Los sistemas LLM y agénticos introducen vectores de ataque que no existen en software tradicional:
1. El "input" del sistema puede ser texto libre en lenguaje natural — extremadamente difícil de sanitizar
2. Los agentes toman acciones autónomas con herramientas reales — el daño puede ser físico
3. Los LLMs más capaces pueden razonar sobre código y vulnerabilidades — son atacantes potenciales

> "GPT-4 explotó el 87% de 15 vulnerabilidades one-day de forma autónoma. Todos los demás modelos y herramientas tradicionales: 0%." — Fang et al.

---

## El OWASP LLM Top 10 (2025)

Marco de referencia de la industria. Las vulnerabilidades más críticas en contextos agénticos:

### 🔴 LLM01: Prompt Injection
**Qué es**: instrucciones maliciosas inyectadas en el input que sobreescriben el comportamiento previsto del sistema. Término acuñado por Simon Willison en septiembre 2022.

**Ver síntesis completa**: [[concepts/prompt-injection]]

**Tipos**:
- **Directa**: el usuario inyecta instrucciones en su propio mensaje
- **Indirecta**: contenido externo (páginas web, documentos, emails, chunks RAG) contiene instrucciones que el agente lee y ejecuta — acuñado por Kai Greshake (2023). El usuario no hace nada malicioso.

**Por qué no tiene solución técnica limpia**: a diferencia de SQL injection (resuelto con queries parametrizadas), los LLMs no pueden distinguir arquitecturalmente instrucciones de datos — ambos son texto plano. La solución de "prompts parametrizados" es "extremely difficult, if not impossible, to implement" (Willison, 2023).

**Caso documentado**: Bing Chat (2023) — Greshake insertó texto invisible en una página web. El agente adoptó una "agenda secreta" para obtener y exfiltrar el nombre del usuario vía URL manipulada.

**En sistemas agénticos**: cuando un agente usa Computer Use o lee documentos externos, cualquier pieza de contenido externo puede ser un vector de inyección. La superficie crece con cada herramienta añadida.

**Mitigaciones en capas**:
1. *Prompt*: Sandwich Defense, XML Tagging, Instruction Defense, Random Sequence Enclosure
2. *Arquitectura*: segregación de contenido no-confiable, menor privilegio, HITL para acciones de alto impacto
3. *Software*: [[entities/nemo-guardrails]] — Input/Retrieval/Output Rails como capas de control explícitas
4. *Operaciones*: pruebas adversariales, monitoreo de trayectorias, clasificadores de injection automáticos (Anthropic en Computer Use)

> "No fool-proof methods of prevention exist." — OWASP LLM01:2025

### 🔴 LLM06: Excessive Agency
**Qué es**: el agente tiene más acceso, herramientas o autonomía de la necesaria.

**Por qué es crítico**: un agente comprometido via LLM01 con LLM06 puede causar daño real y escalable. El paper de Fang et al. demuestra que GPT-4 puede explotar vulnerabilidades autónomamente — si ese agente tiene acceso a tu infraestructura, las consecuencias son graves.

**Mitigación**: principio de menor privilegio (mínimo acceso necesario), HITL para acciones destructivas o irreversibles, límites explícitos de recursión (LangGraph: 1000 steps), auditoría de todas las acciones.

### 🟠 LLM08: Vector and Embedding Weaknesses
**Qué es**: vectores de ataque específicos de sistemas RAG y de embeddings.

**Ataques**:
- Envenenamiento de base vectorial (insertar chunks maliciosos que el agente recuperará)
- Recuperación inadvertida de PII o datos confidenciales via similarity search
- Inversión de embeddings para reconstruir datos de entrenamiento

**Mitigación**: validar fuentes antes de indexar, segmentar bases vectoriales por nivel de acceso, nunca indexar datos más sensibles de los necesarios para cada usuario.

### 🟡 LLM10: Unbounded Consumption
**Qué es**: el agente consume recursos sin límite — tokens, API calls, tiempo, dinero.

**En LangGraph**: el límite de recursión (default: 1000 steps) es la primera línea de defensa. Combinado con `CachePolicy` y breakpoints.

**Mitigación**: límites de steps, timeouts, presupuestos de API tokens, alertas de costo.

---

## La superficie de ataque ampliada en sistemas agénticos

```
Sistema LLM Simple:
  Input usuario → LLM → Output texto
  (vectores: LLM01 directo)

Sistema Agéntico con RAG + Tools + Computer Use:
  Input usuario → LLM → ¿herramienta?
                      ├── RAG → documentos externos (LLM01 indirecto, LLM08)
                      ├── APIs → sistemas externos (LLM06)
                      ├── Code execution → entorno (LLM06)
                      └── Computer Use → pantalla/web (LLM01 visual, LLM06)
  (vectores: todos los anteriores + nuevos)
```

**Conclusión**: la superficie de ataque crece con cada herramienta añadida al agente.

---

## Cómo integrar seguridad en el SDLC agéntico

### Fase Design
- Aplicar principio de menor privilegio: ¿qué herramientas y permisos necesita realmente el agente?
- Identificar qué fuentes externas leerá el agente (potenciales vectores LLM01 indirecto)
- Diseñar HITL para acciones irreversibles o de alto impacto

### Fase Implement
- Implementar límites de recursión y timeouts
- Sanitizar inputs antes de insertarlos en prompts del sistema
- Usar entornos aislados (Docker, VMs) para Computer Use y code execution

### Fase Test
- Ejecutar pruebas de prompt injection (intentar sobreescribir instrucciones del sistema)
- Testear boundary conditions: ¿qué pasa si el agente entra en un bucle?
- Revisar qué datos pueden recuperarse inadvertidamente del vector store
- Simular escenarios de OWASP LLM Top 10

### Fase Maintain
- Tracear todas las acciones del agente (LangSmith o equivalente)
- Alertas de anomalía: número de steps inusualmente alto, tool calls a recursos inesperados
- Auditoría periódica del vector store

---

## La brecha de herramientas tradicionales

Los scanners DAST/SAST tradicionales (ZAP, Metasploit) obtuvieron **0%** de éxito en el mismo benchmark donde GPT-4 obtuvo 87%. Esto significa:

1. Los agentes LLM son amenazas que las herramientas de seguridad existentes no detectan
2. El pipeline de seguridad del SDLC debe incluir revisiones específicas para agentes
3. La seguridad agéntica es un campo emergente sin herramientas maduras de defensa

---

## Conexiones

- [[concepts/devsecops]] — DevSecOps debe adaptarse para incluir estas vulnerabilidades
- [[owasp-llm-top10]] — fuente primaria del OWASP Top 10
- [[llm-agents-vulnerabilities]] — evidencia empírica (Fang et al.)
- [[computer-use]] — Computer Use amplifica LLM01 y LLM06
- [[concepts/rag]] — RAG introduce LLM08
- [[concepts/langgraph]] — mitigaciones estructurales (límite recursión, HITL con Interrupts)
- [[concepts/prompt-injection]] — síntesis profunda de LLM01 (taxonomía, mecanismos, mitigaciones)
- [[entities/nemo-guardrails]] — toolkit open-source de mitigación estructural
- [[simon-willison-2022]] — origen histórico del concepto
- [[simon-willison-2023]] — indirect injection en detalle
