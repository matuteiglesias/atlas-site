---
id: metodos_geo_integration_methods
title: "Integración geo — IGN + Censo, claves y joins"
slug: /metodos/geo_integration_methods
module: metodos
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [geo, ign, censo, crs, joins, geopandas, claves]
source_repo: <repo-url>
source_path: docs/metodos/geo_integration_methods.md
---


Guía operativa para **asociar datos socioeconómicos a geometrías** oficiales (IGN/CONICET) y **unir** niveles (provincia, departamento, fracción, radio/circuito) con claves normalizadas. El objetivo: reproducibilidad, joins sin pérdidas, y salidas que respeten las políticas **CRS** y de **exportación** del Atlas.

> Esta página documenta **cómo** integrar; las **fuentes y contratos** de capas viven en *Referencia → Geometrías geoespaciales*. No dupliques rutas ni checksums aquí.

---

## 1) Decisiones y supuestos
- **Geo anual**, hechos trimestrales: todo join con hechos usa `YEAR = year(Q)` vía adaptador temporal.
- **Claves normalizadas** antes del join: `PROV`, `DPTO` enteros tipados y **padding** fijo; `COD_2010` con `zfill(9)`.
- **CRS canónico** heredado de la referencia geo: re-proyectar a ese CRS **antes** de publicar; calcular áreas en CRS **métrico** (no geográfico).
- **Exclusiones**: filtrar Antártida e Islas (si aparecen en la capa fuente) según codificación oficial.
- **No adivinar** claves: si falta `COD_2010`/`IDFRAC`, construirlo **solo** con reglas documentadas (padding + composición), y registrar el transform.

---

## 2) Contratos mínimos (entradas / salidas)

### Entradas
- **Capas**: provincias, departamentos, fracciones, radios/circuitos (ver referencia geo).
- **Tablas de claves**: `radio_ref`, `radios_circuitos_secciones_ref`, `claves_dptos_ref`.
- **Datos socioeconómicos**: p. ej., `hogares_geo_YEAR` (dimensión) y hechos por hogar/persona (`*_Q`).

### Salidas
- **Dimensiones enriquecidas** (anuales): `hogares_geo_YEAR.parquet` con `PROV`, `DPTO`, `IDFRAC` y, si aplica, `COD_2010`, `circuito`.
- **GeoJSON de publicación** por nivel: `geo_departamentos_YEAR.geojson`, etc. (ver políticas en referencia geo).
- **Tablas analíticas**: merges listos para agregaciones territoriales (sin geometría), p. ej. `hogares_geo_keys_YEAR.parquet`.

**Garantías**
- Unicidad por nivel: `uniq(PROV)`, `uniq(PROV,DPTO)`, `uniq(IDFRAC)`, `uniq(COD_2010)`.
- Joins **left** desde el dataset socioeconómico hacia claves/geo; sin duplicaciones (auditar cardinalidad).

---

## 3) Normalización de claves (reglas)
- `PROV`, `DPTO`: tipar `int64` (o `string` con padding explicado, pero no mezclar).  
- `COD_2010`: `string` de **9** dígitos (`zfill(9)`), derivado del código de radio oficial.  
- `IDFRAC`: identificador compuesto (prov+depto+frac) con padding fijo (documentar longitudes).  
- **Dominio**: validar que cada código pertenezca a la lista oficial de la capa usada.

~~~python
# ejemplo mínimo
radio_ref['COD_2010'] = radio_ref['radio'].astype(str).str.zfill(9)
for c in ['PROV','DPTO']:
    radio_ref[c] = radio_ref[c].astype('int64')  # o string normalizado, pero consistente
~~~

---

## 4) Rutas de join típicas

**Por radio/circuito**

1. `RADIO_REF_ID` → `radio_ref` ⇒ trae `COD_2010`, `PROV`, `DPTO`.
2. `COD_2010` → `radios_circuitos_secciones_ref` ⇒ trae `circuito`, `IN1`.
3. `IN1` → `claves_dptos_ref` ⇒ trae ids/nombres electorales.

**Por departamento**

* `PROV + DPTO` → `departamentos` (geometría y metadatos).

**Por fracción**

* `IDFRAC` → `fracciones` (geometría y metadatos).

> Consejo: seleccionar **solo** columnas necesarias en cada paso para evitar duplicados y reducir memoria.

---

## 5) Pipeline mínimo (GeoPandas + Pandas)

