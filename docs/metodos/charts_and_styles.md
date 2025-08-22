---
id: metodos_charts_and_styles
title: "Charts & estilos — tablas, escalas y diagramas"
slug: /metodos/charts_and_styles
module: metodos
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [charts, estilos, mapbox, color, graphviz]
source_repo: <repo-url>
source_path: docs/metodos/charts_and_styles.md
---

 y decisiones
Guía unificada para (a) **tablas estilizadas** en notebooks/reportes, (b) **escalas de color e interpolación** para Mapbox (fill-color), y (c) **convenciones de diagramación** para procesos y flujos de datos.

**Decisiones (consolidadas)**
- **Un solo contrato de escalas por métrica** (rangos lineales predefinidos + `linspace`) y **parcheo determinista** de `interpolate_list`. Deprecamos “listas ad-hoc” de stops sin doc. <!-- removed contentReference -->
- **Presentación acoplada tabla+gráfico** (cuando corresponda), y **overlays** en mapas con transparencia controlada. <!-- removed contentReference -->
- **Diagramas con símbolos/colores consistentes** y uso preferente de **graphviz** para reproducibilidad. <!-- removed contentReference -->

---

## 1) DataFrames estilizados (barras, notación)
**Objetivo:** mejorar lectura rápida de tablas en notebooks y reportes impresos.

**Patrones recomendados**
- **Barras horizontales** sobre columnas cuantitativas (percentiles o magnitudes).  
- **Formato**: si la magnitud lo requiere, notación **ingenieril** o **científica** con sufijos (`K`, `M`).  
- **Ordenación y subtotales** por `grouper` antes del estilo (evita que el formateo oculte outliers).

**Snippet (pandas Styler)**
~~~python
def style_table(df, cols_pct=None, cols_num=None):
    s = df.copy()
    if cols_pct:
        s[cols_pct] = s[cols_pct].astype(float)
    if cols_num:
        s[cols_num] = s[cols_num].astype(float)
    sty = s.style
    if cols_pct:
        sty = sty.bar(subset=cols_pct, color='#DDEBF7', align='zero')
        sty = sty.format({c: '{:.1%}' for c in cols_pct})
    if cols_num:
        sty = sty.bar(subset=cols_num, color='#FCE4D6')
        sty = sty.format({c: '{:,.0f}' for c in cols_num})
    return sty.set_table_styles([{'selector': 'th', 'props': 'text-align:center;'}])
~~~

**Tabla y gráfico “pegados” en el output**: mostrar la tabla **justo debajo** del plot para cada `distrito/sección` mejora el contexto y reduce scroll.&#x20;

---

## 2) Mapas (Matplotlib) — overlay con transparencia

Para inspecciones rápidas sin stack web: **dibujar geometrías** y **superponer** un mosaico base con `imshow` usando los **bounds** del `Axes`, y aplicar **alpha** en la capa vectorial.&#x20;

~~~python
fig, ax = plt.subplots(figsize=(10,6))
circuitos_gdf.dropna().plot(ax=ax, alpha=0.5)  # capa vectorial
minx, maxx = ax.get_xlim(); miny, maxy = ax.get_ylim()
image = get_maps_image(minx, miny, maxx, maxy)  # recupera raster de fondo
ax.imshow(image, extent=[minx, maxx, miny, maxy], aspect='equal')
~~~

**Regla:** el mapa base **no** debe tapar la simbología; controlarlo con `alpha` en la capa vectorial.&#x20;

---

## 3) Mapbox — políticas de escalas y “interpolate”

Estandarizamos **rangos lineales** por métrica y la **inserción** de stops en `interpolate_list`:

**Rangos sugeridos**

* **Pobreza**: 0 → 0.60
* **Indigencia**: 0 → 0.25
* **Canasta alimentaria (CBA)**: 0 → 300 000
* **Canasta total (CBT)**: 0 → 600 000
* **Ingreso mediano**: 0 → 300 000
* **Ingreso total familiar**: 0 → 600 000
  Estos rangos vienen de la práctica en tus cuadernos y se implementan con `np.linspace` para `n_stops` uniforme.&#x20;

**Generación de stops**

~~~python
# ejemplo reducida
if 'Pobreza' in title:
    linspace_values = np.linspace(0, 0.6, n_stops)
