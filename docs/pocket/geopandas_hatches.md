---
id: pocket_geopandas_hatches
title: "Hatches en GeoPandas"
slug: /pocket/geopandas_hatches
module: apendice-pocket
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [geopandas, matplotlib, hatch, leyenda, transparencia]
source_repo: <repo-url>
source_path: docs/pocket/geopandas_hatches.md
---

## Propósito
Patrones de **hatch** en mapas (p. ej., `'/'`, `'//'`, `'///'`), con **leyendas correctas** y **overlay** para controlar colores/alpha.

## Densidad y leyenda
~~~python
import matplotlib.patches as mpatches

base_colors = {
  "capas_A": ("lightblue",  "///"),
  "capas_B": ("lightcoral", "\\\\\\"),
  "capas_C": ("lightgreen", "|||"),
}

legend_items = []
for name, (color, hatch) in base_colors.items():
    sel = merged.loc[merged[f"{name}_flag"] > 0]
    # 1) trazar hatch (sin cara) con borde del color
    sel.plot(ax=ax, color='none', edgecolor=color, hatch=hatch)
    legend_items.append(mpatches.Patch(facecolor=color, hatch=hatch, label=name, edgecolor=color))

# 2) overlay de color (sin hatch) con alpha para dejar ver las rayas
for name, (color, _) in base_colors.items():
    sel = merged.loc[merged[f"{name}_flag"] > 0]
    sel.plot(ax=ax, color=color, edgecolor='none', alpha=0.4)

ax.legend(handles=legend_items, loc='upper left')
~~~

### Notas operativas

* La **leyenda** requiere `Patch` (no `Line2D`) para mostrar el hatch.
* Si querés **rayas y relleno** con colores distintos, el **doble plot** (arriba) es la vía práctica.
* Ajustá densidad con `'/'` → `'//'` → `'///'` según visibilidad y solapes.

~~~
(Trucos: densidad `'/' '//' '///'`, leyendas con `Patch`, y overlay con alpha para que el hatch sea visible). <!-- removed contentReference -->

