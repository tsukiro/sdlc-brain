---
title: "DevSecOps"
type: concept
tags: [sdlc, devsecops, seguridad, devops, testing]
sources: [2026-05-13_aws-sdlc.md]
updated: 2026-05-13
---

# DevSecOps

## Descripción

DevSecOps es la práctica de **integrar seguridad en cada fase del SDLC** en lugar de tratarla como un paso final o un proceso separado. La "Sec" se inserta entre "Dev" (desarrollo) y "Ops" (operaciones), señalando que la seguridad es responsabilidad compartida de todos, no solo del equipo de seguridad.

## Fuente primaria

Cubierto principalmente por **AWS**. IBM menciona el SSDLC (Secure SDLC) como concepto relacionado pero sin detalle. Atlassian no lo cubre en su artículo principal de SDLC.

## Principios

- La seguridad se integra **en cada fase** del SDLC, no se añade al final
- Colaboración entre tres grupos: desarrolladores, especialistas en seguridad y operaciones
- Shift-left: detectar y resolver problemas de seguridad lo más temprano posible

## Prácticas por fase del SDLC

| Fase        | Actividad de seguridad                               |
|-------------|------------------------------------------------------|
| Plan        | Modelado de amenazas, requisitos de seguridad        |
| Design      | Análisis arquitectónico, revisión de diseño de seguridad |
| Implement   | Revisión de código estático (SAST), coding seguro    |
| Test        | Pruebas dinámicas (DAST), pruebas de penetración     |
| Deploy      | Configuración segura, gestión de secretos            |
| Maintain    | Monitoreo de vulnerabilidades, respuesta a incidentes|

## Prácticas específicas mencionadas (AWS)

- **Revisión de código**: análisis estático del código fuente
- **Análisis arquitectónico**: evaluación de riesgos de seguridad en el diseño
- **Pruebas de penetración**: simulación de ataques para encontrar vulnerabilidades

## Relación con SSDLC

IBM menciona el **SSDLC (Secure Software Development Life Cycle)** como concepto relacionado. La diferencia de énfasis:
- **DevSecOps**: cultura y colaboración (personas + proceso)
- **SSDLC**: el proceso formal de integrar seguridad en las fases del ciclo de vida

En la práctica, ambos términos apuntan a la misma idea: seguridad integrada, no como etapa final.

## Conexiones

- [[sdlc]] — concepto padre
- [[agile]] — DevSecOps se implementa frecuentemente en contextos Agile
- [[aws-sdlc]] — fuente primaria
- [[ibm-sdlc]] — menciona SSDLC como concepto relacionado
