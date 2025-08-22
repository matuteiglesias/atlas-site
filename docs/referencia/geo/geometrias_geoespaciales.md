---
id: referencia_geometrias_geoespaciales
title: ""Geometrías geoespaciales: provincias, departamentos, fracciones""
slug: /referencia/geo/geometrias_geoespaciales
module: referencia-geo
version: 0.2.0
status: draft
owners: [atlas-core]
tags: [geo, ign, conicet, shapefiles, geojson, claves, crs]
source_repo: <repo-url>
source_path: docs/referencia/geo/geometrias_geoespaciales.mdx
schema_ref: /downloads/atlas.dbml#hogares_geo
time_grain: A
join_policy: "Geo anual. Joins con hechos trimestrales via YEAR = year(Q) y adaptador temporal."
---


Límites geoespaciales de provincias, departamentos y fracciones para la Argentina. Estos insumos son la base de:
- unión con `hogares_geo` y `personas_geo`
- generación de GeoJSON por nivel para visualización y agregaciones territoriales
- cómputo de métricas por recorte espacial

## Fuentes y rutas esperadas
- Provincias: `./../../geoespacial-censo-IGN/IGN_shp/ign_provincia`
- Departamentos: `./../../geoespacial-censo-IGN/censos_shp_CONICET_dissolved/dptos_2010.shp`
- Fracciones: `./../../geoespacial-censo-IGN/censos_shp_CONICET_dissolved/fracs_2010.shp`

> Nota: mantener estas rutas como variables de configuración del build si difieren entre ambientes.

## Variables clave y tipos
- `PROV` int64. Código de provincia.
- `DPTO` int64. Código de departamento.
- `IDFRAC` string. Identificador compuesto de fracción.
- `geometry` geometry. Multipolygon o polygon según capa.
- `area_km2` float64. Área de la división en km².

Atributos adicionales presentes en ciertas capas (p. ej. provincias) como `OBJECTID, Entidad, Objeto, FNA, GNA, NAM, SAG, FDC, IN1, SHAPE_STAr, SHAPE_STLe` no son parte del contrato básico. Documentar solo si se usan.

## Claves de unión con el Atlas
- Con `hogares_geo` y `personas_geo`:
  - nivel provincia: `PROV`
  - nivel departamento: `PROV + DPTO`
  - nivel fracción: `IDFRAC` (definición exacta abajo)
- Política temporal: geo es anual. Al unir con hechos trimestrales (`Q`) usar adaptador `YEAR = year(Q)`.

### Especificación de claves compuestas
- `IDFRAC`: construir a partir de códigos normalizados de provincia, departamento y fracción.
  - Reglas de padding y longitudes fijas: completar con la especificación de normalización de códigos (ver “Normalización de códigos y casos especiales” en CoreRef).
  - Validar unicidad de `IDFRAC` por año.

## Política CRS y reproyección
- CRS canónico para publicación de GeoJSON: **definir aquí** (recomendado WGS84 EPSG:4326 para web).
- Capa fuente puede venir en CRS proyectado. Reproyectar a CRS canónico antes de:
  - cálculo de `area_km2` (hacerlo en CRS proyectado métrico, no geográfico)
  - serialización a GeoJSON
- Declarar CRS en metadatos de exportación. Fallar el build si falta.

## Contrato de exportación
Exportables canónicos por nivel y año:
~~~yaml
exports:
  - path: /exports/geo_provincias_2010.geojson
    sha256: "<sha256-prov-2010>"
    crs: "EPSG:<definir>"
    features: "<n>"
  - path: /exports/geo_departamentos_2010.geojson
    sha256: "<sha256-dpto-2010>"
    crs: "EPSG:<definir>"
    features: "<n>"
  - path: /exports/geo_fracciones_2010.geojson
    sha256: "<sha256-frac-2010>"
    crs: "EPSG:<definir>"
    features: "<n>"
~~~

Reglas:

* codificación UTF-8
* precisión de coordenadas configurable (p. ej. 6 decimales)
* validación de geometrías antes de exportar (repair mínimo si es necesario)

## Pipeline mínimo sugerido

1. Leer shapefiles de IGN/CONICET.
2. Verificar CRS y reprojectar a CRS canónico.
3. Normalizar códigos (`PROV`, `DPTO`) y construir `IDFRAC`.
4. Calcular `area_km2` en CRS proyectado métrico.
5. QAs topológicos. Ver sección QA.
6. Serializar a GeoJSON con propiedades mínimas y metadatos de origen.

## QA de integridad y topología

Fallar el build si:

1. Geometrías inválidas sin reparar: usar `buffer(0)` o `make_valid` y reintentar.
2. Self-intersections o rings mal orientados que impidan el dissolve.
3. Huecos extremadamente pequeños que generen slivers tras disolver.
4. Duplicados de clave:

   * `uniq(PROV)` en provincias
   * `uniq(PROV, DPTO)` en departamentos
   * `uniq(IDFRAC)` en fracciones
5. `area_km2` no positiva o fuera de rango razonable por nivel.
6. CRS ausente o inconsistente con el declarado en `exports`.

Checks recomendados:

* Diferencias de bounding box respecto a expectativas por nivel.
* Intersecciones entre polígonos del mismo nivel (no deberían solaparse).
* Suma de `area_km2` por nivel frente a benchmark nacional.

## Uso en Atlas

* Unión con `hogares_geo` para enriquecer métricas:

  * anual: `hogares_geo.YEAR` debe coincidir con el año de la capa
  * trimestral: usar `YEAR = year(Q)` en joins con hechos
* Generación de GeoJSON con métricas: agregar columnas de indicadores y exportar a `downloads/` o `exports/` según política de publicación.

## Ejemplo de join y export (pseudocódigo)

~~~python
# geopandas
g = geopandas.read_file("/exports/geo_departamentos_2010.geojson")
h = pandas.read_parquet("/exports/hogares_geo_2010.parquet")
agg = (pobreza_hogares
       .assign(YEAR=lambda d: d["Q"].dt.year)
       .merge(h[["HOGAR_REF_ID","YEAR","PROV","DPTO"]],
              on=["HOGAR_REF_ID","YEAR"], how="left")
       .groupby(["PROV","DPTO","YEAR"], as_index=False)
       .agg(tasa_pobreza=("Pobreza","mean"), n=("HOGAR_REF_ID","size")))
out = g.merge(agg, on=["PROV","DPTO"], how="left")
out.to_file("/exports/mapa_dpto_pobreza_2010.geojson", driver="GeoJSON")
~~~

## Errores comunes y cómo evitarlos

* Usar CRS geográfico para áreas. Calcular `area_km2` en CRS proyectado métrico.
* Mezclar granularidad anual con trimestral sin adaptador temporal.
* No fijar padding y normalización de códigos y luego fallar joins por strings incompatibles.
* Exportar GeoJSON con demasiada precisión o sin simplificación controlada, lo que infla el tamaño y afecta rendimiento.

## Licencia y citación

* Indicar organismo proveedor, año de referencia y términos de uso (IGN, CONICET).
* Recomendar citar la fuente y el hash del archivo exportado.

## Pendientes

* Completar CRS canónico exacto.
* Documentar padding y formato de `IDFRAC` con ejemplos reproducibles.
* Añadir checksums reales en `exports`.

~~~


