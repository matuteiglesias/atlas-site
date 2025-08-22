---
id: pocket_cli_ipynb_search
title: "CLI — buscar en .ipynb"
slug: /pocket/cli_ipynb_search
module: apendice-pocket
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [cli, grep, find, ipynb, notebooks]
source_repo: <repo-url>
source_path: docs/pocket/cli_ipynb_search.md
---

## Propósito
Buscar **snippets/funciones** dentro de `.ipynb` sin abrirlos (útil en higiene y refactors).

## Recetas rápidas

### 1) `find` + `grep` (recursivo, muestra archivo + match)
~~~bash
find . -name "*.ipynb" -exec grep -H "get_maps_image" {} \;
~~~

Imprime el nombre del notebook y la línea con el match. Cambiá `"get_maps_image"` por tu patrón.&#x20;

### 2) Solo notebooks (`--include`) y búsqueda recursiva

~~~bash
grep -r "['circuit_id'] = " --include='*.ipynb'
~~~

Funciona en Linux/macOS; en Windows usá WSL, Git Bash o Cygwin.&#x20;

### 3) Bucle simple (archivo + línea)

~~~bash
for file in *.ipynb; do
  grep -Hn 'stats_circuitos' "$file" && echo "$file"
done
~~~

Recordá que `.ipynb` es **JSON**: puede devolver coincidencias en **outputs/metadata** además del código.&#x20;

## Variantes útiles

* Solo nombres de archivos: añade `-l` a `grep`.&#x20;
* Case-insensitive: `grep -i ...`
* Contar líneas: `grep -n ...`
* Directorio raíz del proyecto: ejecutá los comandos en la carpeta tope (evita falsos negativos).

## Checklist

* ¿Tu patrón es demasiado específico? Probá fragmentos más cortos.
* ¿El match aparece en `outputs`? Considerá limpiar/colapsar salidas antes de versionar.

~~~