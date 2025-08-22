---
id: pocket_json_style_color_mapbox
title: "JSON style — color & scale (Mapbox)"
slug: /pocket/json_style_color_mapbox
module: apendice-pocket
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [json, mapbox, color, scale, interpolate, fill-color]
source_repo: <repo-url>
source_path: docs/pocket/json_style_color_mapbox.md
---

## Propósito
Parchear **`fill-color`** en estilos de Mapbox GL: actualizar *stops* y escalas sin romper la estructura de la expresión (`interpolate`, `linear`, `['get', <métrica>]`). Incluye helpers para **reemplazo en listas anidadas**. <!-- removed contentReference -->

> Para criterios de escalas, rangos y estilo general, ver **/metodos/charts_and_styles.md** (política de color scales y `linspace`).

## Anatomía de `fill-color`
Mapbox usa expresiones tipo:
~~~json
"paint": {
  "fill-color": [
    "interpolate", ["linear"], ["get", "metric"],
    10000, "#fef0d9",
    25000, "#fdcc8a",
    40000, "#fc8d59",
    55000, "#d7301f"
  ]
}
~~~

Tu tarea suele ser **recalcular los cortes** (stops) y **mapear colores** manteniendo el *shape* de la lista.&#x20;

## Recetas

### 1) Extraer y analizar la expresión

~~~python
def get_fill_color_expr(style_json, layer_id):
    layer = next(l for l in style_json["layers"] if l["id"] == layer_id)
    return layer["paint"]["fill-color"]  # lista con ["interpolate", ..., stops...]
~~~

Verificá que el patrón sea `["interpolate", ["linear"], ["get", <metric>], ...]` antes de manipular.&#x20;

### 2) Reescribir *stops* (numéricos) manteniendo colores

~~~python
def rebuild_stops(values, colors):
    """values: lista de cortes (p.ej., linspace); colors: lista hex (mismo largo)."""
    out = []
    for v, c in zip(values, colors):
        out += [float(v), c]
    return out

def patch_fill_color(style_json, layer_id, metric, values, colors):
    expr = get_fill_color_expr(style_json, layer_id)
    assert expr[0] == "interpolate" and expr[1] == ["linear"], "expr no-lineal"
    # cabeza = ["interpolate", ["linear"], ["get", metric]]
    head = ["interpolate", ["linear"], ["get", metric]]
    expr_new = head + rebuild_stops(values, colors)
    # set en objeto
    for l in style_json["layers"]:
        if l["id"] == layer_id:
            l["paint"]["fill-color"] = expr_new
            break
~~~

Sugerencia: generá `values` con `linspace` y redondeo consistente (ver Pocket `linspace_rounding`).&#x20;

### 3) Reemplazo selectivo de colores en listas anidadas

A veces necesitás **parchar colores** sin tocar los valores:

~~~python
def replace_color_in_nested_list(obj, old_hex, new_hex):
    if isinstance(obj, list):
        return [replace_color_in_nested_list(x, old_hex, new_hex) for x in obj]
    return new_hex if obj == old_hex else obj
~~~

Útil cuando el estilo tiene múltiples capas con colores repetidos.&#x20;

## Contrato mínimo

* **No** cambiar el orden/cabeza de la expresión (`interpolate` → `linear` → `get metric`).
* **Paridad**: `len(values) == len(colors)` y stops monotónicos.
* **Persistencia**: guardar el estilo con UTF-8 y *pretty-print* controlado (evitar ruido en diffs).&#x20;

## QA rápido

* Validar que `fill-color[0:3] == ["interpolate", ["linear"], ["get", metric]]`.
* Asserts de **longitud** y **monotonía** de `values`.
* Probar en un mapa mínimo (fixture) antes de publicar.
