---
id: pocket_geo_plot_prechecks
title: "GeoPandas plot — pre-checks"
slug: /pocket/geo_plot_prechecks
module: apendice-pocket
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [geopandas, plotting, qa, geometry, merges]
source_repo: <repo-url>
source_path: docs/pocket/geo_plot_prechecks.md
---

## Propósito
Checklist rápido antes de plotear con **GeoPandas**: `geometry` activa, NaNs, CRS y merges que degradan tipos.

## Checklist (mínimo)
1) **NaNs en geometry**  
2) **Columna geometry activa** (`set_geometry`)  
3) **CRS consistente** entre capas  
4) **Merge que preserva geometry** (o reconstituir `GeoDataFrame`)  
5) **Plot de humo** para descartar aspect ratio/coords inválidas

## Snippets

### 1) NaNs y limpieza
~~~python
# conteo de NaNs
n_nan = gdf['geometry'].isna().sum()
if n_nan:
    print(f"[warn] {n_nan} geometrías NaN → se dropean para plot")
    gdf = gdf.dropna(subset=['geometry'])
~~~

### 2) Activar columna geometry tras un merge

~~~python
# si tras el merge la geometry quedó como object, reactivarla:
if 'geometry' in merged.columns:
    gdf = geopandas.GeoDataFrame(merged, geometry='geometry', crs=ref_gdf.crs)
else:
    raise ValueError("No se encontró columna 'geometry' luego del merge")
~~~

### 3) CRS y smoke-plot

~~~python
assert gdf.crs is not None, "CRS ausente: definir o heredar de la capa fuente"
ax = gdf.plot()  # smoke-plot
~~~

### 4) Merge que pierde geometry (patrón seguro)

~~~python
# preferible: unir atributos tabulares a la capa geo, no al revés
attrs = attrs_df.set_index('CLAVE')
gdf = ref_gdf.set_index('CLAVE').join(attrs, how='left').reset_index()
gdf = gdf.set_geometry('geometry')  # por si GeoPandas perdió el activo
~~~

## Pitfalls comunes

* **Aspect ratio ≤ 0** al plotear: suele ser **geometry NaN** o CRS roto.
* `merge` con `pandas.DataFrame` puede **degradar** la `GeoSeries` a `object`. Reconstruí el `GeoDataFrame`.

> Complementa: **/metodos/geo\_integration\_methods** (CRS canónico, claves y normalización).

~~~
(Ref: prechecks de NaN y `set_geometry` tras merges). <!-- removed contentReference -->
