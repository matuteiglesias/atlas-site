---
id: pocket_interactive_ts_js
title: "Time series interactivos (JS)"
slug: /pocket/interactive_ts_js
module: apendice-pocket
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [plotly, d3, chartjs, ts, web]
source_repo: <repo-url>
source_path: docs/pocket/interactive_ts_js.md
---

## Propósito
Micro-receta “embedding” para **series de tiempo** en HTML plano con **Plotly.js** (también D3/Chart.js). Flujo: `data → <div> → init`.

## HTML mínimo (div contenedor)
~~~html
<div id="myPlot"></div>
~~~

## CSS opcional

~~~html
<style>
  #myPlot { width: 80%; margin: 0 auto; height: 500px; }
</style>
~~~

## JS (Plotly: leer CSV y trazar)

~~~html
<script src="https://cdn.plot.ly/plotly-2.31.1.min.js"></script>
<script>
Plotly.d3.csv("path_to_data.csv", function(err, rows){
  if (err) { console.error(err); return; }

  const x = rows.map(r => r.date);            // columnas del CSV
  const y = rows.map(r => +r.poverty_level);  // cast a número

  const trace = { type: "scatter", x, y, name: "Poverty" };
  const layout = {
    title: "Poverty Level — Argentina",
    annotations: [{
      x: x[x.length-1], y: y[y.length-1],
      xref: "x", yref: "y", text: y[y.length-1]
    }]
  };
  Plotly.newPlot("myPlot", [trace], layout);
});
</script>
~~~

### Notas

* Podés **hostear** libs o usar CDN; en sitios estáticos simples, CDN es suficiente.
* Si necesitás marcas animadas/custom, **D3** te da control sobre SVG.
* El formato de datos puede ser **CSV o JSON**; Plotly/D3 manejan ambos.

> Ejemplos más largos: ver **/metodos/charts\_and\_styles** (sección de publicación/embedding).