---
id: pocket_dataframe_styling
title: "DataFrames con barras (Styler)"
slug: /pocket/dataframe_styling
module: apendice-pocket
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [pandas, styler, barras, formato]
source_repo: <repo-url>
source_path: docs/pocket/dataframe_styling.md
---

## Propósito
Snippets cortos para **barras horizontales** y **notación ingenieril** en `pandas.DataFrame.style`. Útil para tabulados “de lectura rápida” en notebooks.

## Barras alineadas en cero (rango global)
~~~python
def style_with_bars(df, cols):
    vmax = df[cols].abs().max().max()
    return (df.style
              .bar(subset=cols, align="zero", vmin=-vmax, vmax=vmax, width=100))
~~~

## Formateo en notación ingenieril (k/M/G)

~~~python
def eng(x):
    if x == 0 or x is None: return "0"
    mags = "kMGTPEZY"
    m = 0
    while abs(x) >= 1000 and m < len(mags):
        x /= 1000.0; m += 1
    return f"{x:.0f}{mags[m-1]}" if m else f"{x:.0f}"

def style_bars_eng(df, cols):
    s = style_with_bars(df, cols)
    return s.format(eng, subset=cols)
~~~

### Tips operativos

* **vmax global** para comparabilidad entre columnas.
* Añadí `.format("{:.0e}")` si preferís científica.
* Si el DF es grande, **muestra** pocas columnas para performance.

> Para guías completas y escalas de color en Mapbox, ver **/metodos/charts\_and\_styles**.