elif 'Indigencia' in title:
    linspace_values = np.linspace(0, 0.25, n_stops)
elif 'Canasta Alimentaria' in title:
    linspace_values = np.linspace(0, 300000, n_stops)
# ... (resto de métricas)
~~~



**Inserción/actualización en `interpolate_list`**
La estructura típica de `fill-color` es:

~~~
['interpolate', ['linear'], ['get', VAR],
  v1, '#hex1', v2, '#hex2', ...]
~~~

El reemplazo correcto es **antes de cada color hex** o sobrescribiendo cada valor numérico tras el `['get', VAR]`. Evitar reemplazar colores o el `get`.&#x20;

~~~python
def update_values(interpolate_list, linspace_values):
    is_next_val_scale, color_idx, i = False, 0, 0
    while i < len(interpolate_list):
        val = interpolate_list[i]
        if isinstance(val, list) and val[0] == 'get':
            is_next_val_scale = True
        elif is_next_val_scale and isinstance(val, (int, float)):
            interpolate_list[i] = linspace_values[color_idx]; color_idx += 1
        elif is_next_val_scale and isinstance(val, str) and val.startswith("#"):
            is_next_val_scale = False
        i += 1
    return interpolate_list
~~~

Este patrón refleja la **detección del `get`** y el **recorrido seguro** de stops, con variantes donde se **inserta** el valor numérico inmediatamente **antes** del color si la lista carece de stops explícitos.&#x20;

**Depuración**: trazar con `print` las decisiones (`Found 'get'`, `Replacing value…`) facilita validar que solo tocás los stops y no los colores.&#x20;

---

## 4) Convenciones de diagramación (swimlanes, graphviz)

**Objetivo:** comunicar pipelines y responsabilidades con **claridad** y **consistencia**.

**Símbolos**

* **Rectángulos** = procesos/operaciones; **paralelogramo** = entrada/salida; **diamante** = decisión; **óvalo** = inicio/fin; **flechas** = flujo.&#x20;

**Capas y swimlanes**

* **Swimlanes** (por equipo o etapa) y **layers** (por fase) mejoran la lectura en flujos complejos.&#x20;

**Herramientas**

* **Graphviz** (recomendado para versionar), o Visio/Lucid/draw\.io para diagramas manuales; Airflow/Prefect si se genera desde orquestadores.&#x20;

**Snippet (DOT mínimo)**

~~~dot
digraph atlas_pipeline {
  rankdir=LR; node [shape=rectangle, style=rounded];
  subgraph cluster_ingesta { label="Ingesta"; n1[label="EPH"]; n2[label="Censo"]; }
  subgraph cluster_etl { label="ETL"; e1[label="Preprocesar"]; e2[label="Merges"]; }
  subgraph cluster_modelo { label="Modelo"; m1[label="Entrenar"]; m2[label="Publicar métricas"]; }
  n1 -> e1 -> e2 -> m1 -> m2;
}
~~~

---

## 5) Accesibilidad, leyendas y estilos

* **Accesibilidad**: evitar pares rojo/verde sin textura; asegurar contraste AA.
* **Leyenda**: orden de bins consistente (menor→mayor), y **texto corto** con umbrales explícitos.
* **Texturas/Hatches**: usar “///”, “//”, “/” para densidad cualitativa en mapas monócromos (fallback impresión).

---

## 6) QA de visualizaciones (checklist)

1. **Escala correcta**: rangos y `n_stops` acordes a la métrica; validar `fill-color` después del parcheo.&#x20;
2. **Overlay**: el mapa base no tapa la simbología (ver `alpha`).&#x20;
3. **Tabla/plot acoplados**: tabla debajo del gráfico cuando se itera por `distrito/sección`.&#x20;
4. **Convenciones**: símbolos y colores respetan la guía (rectángulo/proceso, diamante/decisión).&#x20;
5. **Determinismo**: el mismo dataset produce los mismos stops (linspace) y la misma leyenda.

---

## 7) Deprecaciones

* Dejado de usar **paletas manuales dispersas** y **stops no documentados**.
* Evitar scripts que mutan `interpolate_list` sin detectar `['get', VAR]` primero.&#x20;
