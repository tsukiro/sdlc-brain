# Fuente: Learn Prompting — Defensive Measures Against Prompt Injection

**URL**: https://learnprompting.org/docs/prompt_hacking/defensive_measures
**Fecha de acceso**: 2026-05-13
**Tipo**: Documentación educativa (Learn Prompting)

---

## Técnicas de Defensa Documentadas

1. **Filtrado (Filtering)**: Eliminar palabras específicas que podrían usarse en ataques de prompt injection.

2. **Defensa por Instrucción (Instruction Defense)**: Mejorar las instrucciones del prompt para hacerlas más resistentes a manipulación. Incluye instrucciones explícitas sobre cómo tratar el input del usuario.

3. **Evaluación por LLM Separado (Separate LLM Evaluation)**: Utilizar otro modelo de lenguaje para validar resultados antes de entregarlos al usuario.

4. **Post-Prompting**: Añadir contexto o instrucciones de seguridad después del contenido del usuario, no solo antes.

5. **Encierro por Secuencia Aleatoria (Random Sequence Enclosure)**: Rodear el input del usuario con patrones impredecibles que el atacante no puede anticipar.

6. **Defensa Sandwich (Sandwich Defense)**: Encapsular el contenido del usuario entre instrucciones de seguridad — instrucciones de sistema, luego input del usuario, luego instrucciones de sistema de nuevo.

7. **Etiquetado XML (XML Tagging)**: Marcar claramente diferentes secciones del prompt usando etiquetas XML estructuradas, separando visiblemente instrucciones de datos.

## Principio Fundamental

"Si tu aplicación no requiere texto sin restricciones, no permitas esos resultados." — Restricción de output como primera línea de defensa.

## Nota sobre el contenido

La fuente proporciona una visión general de las técnicas pero con nivel de detalle limitado. Para implementaciones específicas, combinar con NeMo Guardrails (implementación) y OWASP LLM01 (marco de referencia).
