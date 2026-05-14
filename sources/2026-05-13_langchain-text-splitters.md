# LangChain — Text Splitters

**URL:** https://python.langchain.com/docs/concepts/text_splitters/ (redirige a docs.langchain.com)  
**Referencia API:** https://docs.langchain.com/oss/python/integrations/splitters  
**Fecha de captura:** 2026-05-13  
**Capturado vía:** búsqueda web + fetch docs

---

## Qué son los Text Splitters en LangChain

Componentes del paquete `langchain-text-splitters` que implementan estrategias de chunking para preparar documentos para RAG y otros pipelines de LLM.

## Tipos principales

### RecursiveCharacterTextSplitter (recomendado por defecto)
- Divide respetando límite natural del texto: párrafos → líneas → espacios → caracteres
- Preserva unidades semánticas mayores siempre que es posible
- Parámetros clave: `chunk_size`, `chunk_overlap`, `separators`
- Recomendado para la mayoría de casos de uso

### CharacterTextSplitter
- Divide basándose en número de caracteres
- Más simple, sin respeto de jerarquía semántica
- Útil cuando consistencia de tamaño importa más que coherencia semántica

### TokenTextSplitter
- Divide según tokens del tokenizador del modelo (no caracteres)
- Más alineado con límites reales de los LLMs
- Útil cuando se trabaja cerca de los límites del context window

### SemanticChunker
- Disponible en `langchain_experimental`
- Usa embeddings para detectar cambios semánticos entre oraciones
- Implementa la estrategia Semantic Chunking del Firecrawl blog

## Parámetros comunes

- `chunk_size`: tamaño máximo del chunk
- `chunk_overlap`: solapamiento entre chunks consecutivos
- `length_function`: función para medir longitud (chars vs tokens)
- `separators`: lista de separadores jerárquicos (para Recursive)

## Relación con el SDLC / RAG pipeline

Los text splitters son el paso de "Transform" en el pipeline Document → Split → Embed → Store → Retrieve → Generate. Su elección afecta directamente la calidad del retrieval.
