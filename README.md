
# Documentación de Backlogs y Funcionalidades Implementadas

Este repositorio está destinado a la documentación de backlogs, así como a las funcionalidades ya implementadas en los proyectos del Core. A medida que se avanza en el desarrollo, se actualizará esta documentación para mantener un registro detallado y organizado.

## Índice

- [Introducción](#introducción)
- [Estructura del Repositorio](#estructura-del-repositorio)
- [Documentación de Backlogs](#documentación-de-backlogs)
  - [Listado de Backlogs](#listado-de-backlogs)
- [Documentación de Funcionalidades Implementadas](#documentación-de-funcionalidades-implementadas)
  - [Listado de Documentos Técnicos](#listado-de-documentos-técnicos)
- [Contribuir](#contribuir)
- [Contacto](#contacto)

## Introducción

Este repositorio contiene toda la documentación relacionada con el desarrollo y seguimiento de los proyectos. Aquí encontrarás información detallada sobre los backlogs, las decisiones de diseño y las funcionalidades implementadas. El propósito es mantener un historial claro y accesible para todos los miembros del equipo y partes interesadas.

## Estructura del Repositorio

La estructura del repositorio es la siguiente:

- **`README.md`**: Este archivo, que sirve como guía principal del repositorio.
- **`Backlog/`**: Contiene la documentación relacionada con los backlogs del proyecto. Dentro de este directorio, encontrarás documentos que detallan las historias de usuario, tareas pendientes, prioridades, etc.
- **`Docs/`**: Contiene la documentación técnica de las funcionalidades ya implementadas. Aquí se detallan las especificaciones técnicas, decisiones de diseño, diagramas, y cualquier otra información relevante.

### Estructura de Archivos

```
├── README.md
├── Backlog/
│   └── core/
│   │   └── feature1/
│   │       └── feature1.md
│   └── transversal/
│       └── feature1/
│           └── feature1.md
└── Docs/
    └── core/
		└── feature1/
            └── feature1.md
```

## Documentación de Backlogs

Los documentos dentro de la carpeta `Backlog/` describen las historias de usuario, tareas, y otros elementos pendientes de desarrollo. Esta sección se mantendrá actualizada con el estado y prioridad de cada elemento.

### Listado de Backlogs

##### Core
- [Apagar servidor de Elasticsearch](Backlog/core/elasctic/down.md)
- [Aumentar covertura de los Test](Backlog/core/test/coverage.md)
- [Mejoras en importadores](Backlog/core/imports/import.md)
- [Mejoras en exportadores](Backlog/core/exports/export.md)

##### Transversal
- [Rediseño de Gema Demographic](Backlog/transversal/demographic/redesign.md)

## Documentación de Funcionalidades Implementadas

La carpeta `Docs/` contiene toda la documentación técnica relacionada con las funcionalidades que ya se han implementado. Este directorio se actualizará a medida que se completen nuevas funcionalidades.

### Listado de Documentos Técnicos

##### Core
- [Segmentos](Docs/core/Segments/funcionamiento_segmentos.md)
- [Sincronización con posiciones](Docs/core/sincronización/positions.md)
- [Sincronización sin posiciones](Docs/core/sincronización/single.md)

#### Auth
- [Cambio de formato de contraseña](Docs/auth/regex.md)

## Contribuir

Para contribuir a la documentación:

1. Crea un branch nuevo para tu documentación.
2. Sigue la estructura y formato establecido en este repositorio.
3. **Formato de Nombres de Archivos**:
    - **Sin Espacios**: Los nombres de los archivos deben evitar el uso de espacios. En su lugar, usa guiones bajos (`_`) para separar palabras. Por ejemplo:
        - `incorrecto: funcionamiento de los segmentos en master.md`
        - `correcto: funcionamiento_segmentos_en_master.md`
    - **Minúsculas**: Usa minúsculas para los nombres de archivos y directorios para mantener consistencia.
    - **Descriptivos**: Asegúrate de que los nombres de los archivos sean descriptivos y reflejen el contenido del documento.
4. Haz un pull request una vez que tu documentación esté completa.

O sigue la siguiente Guía para trabajar desde [Obsidian](https://forum.obsidian.md/t/the-easiest-way-to-setup-obsidian-git-to-backup-notes/51429) **RECOMENDADO**

Asegúrate de mantener la coherencia en la estructura y el contenido de la documentación para facilitar la navegación y comprensión.

## Contacto

Para cualquier duda o sugerencia, por favor contacta al equipo a través de slack [#product-ops](https://rankmiteam.slack.com/archives/C055V1FEK6X)
