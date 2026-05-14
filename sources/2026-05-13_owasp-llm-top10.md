# OWASP Top 10 for LLM Applications 2025

**URL:** https://genai.owasp.org/llm-top-10/  
**Fecha de captura:** 2026-05-13

---

## Las 10 vulnerabilidades (versión 2025)

### LLM01: Prompt Injection
Prompts del usuario alteran el comportamiento del LLM de formas no previstas. El modelo sigue instrucciones maliciosas inyectadas en el input. Tipos: directo (usuario manipula el prompt) e indirecto (contenido externo — páginas web, documentos — inyecta instrucciones).

### LLM02: Sensitive Information Disclosure
El LLM puede revelar información sensible incluida en su entrenamiento, contexto o sistema prompt. Incluye PII, credenciales, datos confidenciales de la empresa.

### LLM03: Supply Chain
Las cadenas de suministro de LLM son vulnerables: modelos preentrenados comprometidos, datasets de fine-tuning envenenados, paquetes de terceros maliciosos, plugins inseguros.

### LLM04: Data and Model Poisoning
Compromiso de datos de pre-entrenamiento, fine-tuning o embedding. Un actor malicioso puede envenenar los datos para introducir backdoors o sesgos en el modelo resultante.

### LLM05: Improper Output Handling
Validación y sanitización insuficiente de outputs del LLM antes de usarlos en sistemas downstream. Puede llevar a XSS, SSRF, SQLi si el output se usa sin sanitizar.

### LLM06: Excessive Agency
El sistema LLM recibe demasiada autonomía o acceso a recursos. Un agente con demasiadas herramientas/permisos puede causar daño significativo si es comprometido o produce outputs inesperados. **Directamente relacionado con el paper de Fang et al.**

### LLM07: System Prompt Leakage
El prompt del sistema (que puede contener instrucciones confidenciales, lógica de negocio o credenciales) puede ser extraído por un atacante con el prompting adecuado.

### LLM08: Vector and Embedding Weaknesses
Riesgos en sistemas que usan vectores y embeddings: envenenamiento de la base vectorial, recuperación de datos sensibles via similarity search, ataques de inversión de embeddings.

### LLM09: Misinformation
Los LLMs generan información falsa con confianza. En sistemas agénticos, la misinformation puede propagarse y usarse como base para decisiones subsiguientes.

### LLM10: Unbounded Consumption
Un LLM consume recursos sin límite: tokens, llamadas API, cómputo. Puede resultar en DoS (Denial of Service) o costos descontrolados. En agentes: bucles infinitos, llamadas excesivas a herramientas.

## Relación con arquitecturas agénticas

Las vulnerabilidades más críticas en contextos agénticos:
- **LLM01** (Prompt Injection): especialmente peligroso en Computer Use donde el modelo ve contenido de páginas web
- **LLM06** (Excessive Agency): el riesgo crece con cada herramienta/permiso que se le da al agente
- **LLM08** (Vector Weaknesses): afecta directamente pipelines RAG
- **LLM10** (Unbounded Consumption): los bucles agénticos pueden disparar costos si no tienen límites
