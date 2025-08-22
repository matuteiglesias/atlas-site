---
id: pocket_git_push_internals
title: "Git push — objetos y conteo"
slug: /pocket/git_push_internals
module: apendice-pocket
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [git, push, troubleshooting, objetos, blobs, trees]
source_repo: <repo-url>
source_path: docs/pocket/git_push_internals.md
---

## Idea central
El conteo de “objetos” durante `git push` **no se corresponde** con “número de archivos tocados”. Git empuja *objetos* (commits, trees, blobs) necesarios para sincronizar **todo lo que falta** en el remoto —no solo tu último commit— y trabaja por **deltas**. <!-- removed contentReference -->

## Por qué ves muchos objetos si tocaste poco
- Podés llevar varios commits sin pushear; el push incluye **todos**.  
- Un solo archivo puede implicar **múltiples blobs** y **trees** (estructura de directorios).  
- Si hiciste add→remove de un archivo pesado en commits distintos, el **histórico** sigue contando. <!-- removed contentReference -->

## Diagnóstico mínimo
1) **Qué commits faltan en remoto** (lo que efectivamente empujarías):
~~~bash
git log --oneline --graph --decorate origin/main..main
~~~



2. **Historia de un archivo sospechoso** (para confirmar si está en la historia):

~~~bash
git log -- path/al/archivo.csv
~~~

Si no hay salida, no aparece en la historia; si aparece, ese blob cuenta para el push.&#x20;

## Lectura crítica (cuando “parece raro”)

* Un push grande *no implica* que “subís archivos gigantes ahora”; puede ser backlog de commits locales.
* Si eliminaste un archivo en *otro* commit, **sigue existiendo** en la historia y puede bloquear por límites de tamaño (ver “Archivos grandes”).&#x20;

