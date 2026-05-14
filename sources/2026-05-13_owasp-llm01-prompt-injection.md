# Fuente: OWASP LLM01:2025 Prompt Injection

**URL**: https://genai.owasp.org/llmrisk/llm01-prompt-injection/
**Fecha de acceso**: 2026-05-13
**Tipo**: Referencia de seguridad (OWASP GenAI Working Group)

---

## Definición

Una vulnerabilidad de inyección de prompts ocurre cuando "user prompts alter the LLM's behavior or output in unintended ways". Estos ataques pueden funcionar incluso si los humanos no pueden percibir el contenido malicioso, mientras el modelo lo interprete.

Distinción: prompt injection y jailbreaking son distintos. "Jailbreaking es una forma de prompt injection donde el atacante proporciona entradas que causan que el modelo desatienda sus protocolos de seguridad completamente."

## Tipos

**Inyecciones Directas**: "a user's prompt input directly alters the behavior of the model in unintended or unexpected ways" — pueden ser intencionales o accidentales.

**Inyecciones Indirectas**: ocurren cuando "an LLM accepts input from external sources, such as websites or files" que contienen instrucciones maliciosas.

## Nueve Escenarios de Ataque

1. **Inyección Directa**: Atacante inyecta prompts en chatbot de soporte, instruye ignorar directrices previas → acceso no autorizado a datos privados.
2. **Inyección Indirecta**: Usuario usa LLM para resumir página con instrucciones ocultas que insertan imágenes maliciosas para exfiltración.
3. **Inyección Accidental**: Compañía incluye instrucciones de detección de IA en descripción laboral; candidato usa LLM inadvertidamente activando la detección.
4. **Influencia Intencional del Modelo**: Atacante modifica documento en repositorio RAG; contenido malicioso altera salidas del LLM.
5. **Inyección de Código**: CVE-2024-5184 en asistente de correo permite "inject malicious prompts, allowing access to sensitive information and manipulation of email content".
6. **Payload Dividido**: Atacante divide prompts maliciosos en CV; cuando LLM evalúa candidato, prompts combinados manipulan respuesta.
7. **Inyección Multimodal**: Atacante "embeds a malicious prompt within an image that accompanies benign text".
8. **Sufijo Adversarial**: String de caracteres aparentemente sin sentido influye maliciosamente en salida, "bypassing safety measures".
9. **Ataque Multilingüe/Ofuscado**: Múltiples idiomas o codificación (Base64, emojis) eluden filtros.

## Impactos Potenciales

- Divulgación de información sensible
- Revelación de infraestructura de IA o prompts del sistema
- Manipulación de contenido generando sesgos
- Acceso no autorizado a funciones del LLM
- Ejecución de comandos en sistemas conectados
- Manipulación de procesos críticos de decisión

## Siete Estrategias de Mitigación

1. **Constreñimiento de Comportamiento**: Instrucciones específicas en prompts del sistema; "enforce strict context adherence".
2. **Definición y Validación de Formatos**: Formatos claros de salida; "use deterministic code to validate adherence".
3. **Filtrado de Entrada/Salida**: Reglas para identificar contenido sensible; filtros semánticos; "Assess context relevance, groundedness, and question/answer relevance".
4. **Control de Privilegios**: "restrict the model's access privileges to the minimum necessary".
5. **Aprobación Humana**: Controles de "human-in-the-loop" para operaciones privilegiadas.
6. **Segregación de Contenido Externo**: "Separate and clearly denote untrusted content to limit its influence on user prompts".
7. **Pruebas Adversariales**: "regular penetration testing and breach simulations, treating the model as an untrusted user".

## Realidad Fundamental

"Given the stochastic influence at the heart of the way models work, it is unclear if there are fool-proof methods of prevention for prompt injection."
