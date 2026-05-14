# CLAUDE.md — Esquema del LLM Wiki

Este archivo es la fuente de verdad sobre cómo funciona este wiki. Léelo al inicio de cada sesión antes de hacer cualquier operación.

---

## Estructura del vault

```
vault/
├── CLAUDE.md              ← este archivo (esquema y reglas)
├── sources/               ← documentos fuente originales (solo lectura)
│   └── _README.md
├── wiki/                  ← wiki mantenido por el LLM (tú escribes aquí)
│   ├── index.md           ← catálogo de todas las páginas del wiki
│   ├── log.md             ← registro cronológico append-only
│   ├── overview.md        ← síntesis de alto nivel (actualizar con cada ingest)
│   ├── entities/          ← páginas de entidades concretas (personas, proyectos, herramientas)
│   ├── concepts/          ← páginas de conceptos y temas abstractos
│   └── analyses/          ← resultados de consultas que vale la pena conservar
```

**Invariantes:**
- `sources/` es inmutable: el LLM nunca modifica ni elimina archivos ahí.
- `wiki/` es territorio del LLM: crea, actualiza y elimina páginas según sea necesario.
- El usuario lee el wiki; el LLM lo escribe.

---

## Convenciones de archivos wiki

### Frontmatter obligatorio

Todas las páginas wiki (excepto `index.md` y `log.md`) deben tener este frontmatter:

```yaml
---
title: "Nombre de la página"
type: entity | concept | source-summary | analysis | overview
tags: [tag1, tag2]
sources: [fuente1.md, fuente2.md]   # fuentes que informaron esta página
updated: YYYY-MM-DD
---
```

### Nombrado de archivos
- Usa `kebab-case` para todos los archivos: `mi-concepto.md`, `juan-perez.md`
- Nombres descriptivos y sin ambigüedad
- Subcarpetas solo para `entities/`, `concepts/`, `analyses/`

### Wikilinks
- Usa siempre `[[nombre-de-página]]` para referencias internas
- Si mencionas una entidad o concepto que no tiene página todavía, crea el link de todas formas (Obsidian lo mostrará como roto hasta que crees la página — esto es útil para detectar gaps)

---

## Operación: Ingest

Cuando el usuario añade una nueva fuente:

1. **Lee** el documento fuente completo desde `sources/`
2. **Discute** con el usuario los puntos clave si es ambiguo o denso
3. **Crea** una página de resumen en `wiki/` (tipo `source-summary`) con:
   - Resumen en prosa de los puntos más importantes
   - Citas textuales relevantes en blockquotes
   - Sección `## Conexiones` listando páginas wiki relacionadas
4. **Actualiza** páginas de entidades y conceptos existentes que el nuevo documento enriquece, contradice o expande
   - En caso de contradicción: añade una sección `## Contradicciones / Tensiones` en la página afectada
5. **Actualiza** `wiki/overview.md` si la síntesis global cambia
6. **Actualiza** `wiki/index.md`: añade la nueva entrada con enlace y descripción de una línea
7. **Añade** entrada al final de `wiki/log.md` con el formato:

```
## [YYYY-MM-DD] ingest | Título del documento
- Resumen de 1-2 líneas sobre qué aportó
- Páginas creadas: [[página1]], [[página2]]
- Páginas actualizadas: [[página3]], [[página4]]
```

---

## Operación: Query

Cuando el usuario hace una pregunta:

1. **Lee** `wiki/index.md` para identificar páginas relevantes
2. **Lee** las páginas relevantes
3. **Sintetiza** una respuesta con citas a las páginas wiki (`[[enlace]]` o sección + página)
4. **Evalúa** si la respuesta es suficientemente valiosa para guardarla:
   - Si sí: crea una página en `wiki/analyses/` (tipo `analysis`) y la añades al `index.md` y al `log.md`
   - Si no: responde solo en el chat

Formato de entry en log para queries guardadas:
```
## [YYYY-MM-DD] query | Pregunta resumida
- Respuesta archivada en: [[analyses/nombre-del-análisis]]
```

---

## Operación: Lint

Cuando el usuario pide una revisión de salud del wiki:

Busca y reporta:
1. **Contradicciones**: afirmaciones en páginas distintas que se contradicen
2. **Páginas huérfanas**: páginas sin ningún inbound link desde otras páginas wiki
3. **Claims obsoletos**: afirmaciones que fuentes más recientes han superado
4. **Gaps de cobertura**: conceptos o entidades mencionados sin su propia página
5. **Cross-references faltantes**: páginas relacionadas que no se enlazan entre sí
6. **Fuentes sin procesar**: archivos en `sources/` sin su correspondiente `source-summary`

Genera un reporte en el chat (no lo guardes a menos que el usuario lo pida) y sugiere acciones concretas.

Entry en log:
```
## [YYYY-MM-DD] lint
- N issues encontrados: [lista breve]
- Acciones tomadas: [lista breve o "ninguna, solo reporte"]
```

---

## Convenciones de `index.md`

El índice agrupa las páginas por tipo. Formato de cada entrada:

```markdown
- [[ruta/nombre-de-página]] — descripción de una línea (N fuentes)
```

Secciones del índice:
- `## Overview`
- `## Source Summaries`
- `## Entities`
- `## Concepts`
- `## Analyses`

---

## Convenciones de `log.md`

- **Solo se añade al final**, nunca se edita entradas anteriores
- Cada entrada comienza con `## [YYYY-MM-DD] tipo | título`
- Tipos válidos: `ingest`, `query`, `lint`, `meta` (para cambios al esquema)
- El prefijo `## [` permite parsear con: `grep "^## \[" wiki/log.md | tail -10`

---

## Evolución del esquema

Si el usuario y el LLM acuerdan cambiar cómo funciona el wiki, actualiza este archivo y añade una entrada `meta` al log explicando qué cambió y por qué.

---

## Inicio de sesión

Al comenzar una nueva sesión en este vault:
1. Lee este `CLAUDE.md`
2. Lee `wiki/log.md` (últimas 10 entradas) para contexto reciente
3. Lee `wiki/index.md` para tener el mapa del wiki
4. Confirma al usuario que estás listo y resume brevemente el estado actual del wiki
