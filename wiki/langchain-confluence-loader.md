---
title: "LangChain — ConfluenceLoader"
type: source-summary
tags: [langchain, confluence, document-loader, rag, knowledge-base, python]
sources: []
updated: 2026-05-14
---

# LangChain — ConfluenceLoader

**Fuente:** https://docs.langchain.com/oss/python/integrations/document_loaders/confluence  
**Package:** `langchain-community` + `atlassian-python-api`  
**Clase:** `ConfluenceLoader`  
**Output:** Lista de objetos `Document` (LangChain core)

## Qué hace

Permite cargar páginas de Confluence como `Document` objects de LangChain para usarlos en pipelines RAG, indexación en vector stores o cualquier cadena de procesamiento de documentos.

---

## Instalación

```bash
pip install -qU atlassian-python-api langchain-community
```

---

## Métodos de autenticación

| Método | Cuándo usar |
|---|---|
| `username` + `api_key` | Atlassian Cloud |
| `token` | Data Center / Server on-prem (PAT) |
| `OAuth2` | Flujos OAuth2 |
| `cookies` | Sesión de browser |

---

## Selección de páginas

| Parámetro | Tipo | Descripción |
|---|---|---|
| `page_ids` | `list[str]` | IDs específicos de páginas |
| `space_key` | `str` | Carga todas las páginas del space |

Si se pasan ambos → unión de páginas. Ambos valores están en la URL:  
`https://yoursite.atlassian.com/wiki/spaces/{space_key}/pages/{page_id}`

---

## Parámetros clave

| Parámetro | Default | Descripción |
|---|---|---|
| `include_attachments` | `False` | Extrae texto de adjuntos (PDF, PNG, JPEG, SVG, DOC, XLS) |
| `limit` | `50` | Documentos por batch (máx 100 en atlassian-python-api) |
| `max_pages` | `1000` | Total máximo de documentos a cargar |

---

## Ejemplos de uso

### Cloud (username + API token)

```python
from langchain_community.document_loaders import ConfluenceLoader

loader = ConfluenceLoader(
    url="https://yoursite.atlassian.com/wiki",
    username="user@example.com",
    api_key="<api-token>",          # desde id.atlassian.com/manage-profile/security/api-tokens
    space_key="MYSPACE",
    include_attachments=True,
    limit=50,
)
documents = loader.load()
```

### On-Prem / Data Center (PAT)

```python
from langchain_community.document_loaders import ConfluenceLoader

loader = ConfluenceLoader(
    url="https://confluence.yoursite.com/",
    token="<personal-access-token>",   # solo token, sin username
    space_key="MYSPACE",
    include_attachments=True,
    limit=50,
    max_pages=50,
)
documents = loader.load()
```

### Integración en pipeline RAG

```python
from langchain_community.document_loaders import ConfluenceLoader
from langchain_text_splitters import RecursiveCharacterTextSplitter
from langchain_openai import OpenAIEmbeddings
from langchain_community.vectorstores import Chroma

# 1. Cargar páginas
loader = ConfluenceLoader(url=..., username=..., api_key=..., space_key="DOCS")
docs = loader.load()

# 2. Chunking
splitter = RecursiveCharacterTextSplitter(chunk_size=512, chunk_overlap=50)
chunks = splitter.split_documents(docs)

# 3. Indexar en vector store
vectorstore = Chroma.from_documents(chunks, OpenAIEmbeddings())
retriever = vectorstore.as_retriever()
```

---

## Consideraciones de producción

- El loader respeta **permisos del usuario** cuyo token/API key se usa — solo carga páginas accesibles
- Para spaces grandes, usar `max_pages` para controlar el volumen
- `include_attachments=True` descarga archivos — puede ser lento con muchos adjuntos PDF; considerar procesamiento asíncrono
- Los `Document` objects incluyen metadata (`page_id`, `title`, `url`, `space_key`) útil para citations en RAG

---

## Conexiones

- [[concepts/rag]] — ConfluenceLoader como fuente de documentos para pipelines RAG
- [[langchain-python]] — ConfluenceLoader es parte del ecosistema LangChain
- [[langchain-text-splitters]] — siguiente paso: chunking de los Documents cargados
- [[concepts/langchain]] — Document Loaders como categoría de componentes LangChain
