---
id: pocket_find_sed_html
title: "Batch-update HTML (find+sed)"
slug: /pocket/find_sed_html
module: apendice-pocket
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [cli, html, find, sed, batch-update]
source_repo: <repo-url>
source_path: docs/pocket/find_sed_html.md
---

## Propósito
Hacer **reemplazos masivos** en `.html` (URLs, versiones de librerías) con seguridad.

## Recetas confirmadas

### 1) Reemplazo in-place (todas las `.html`)
~~~bash
find . -name '*.html' -type f -exec sed -i 's|api.mapbox.com/mapbox-gl-js/v2.4.1|api.mapbox.com/mapbox-gl-js/v3.0.0|g' {} +
~~~

Sustituye *todas* las apariciones y modifica los archivos en sitio. **Hacé backup** antes.&#x20;

### 2) Cambiar enlaces CSS/JS de Mapbox (dos pasos)

~~~bash
find . -type f -name "*.html" -exec sed -i "s|https://api.mapbox.com/mapbox-gl-js/v3.0.0/mapbox-gl.css|https://api.mapbox.com/mapbox-gl-js/v3.0.0-beta.1/mapbox-gl.css|g" {} +
find . -type f -name "*.html" -exec sed -i "s|https://api.mapbox.com/mapbox-gl-js/v3.0.0/mapbox-gl.js|https://api.mapbox.com/mapbox-gl-js/v3.0.0-beta.1/mapbox-gl.js|g" {} +
~~~

Útil para congelar versiones o testear betas de forma consistente.&#x20;

## Consejos operativos

* **Dry-run**: probá primero en un subfolder o usa `sed -n 's|old|new|gp' file.html` para ver difs.
* **Backup**: `sed -i.bak 's|old|new|g' file.html` deja `file.html.bak`.
* Si el cambio toca **tokens/URLs** de automatización, documentá en `operacion/ci_github_actions.md` (runbook de secrets/dispatch).

## QA mínimo

* Contabilizá archivos afectados (`| wc -l`).
* Grep final para verificar que **no quedan** ocurrencias viejas.
