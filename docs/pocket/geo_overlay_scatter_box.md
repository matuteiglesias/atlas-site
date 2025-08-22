---
id: pocket_geo_overlay_scatter_box
title: "Scatter + box ponderado (overlay)"
slug: /pocket/geo_overlay_scatter_box
module: apendice-pocket
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [matplotlib, weighted, boxplot, scatter, bins, ticks]
source_repo: <repo-url>
source_path: docs/pocket/geo_overlay_scatter_box.md
---

## Propósito
Superponer **scatter** (puntos ponderados) y **box plots ponderados** por bins de ingreso, compartiendo eje X; **Y en %** y **X en k**.

## Requisitos
- `weighted_quantile` (q1/q2/q3 con pesos).  
- `ingreso_medio` por observación; `votos_porcentaje` y `votos_cantidad`.  
- Bins de ingreso (p.ej., `np.arange(10_000, 200_000, 10_000)`).

## Overlay mínimo (scatter + box ponderado)
~~~python
def eng_k(x, _): return f'{x*1e-3:.0f}k'

def plot_scatter(ax, df, color_map):
    ax.scatter(df['ingresos'],
               df['votos_porcentaje']*100,      # a %
               s=df['votos_cantidad']/70,        # ponderación
               c=color_map.get(df['agrupacion_nombre_'].iat[0], 'black'),
               alpha=0.15, edgecolors="w", lw=0.5)
    ax.set_xlabel('Ingresos (AR$)'); ax.set_ylabel('Votos (%)')
    ax.xaxis.set_major_formatter(eng_k)
    ax.grid(True, ls='--', lw=0.5)

def plot_weighted_box(ax, df, bins):
    df = df.assign(x_bins=pd.cut(df.ingresos, bins=bins, labels=False))
    qs, xs = [], []
    for _, g in df.groupby('x_bins'):
        if g.empty: continue
        q1 = weighted_quantile(g.votos_porcentaje, .25, sample_weight=g.votos_cantidad)*100
        q2 = weighted_quantile(g.votos_porcentaje, .50, sample_weight=g.votos_cantidad)*100
        q3 = weighted_quantile(g.votos_porcentaje, .75, sample_weight=g.votos_cantidad)*100
        qs.append((q1,q2,q3)); xs.append(g.ingresos.mean())
    for x,(q1,q2,q3) in zip(xs, qs):
        ax.plot([x,x],[q1,q3], color='.3', lw=1.5, alpha=.6)
        w = 0.2*(bins[1]-bins[0])
        ax.plot([x-w, x+w], [q1,q1], color='.3', lw=1.5, alpha=.6)
        ax.plot([x-w, x+w], [q3,q3], color='.3', lw=1.5, alpha=.6)
        ax.plot(x, q2, 'k*', alpha=.7)
    ax.xaxis.set_major_formatter(eng_k); ax.set_ylabel('Votos (%)'); ax.grid(True, axis='y', ls='--', lw=0.5)
~~~

### Consejos

* **Cortar bins con baja N** (o sombrearlos).
* Mantener **escala X** estable entre gráficos.
* Usar **alpha bajo** en scatter para densidades altas.

~~~
(Bases: overlay con medias de bin para posicionar cajas; % en Y y k-ticks en X; y cuantiles ponderados). <!-- removed contentReference -->

