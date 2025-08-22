---
id: pocket_categorical_colormaps
title: "Pocket — colormaps categóricos estables"
slug: /pocket/categorical_colormaps
module: apendice-pocket
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [charts, matplotlib, pandas, colormap, categorical]
source_repo: <repo-url>
source_path: docs/pocket/categorical_colormaps.md
---


Asegurar **colores estables por categoría** (y keys compuestas) en figuras reproducibles, evitando saltos al agregar/quitar grupos.

> Fuente: *cluster_0168 — Correcciones y mejoras para mapeos de color.*

---

## Contrato mínimo
**Entrada**: lista/serie de categorías (strings, ints o **tuplas**), nombre de colormap.  
**Salida**: `dict {categoria: "#RRGGBB"}` y utilidades de mapeo + leyenda.

---

## 1) Paleta determinística (orden estable)
Por defecto, asignamos colores en **orden alfabético** de categorías para garantizar estabilidad entre runs.

~~~python
from matplotlib import cm, colors

def stable_palette(categories, cmap_name="tab20", sort=True):
    # cast seguro a str para claves hashables
    cats = [str(c) for c in categories]
    cats = sorted(set(cats)) if sort else list(dict.fromkeys(cats))
    cmap = cm.get_cmap(cmap_name, len(cats))
    return {c: colors.to_hex(cmap(i)) for i, c in enumerate(cats)}
~~~

* `sort=True` → estable entre ejecuciones;
* `sort=False` → respeta primer aparición (útil en tablas ordenadas por ranking).

---

## 2) Keys compuestas (tuplas) → representación estable

Normalizamos tuplas a un string único y legible.

~~~python
def norm_key(k):
    return " | ".join(map(str, k)) if isinstance(k, tuple) else str(k)
~~~

Uso típico:

~~~python
# df["grp"] puede contener tuplas (prov, dpto) o strings
df["_grp_norm"] = df["grp"].map(norm_key)
pal = stable_palette(df["_grp_norm"].unique(), "tab20")
df["color"] = df["_grp_norm"].map(lambda k: pal.get(k, "#9e9e9e"))
~~~

> Evita `KeyError` con `.get(..., default)` y mantené un color “neutro” para categorías no mapeadas.

---

## 3) Aplicación en gráficos (bar/scatter) y leyenda estable

~~~python
import matplotlib.pyplot as plt
import matplotlib.patches as mpatches

# ejemplo bar
ax = df.sort_values("_grp_norm").plot.bar(
    x="_grp_norm", y="valor", color=df["color"].values, legend=False
)

# leyenda: proxies en el orden estable de la paleta
handles = [mpatches.Patch(color=col, label=lab) for lab, col in pal.items()]
ax.legend(handles=handles, title="Grupo", ncol=2, frameon=False)
plt.tight_layout()
~~~

---

## 4) Trampas comunes (y fixes)

* **Reasignación implícita** de colores al cambiar el subset → siempre derive colores desde la **paleta global estable**.
* **Keys no hashables** (listas) → normalizá a string con `norm_key`.
* **RGBA a hex**: usa `matplotlib.colors.to_hex(rgba)` para evitar formatos inconsistentes.
* **Colormap insuficiente** para muchas categorías → elegir `tab20`, `tab20b`, o generar paletas discretas con `get_cmap(..., N)`.

---

## Ver también

* `metodos/charts_and_styles.md` (política de escalas y estilos).
* `pocket/mapbox_overlay_patches.md` (si la asignación termina en JSON de estilos).
