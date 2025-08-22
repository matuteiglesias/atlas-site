---
id: pocket_css_pdf_landscape
title: "CSS para PDFs (A4 landscape)"
slug: /pocket/css_pdf_landscape
module: apendice-pocket
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [publishing, css, pdf, print, landscape]
source_repo: <repo-url>
source_path: docs/pocket/css_pdf_landscape.md
---

## Propósito
Hoja de estilo **print** para PDF en **A4 apaisado**, con **márgenes chicos**, tipografías compactas y tablas de ancho completo. Punto de partida reproducible de tus prototipos. <!-- removed contentReference -->

## `print.css` (base compacta)
~~~css
/* Tipografía y densidad */
body { font-family: Arial, sans-serif; font-size: 10px; }

/* Títulos compactos */
h1, h2, h3 { color: MidnightBlue; margin: 0.2em 0; }

/* Tablas densas y full width */
table { width: 100%; border-collapse: collapse; }
th, td { border: 1px solid #696969; padding: 0.2em; }
th { background: Gainsboro; font-weight: bold; }

/* Página A4 landscape + márgenes chicos */
@page { size: A4 landscape; margin: 5mm; }

/* Cortes de página */
.page-break { page-break-after: always; }

/* Footer opcional (paginación) */
.footer {
  position: fixed; bottom: 5px; width: 100%;
  text-align: center; font-size: 8px;
}
~~~

Este bloque refleja exactamente tus ajustes de tamaño, márgenes y layout para impresión.&#x20;

### Variante ultra-compacta (cuando el PDF queda “grande”)

~~~css
body { font-size: 8px; }
table { font-size: 8px; }
th, td { padding: 2px; }
@page { size: A4 landscape; margin: 5mm; }
~~~

Cuando el motor de PDF no respeta el *zoom*, bajar `font-size` y `padding` resulta más confiable.&#x20;

## Integración y gotchas

* Cargar como `print.css` con `media="print"`; para *export to PDF* vía navegador suele bastar.
* Algunos motores (Chromium vs. wkhtmltopdf) tratan `@page` distinto: si el margen no aplica, reducir padding/márgenes de **contenedores** (no sólo `@page`).
* Evitar `position: fixed` en muchos elementos; mantenelo sólo en `.footer` (paginación simple).

## QA mínimo

* **Smoke test**: una página con una tabla ancha, títulos H1/H2 y `.page-break`.
* Verificar **cortes** (que H2 no quede solo al final de página).
* Embed de fuentes si usás familia no estándar (para evitar sustituciones en el PDF).

> Complementa el **print.css maestro** del sitio; si hacés un override, documentá el alcance (qué se sobreescribe y por qué).

~~~
