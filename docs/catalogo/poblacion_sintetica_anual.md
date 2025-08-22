---
id: poblacion_sintetica_anual
title: "Poblacion sintetica por año"
slug: /catalogo/poblacion_sintetica_anual
module: catalogo
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [poblacion, sintetica, anual, frac, muestreo]
source_repo: <repo-url>
source_path: docs/catalogo/poblacion_sintetica_anual.mdx
schema_ref: /downloads/atlas.dbml#personas_geo
time_grain: A
join_policy: "Geo anual. Si se cruza con hechos trimestrales, usar YEAR = year(Q)."
---


Dataset de poblacion sintetica anual generado a partir de insumos censales y de EPH. Se utiliza para pruebas, muestreos de alta velocidad y validaciones de consistencia espacial.

## Contrato de exportacion
- Patron de nombre: `table_f[FRAC]_[YEAR]_[COUNTRY].csv`
  - `[FRAC]`: fraccionamiento usado en la generacion, ejemplo `0.005`
  - `[YEAR]`: año de referencia, ejemplo `2015`
  - `[COUNTRY]`: ISO3 pais, ejemplo `ARG`
- Ejemplo: `table_f0.005_2015_ARG.csv`

~~~yaml
exports:
  - path: /exports/table_f0.005_2015_ARG.csv
    sha256: "<sha256>"
    rows: "<n>"
    year: 2015
    country: "ARG"
    frac: 0.005
~~~

## Columnas minimas

TODO completar segun tu pipeline actual. Sugerencia de base:

* `ID` string. Identificador de persona sintetica
* `HOGAR_REF_ID` string. Identificador de hogar
* `ANO4` int. Año
* `PROV`, `DPTO`, `AGLOMERADO`, `RADIO_REF_ID` claves geo
* Atributos demograficos y laborales segun version de generacion

## Dependencias

* Censo 2010 variables normalizadas
* Armonizacion Censo EPH
* Deflactores e insumos para escalas de equivalencia si aplica

## QA minimo

1. Unicidad por `ID` y cobertura de `HOGAR_REF_ID`
2. Dominio de claves geo y consistencia con capas anuales
3. `frac` consistente con tamaño observado
4. Documentar cualquier reponderacion posterior a la generacion

## Edge cases

* Formato de `FRAC`: usar punto decimal. Si se ofrece alias `f005`, documentar equivalencia y no mezclar estilos
* Versionado: si cambia la logica de generacion, emitir nuevo archivo y registrar `sha256` distinto

## Referencias cruzadas

* DBML fuente de verdad
* Geometrias geoespaciales
* Playbook de orquestacion de muestreos

~~~
