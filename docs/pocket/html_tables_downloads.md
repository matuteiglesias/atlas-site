---
id: pocket_html_tables_downloads
title: "Tablas HTML de descargas"
slug: /pocket/html_tables_downloads
module: apendice-pocket
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [publishing, html, downloads, tables]
source_repo: <repo-url>
source_path: docs/pocket/html_tables_downloads.md
---

## Propósito
Estructuras de **tabla HTML** para exponer descargas por **base** (P/H/…) y **agrupador** (Total, AGLOSI, Región, PROV, DPTO, …). Este patrón ya está bosquejado en tus notas y conviene fijarlo para que todos publiquen igual. <!-- removed contentReference -->

## Plantilla mínima (basada en tus columnas)
~~~html
<table class="downloads">
  <thead>
    <tr>
      <th>Base</th>
      <th>Total</th>
      <th>AGLOSI</th>
      <th>AGLOMERADO</th>
      <th>Región</th>
      <th>PROV</th>
      <th>DPTO</th>
      <th>P0910</th>
      <th>Región AGLOSI</th>
      <th>PROV AGLOSI</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>P (Personas)</td>
      <td><a href="URL_TOTAL_P" download aria-label="P Total">Descargar</a></td>
      <td><a href="URL_AGLOSI_P" download aria-label="P AGLOSI">Descargar</a></td>
      <td><a href="URL_AGLOMERADO_P">Descargar</a></td>
      <td><a href="URL_REGION_P">Descargar</a></td>
      <td><a href="URL_PROV_P">Descargar</a></td>
      <td><a href="URL_DPTO_P">Descargar</a></td>
      <td><a href="URL_P0910_P">Descargar</a></td>
      <td><a href="URL_REGION_AGLOSI_P">Descargar</a></td>
      <td><a href="URL_PROV_AGLOSI_P">Descargar</a></td>
    </tr>
    <tr>
      <td>H (Hogares)</td>
      <td><a href="URL_TOTAL_H">Descargar</a></td>
      <td><a href="URL_AGLOSI_H">Descargar</a></td>
      <td><a href="URL_AGLOMERADO_H">Descargar</a></td>
      <td><a href="URL_REGION_H">Descargar</a></td>
      <td><a href="URL_PROV_H">Descargar</a></td>
      <td><a href="URL_DPTO_H">Descargar</a></td>
      <td><!-- P0910 no aplica a H --></td>
      <td><a href="URL_REGION_AGLOSI_H">Descargar</a></td>
      <td><a href="URL_PROV_AGLOSI_H">Descargar</a></td>
    </tr>
  </tbody>
</table>
~~~

Este es el esqueleto que ya venías usando; estandarizamos columnas y anotaciones de exclusión.&#x20;

### Una alternativa si hay muchas “bases”

* **Una tabla por base** (P, H, M24, PAGLO, Hp, Hi) para limitar scroll horizontal y sumar una breve leyenda por base.&#x20;

## Accesibilidad y UX (decisiones)

* **Texto del link**: “Descargar” + `aria-label` con base/agrupador (evita ambigüedades del lector de pantalla).
* **Claridad**: si algún agrupador **no aplica**, dejar celda vacía con comentario HTML explícito (evita 404 y confusiones).&#x20;
* **Formato/tamaño** (opcional): agregar `(<ext> • <MB>)` en el `title` o junto al link.
* **Leyenda breve** arriba/abajo con explicación de agrupadores para usuarios nuevos.&#x20;

## QA de enlaces (mínimo)

* Validar que todas las URLs referencian el **naming contract** del catálogo.
* Comprobar que los **agrupadores** de cada base coinciden con lo documentado (no inventar columnas vacías para “simetría”).

## CSS sugerido (compacto y legible)

~~~css
table.downloads { border-collapse: collapse; width: 100%; font-size: 0.95rem; }
.downloads th, .downloads td { border: 1px solid #ccc; padding: .35rem .5rem; }
.downloads th { background: #f3f3f3; text-align: left; }
.downloads td a { text-decoration: none; }
.downloads tr:nth-child(even) { background: #fafafa; }
~~~

> Ver también: **/metodos/charts\_and\_styles** → “Patrones de publicación/viz”.

~~~