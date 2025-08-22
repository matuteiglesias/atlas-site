---
id: pocket_mapbox_overlay_patches
title: "Pocket — parches de estilo (Mapbox)"
slug: /pocket/mapbox_overlay_patches
module: apendice-pocket
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [mapbox, json, style, color, interpolate, tiles, sources]
source_repo: <repo-url>
source_path: docs/pocket/mapbox_overlay_patches.md
---


Aplicar **parches rápidos** a estilos Mapbox: actualizar **`source`/`data`** y redefinir **`fill-color`** con `interpolate` (stops ordenados).

> Fuente: *cluster_0191 — Integración de color/escala en JSON de estilos.*

---

## Contrato mínimo
**Entrada**: `style` (dict leído desde `.json`), `layer_id` objetivo, stops `[(x1,"#hex1"), ...]`.  
**Salida**: `style` modificado (se recomienda escribir a un nuevo archivo).  
**Garantías**: no mutar en sitio (deep copy) y **ordenar** stops por valor ascendente.

---

## 1) Utilidades básicas (layers & sources)

~~~python
import copy

def find_layer(style, layer_id):
    for ly in style.get("layers", []):
        if ly.get("id") == layer_id:
            return ly
    raise KeyError(f"Layer '{layer_id}' no encontrado")

def update_source_url(style, source_id, new_value):
    s = style.get("sources", {}).get(source_id)
    if not s:
        raise KeyError(f"Source '{source_id}' no encontrado")
    # vector tiles → url; geojson → data
    if s.get("type") == "vector":
        s["url"] = new_value
    elif s.get("type") == "geojson":
        s["data"] = new_value
    else:
        # si es raster/otros, cubrir casos según tu catálogo
        s["url"] = new_value
~~~

---

## 2) `fill-color` con `interpolate` (lineal)

Expresión canónica:

~~~json
["interpolate", ["linear"], ["get", "<prop>"], x1, "#hex1", x2, "#hex2", ...]
~~~

Helper seguro:

~~~python
def set_fill_color_linear(style, layer_id, prop, stops):
    st = copy.deepcopy(style)
    ly = find_layer(st, layer_id)
    ly.setdefault("paint", {})
    # stops ordenados y únicos
    stops_sorted = sorted({float(x): str(c) for x, c in stops}.items())
    expr = ["interpolate", ["linear"], ["get", prop]]
    for x, c in stops_sorted:
        expr += [x, c]
    ly["paint"]["fill-color"] = expr
    return st
~~~

Uso:

~~~python
# p. ej., para 'valor' entre 0 y 100
st2 = set_fill_color_linear(style, "departamentos-fill", "valor",
                            [(0, "#f7fbff"), (25, "#c6dbef"), (50, "#6baed6"), (75, "#2171b5"), (100, "#08306b")])
~~~

---

## 3) Parches combinados: source + color

~~~python
def patch_style(style, layer_id, prop, stops, source_id=None, new_source=None):
    st = copy.deepcopy(style)
    if source_id and new_source:
        update_source_url(st, source_id, new_source)
    return set_fill_color_linear(st, layer_id, prop, stops)
~~~

---

## 4) QA rápido

* **Existencia**: `layer_id` y `source_id` válidos.
* **Monotonía**: stops ordenados ascendente; valores únicos.
* **Tipo de layer**: `fill-color` solo para layers `fill`.
* **Propiedad**: `["get", prop]` debe existir en el source (o el color caería al primer/último stop).
* **Salida**: persistir con `ensure_ascii=False` y backup del `.json` original.

~~~python
import json, pathlib
path_out = pathlib.Path("style_patched.json")
with path_out.open("w", encoding="utf-8") as f:
    json.dump(st2, f, ensure_ascii=False, indent=2)
~~~

---

## 5) Trampas comunes (y fixes)

* **`KeyError`** en búsquedas anidadas → usar helpers y `setdefault`.
* **Reemplazo parcial** de la expresión → preferible rearmar toda la lista `interpolate`.
* **Escala inconsistente** con los datos → calcular rangos (min/percentiles) antes de definir stops.
* **Hex inválidos** o mezclas RGBA/hex → normalizar a `#RRGGBB` antes de inyectar.

---

## Ver también

* `metodos/charts_and_styles.md` (política de escalas, `linspace`, reemplazo seguro).
* `pocket/categorical_colormaps.md` (paletas discretas consistentes en el lado Python).