~~~python
import geopandas as gpd, pandas as pd

# 1) Leer capas y re-proyectar al CRS canónico
dptos = gpd.read_file("/path/departamentos_2010.shp")
dptos = dptos.to_crs("EPSG:<canonico>")
dptos = dptos.query("NOMPROV != 'Antártida'")  # ejemplo de exclusión

# 2) Claves
radio_ref   = pd.read_csv("radio_ref.csv", usecols=["RADIO_REF_ID","PROV","DPTO","radio"])
radio_ref["COD_2010"] = radio_ref["radio"].astype(str).str.zfill(9)
radios_circ = pd.read_csv("radios_circuitos_secciones_ref.csv", usecols=["COD_2010","IN1","circuito"])
clv_dptos   = pd.read_csv("claves_dptos_ref.csv", usecols=["IN1","distrito_id","seccion_id"])

# 3) Base socioeconómica (dimensión hogares anual)
hog = pd.read_parquet("hogares_geo_2010.parquet")[["HOGAR_REF_ID","PROV","DPTO","RADIO_REF_ID"]]

# 4) Joins
m1 = hog.merge(radio_ref.drop(columns=["radio"]), on=["RADIO_REF_ID","PROV","DPTO"], how="left")
m2 = m1.merge(radios_circ, on="COD_2010", how="left").merge(clv_dptos, on="IN1", how="left")

# 5) Auditoría de cardinalidad y nulos
assert m2[["HOGAR_REF_ID"]].isna().sum().item() == 0
loss = len(hog) - len(m2)
print("perdidas por join:", loss)

# 6) Export analítico (sin geometría)
m2.to_parquet("hogares_geo_keys_2010.parquet", index=False)

# 7) Unión con geometría (por DPTO)
g = dptos[["PROV","DPTO","geometry"]]
out = g.merge(m2.groupby(["PROV","DPTO"]).agg(n_hog=("HOGAR_REF_ID","count")).reset_index(),
              on=["PROV","DPTO"], how="left")
out.to_file("geo_departamentos_2010.geojson", driver="GeoJSON")
~~~

---

## 6) QA geoespacial y de claves (checklist)

**Topología**

* Geometrías válidas (`make_valid` / `buffer(0)` si la librería lo soporta).
* Sin solapes entre polígonos del mismo nivel.
* `area_km2 > 0` y en rango razonable por nivel (sumas y outliers).

**Claves y joins**

* Unicidad de `PROV`, `PROV+DPTO`, `IDFRAC`, `COD_2010`.
* Porcentaje de `anti-join` < umbral (auditar y documentar excepciones).
* Sin **coerciones silenciosas** (p. ej., strings con padding vs ints).

**CRS**

* CRS de cálculo de área **proyectado**; CRS de publicación el **canónico** del atlas (web-friendly).
* Declarar CRS en metadatos de exportación; fallar si falta.

---

## 7) Performance y memoria

* Selección de columnas en cada join (`usecols`, `[['col1','col2']]`).
* Evitar `dissolve` innecesario; preferir `merge` por claves.
* Convertir atributos nominales a `category` en DataFrames grandes.
* Para pipelines intensivos, escribir intermedios en **Parquet** (compresión `snappy`/`zstd`).

---

## 8) Errores comunes (y cómo evitarlos)

* **Padding inconsistente** en `COD_2010` ⇒ joins vacíos → normalizar con `zfill(9)` antes.
* **CRS incorrecto para áreas** ⇒ valores absurdos → calcular áreas en CRS métrico dedicado.
* **Duplicaciones** por joins a tablas no deduplicadas → `drop_duplicates(keys)` antes del merge.
* **Mezclar año y trimestre** sin adaptador → usar `YEAR = year(Q)` en todos los cruces con hechos.

---

## 9) Publicación y trazabilidad

* **Analítico**: Parquet/Feather sin geometría; **Web**: GeoJSON con precisión controlada.
* Guardar `sha256`, `features` y `CRS` en metadatos de exportación (ver referencia geo).
* Trazar versión (`source_repo@commit`) y parámetros (año, filtros).

---

## 10) Anexo — Parqueados

**Recorte de imágenes satelitales (0169)**
Fuera del núcleo Atlas. Si se necesitara, documentar aparte (QGIS/GDAL/Rasterio) y **no** mezclar en este método de integración vectorial. Mantener aislado para que el flujo de joins IGN+Censo no se contamine con pipelines ráster.

~~~
