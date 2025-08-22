---
id: pocket_chart_scaffold
title: "Chart scaffold (Matplotlib)"
slug: /pocket/chart_scaffold
module: apendice-pocket
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [charts, matplotlib, scaffold, rutina]
source_repo: <repo-url>
source_path: docs/pocket/chart_scaffold.md
---

## Propósito
Plantilla mínima y estable para **gráficos Matplotlib**: `figure → plot(s) → title/xlabel/ylabel → legend → grid → xticks → tight_layout → show`. Útil para asegurar consistencia y evitar “gráficos crudos”.

## Secuencia base (línea temporal)
~~~python
import matplotlib.pyplot as plt

fig = plt.figure(figsize=(10, 6))     # 1) lienzo
ax  = plt.gca()                       # 2) eje

# 3) una o más series
ax.plot(x, y, label="Serie")

# 4) títulos y ejes
ax.set_title("Título")
ax.set_xlabel("Fecha")
ax.set_ylabel("Valor")

# 5) leyenda y ayudas visuales
ax.legend()
ax.grid(True)

# 6) formateo de ticks (cuando aplica)
plt.xticks(rotation=45)

# 7) ajuste y render
plt.tight_layout()
plt.show()
~~~

### Variantes frecuentes

* **Comparar original vs. fitted vs. rolling**: llamar `ax.plot(...)` múltiples veces con `label` distintos; mantener ejes y leyenda.
* **Paneles STL** (trend, seasonal, resid): `plt.figure(...)` y `plt.subplot(3,1,k)` si necesitás 3 paneles con títulos por panel.
* **Foco trimestral**: si el índice es Q, rotar ticks y usar grid fino.

> Para políticas de estilo (tipos de ejes, escalas de color, hatch en mapas), ver **/metodos/charts\_and\_styles**.
