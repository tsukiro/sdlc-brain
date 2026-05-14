# Fuente: NVIDIA NeMo Guardrails (GitHub)

**URL**: https://github.com/NVIDIA/NeMo-Guardrails
**Fecha de acceso**: 2026-05-13
**Tipo**: Repositorio open-source (NVIDIA)

---

## ¿Qué es?

NeMo Guardrails es un toolkit open-source de NVIDIA para agregar "guardrails" (controles) programables a aplicaciones conversacionales basadas en LLMs. Las guardrails son "specific ways of controlling the output of a large language model, such as not talking about politics, responding in a particular way to specific user requests."

## Los Cinco Tipos de Rails

1. **Input Rails**: Procesan y validan la entrada del usuario antes de cualquier procesamiento adicional. Pueden rechazar o modificar contenido antes de que llegue al LLM.
2. **Dialog Rails**: Influyen en cómo se estructura el prompt del LLM; determinan si se ejecutan acciones específicas o si se usan respuestas predefinidas.
3. **Retrieval Rails**: Operan en escenarios RAG, filtrando o alterando fragmentos recuperados antes de usarlos en el prompt.
4. **Execution Rails**: Supervisan entradas y salidas de acciones personalizadas (tools) que el LLM necesita invocar.
5. **Output Rails**: Validan y modifican la salida generada antes de devolverla al usuario.

## Lenguaje Colang

Introduce **Colang**, "a modeling language specifically created for designing flexible, yet controllable, dialogue flows" con sintaxis similar a Python. Soporta Colang 1.0 y Colang 2.0.

```
define user express greeting
  "Hello!"
  "Good afternoon!"

define flow
  user express greeting
  bot express greeting
  bot offer to help
```

## Instalación

```bash
pip install nemoguardrails
```

Requiere Python 3.10–3.13. Depende de `annoy` (requiere compiladores C++).

## Integración LangChain

Opcional. Se activa con `NEMOGUARDRAILS_LLM_FRAMEWORK=langchain`. Permite envolver cadenas LangChain o invocarlas dentro de configuraciones de guardrails.

## Ejemplo de uso básico

```python
from nemoguardrails import LLMRails, RailsConfig

config = RailsConfig.from_path("PATH/TO/CONFIG")
rails = LLMRails(config)

completion = rails.generate(
    messages=[{"role": "user", "content": "Hello world!"}]
)
```

## LLMs soportados

OpenAI (GPT-3.5, GPT-4), LLaMA-2, Falcon, Vicuna, Mosaic, y otros mediante integración LangChain.

## Características adicionales

- CLI integrada para chatear, evaluar y ejecutar servidores
- Servidor HTTP que expone API `/v1/chat/completions`
- Docker support
- Herramientas de evaluación de vulnerabilidades LLM
