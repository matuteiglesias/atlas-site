---
id: referencia_nucleo_dbml_fuente_de_verdad
title: "DBML — Fuente de verdad para pobreza y hogares"
slug: /referencia/nucleo/dbml_fuente_de_verdad
module: referencia-nucleo
version: 0.2.0
status: draft
owners: [atlas-core]
tags: [dbml, schema, claves, hogares, personas, geo, pobreza]
source_repo: <repo-url>
source_path: docs/referencia/nucleo/dbml_fuente_de_verdad.mdx
schema_ref: /downloads/atlas.dbml
time_grain: "Q (ingresos/pobreza), A (geo)"
join_policy: "Temporal: (A↔Q) via adaptador; Llaves: HOGAR_REF_ID, ID (persona), claves geo"
---

## Principios de modelado

- **Claves estables**: `HOGAR_REF_ID` (hogar), `ID` (persona).  
- **Temporalidad**:  
  - **Trimestral (Q)** para ingresos/pobreza.  
  - **Anual (YEAR/ANO4)** para dimensión geográfica (hogares y personas).  
- **Uniones canónicas**:  
  - Personas (trimestral): `personas_ingresos_Q` ⨝ `pobreza_hogares` por (`HOGAR_REF_ID`, `Q`), y ⨝ geo por (`HOGAR_REF_ID`, `YEAR`).  
  - Hogares (trimestral): `pobreza_hogares` ⨝ `hogares_geo` por (`HOGAR_REF_ID`, `YEAR`). <!-- removed contentReference -->

> Nota de deduplicación: unificamos nombres y roles:  
> - **Mantener** `personas_ingresos_Q` (deprecar alias `personas_ingresos_Q_df`).  
> - **Mantener** `personas_geo` y `hogares_geo` como dimensiones **anuales**.  
> - **Mantener** `pobreza_hogares` como **hecho trimestral a nivel hogar**. <!-- removed contentReference -->

---

## Esquema DBML (consolidado)

~~~dbml
Project atlas {
  database_type: "postgres"
  Note: 'Q es ISO-8601 ancla trimestral (p.ej., 2022-05-15). YEAR/ANO4 anual.'
}

/* ===================== DIMENSIONES GEO (ANUAL) ===================== */

Table hogares_geo {
  HOGAR_REF_ID varchar [not null]                // clave hogar
  YEAR int [not null]                             // año de referencia (geo)
  RADIO_REF_ID varchar                           // radio censal
  AGLOMERADO int                                 // código aglomerado
  FRAC_REF_ID varchar                            // fracción
  DPTO int                                       // departamento
  NOMDPTO varchar                                // nombre dpto
  PROV int                                       // provincia
  NOMPROV varchar                                // nombre prov
  Region varchar                                 // región
  COD_2010 varchar                               // clave IGN/CONICET 2010
  distrito_id varchar
  seccion_id varchar
  seccion_nombre varchar
  circuito varchar
  IN1 varchar
  NAM varchar

  Note: 'Clave compuesta anual permite cambios de localización.'
  Indexes {
    (HOGAR_REF_ID, YEAR) [pk]
    PROV
    DPTO
    AGLOMERADO
  }
}

Table personas_geo {
  ID varchar [not null]                           // persona
  HOGAR_REF_ID varchar [not null]                 // hogar de la persona
  ANO4 int [not null]                             // año geo de persona
  RADIO_REF_ID varchar
  AGLOMERADO int
  DPTO int
  NOMDPTO varchar
  PROV int
  NOMPROV varchar
  Region varchar
  COD_2010 varchar
  IDFRAC varchar
  IN1 varchar
  circuito varchar

  Indexes {
    (ID, ANO4) [pk]
    (HOGAR_REF_ID, ANO4)
    PROV
    DPTO
    AGLOMERADO
  }

  Ref: personas_geo.HOGAR_REF_ID > hogares_geo.HOGAR_REF_ID
}

/* ===================== HECHOS (TRIMESTRAL) ===================== */

Table personas_ingresos_Q {
  ID varchar [not null]                           // persona
  HOGAR_REF_ID varchar [not null]                 // hogar
  Q date [not null]                               // ancla trimestral ISO
  P47T_persona numeric                            // ingreso persona
  P02 int
  P03 int
  P09 int
  P10 int
  P0910 int

  Indexes {
    (ID, Q) [pk]
    HOGAR_REF_ID
  }

  Ref: personas_ingresos_Q.HOGAR_REF_ID > hogares_geo.HOGAR_REF_ID
  Ref: personas_ingresos_Q.ID > personas_geo.ID
}

Table pobreza_hogares {
  HOGAR_REF_ID varchar [not null]
  Q date [not null]
  P47T_hogar numeric
  CBA numeric
  CBT numeric
  CB_EQUIV numeric
  Pobreza boolean
  Indigencia boolean
  gap_pobreza numeric
  gap_indigencia numeric

  Indexes {
    (HOGAR_REF_ID, Q) [pk]
  }

  Ref: pobreza_hogares.HOGAR_REF_ID > hogares_geo.HOGAR_REF_ID
}
~~~


* Las tres tablas `personas_ingresos_Q`, `pobreza_hogares`, `hogares_geo` y la dimensión `personas_geo` aparecen como el núcleo para combinar ingresos, pobreza y geo, con merges canónicos sobre `HOGAR_REF_ID` y `Q`.&#x20;
* La dimensión geo se documenta con provincias/departamentos/fracciones/códigos 2010 y se usa para generar GeoJSON y agregaciones espaciales.&#x20;

---

## Diccionario de claves y temporalidad (contrato)

* **Claves entidad**:

  * Hogar: `HOGAR_REF_ID` (string estable).
  * Persona: `ID` (string estable).
* **Claves geo** (anuales): `PROV`, `DPTO`, `AGLOMERADO`, `RADIO_REF_ID`, `FRAC_REF_ID`, `COD_2010`, `Region`.&#x20;
* **Tiempo**:

  * `Q`: fecha ISO ancla de trimestre, **DATE** (ej. `2022-05-15`).
  * `YEAR/ANO4`: entero anual.
* **Uniones**:

  * Trimestral: (`HOGAR_REF_ID`, `Q`)
  * Geo anual: (`HOGAR_REF_ID`, `YEAR`) / (`ID`, `ANO4`)
* **Anti-joins** obligatorios en build\*\*:\*\* detectar claves que no matchean entre hechos y dimensiones (reportar % faltantes).

---

## Contratos mínimos de datos

**Exportables canónicos (contrato de nombres):**

* `downloads/atlas.dbml` — esquema completo DBML (este documento).
* `exports/pobreza_hogares_Q.csv` — **hecho trimestral** (hogar).
* `exports/personas_ingresos_Q.csv` — **hecho trimestral** (persona).
* `exports/hogares_geo_YEAR.parquet` — **dimensión anual** (hogar).
* `exports/personas_geo_YEAR.parquet` — **dimensión anual** (persona).&#x20;

**Columnas mínimas por exportable:**

* `pobreza_hogares_Q.csv`: `HOGAR_REF_ID`, `Q`, `P47T_hogar`, `CBA`, `CBT`, `CB_EQUIV`, `Pobreza`, `Indigencia`, `gap_pobreza`, `gap_indigencia`.&#x20;
* `personas_ingresos_Q.csv`: `ID`, `HOGAR_REF_ID`, `Q`, `P47T_persona`, `P02`, `P03`, `P09`, `P10`, `P0910`.&#x20;
* `hogares_geo_YEAR.parquet`: `HOGAR_REF_ID`, `YEAR`, `RADIO_REF_ID`, `AGLOMERADO`, `FRAC_REF_ID`, `DPTO`, `NOMDPTO`, `PROV`, `NOMPROV`, `Region`, `COD_2010`, `distrito_id`, `seccion_id`, `seccion_nombre`, `circuito`, `IN1`, `NAM`.&#x20;
* `personas_geo_YEAR.parquet`: `ID`, `HOGAR_REF_ID`, `ANO4`, `RADIO_REF_ID`, `AGLOMERADO`, `DPTO`, `NOMDPTO`, `PROV`, `NOMPROV`, `Region`, `COD_2010`, `IDFRAC`, `IN1`, `circuito`.&#x20;


**Política temporal y citación:**

* **Política temporal**: hechos trimestrales (`Q`), dimensiones anuales (`YEAR/ANO4`); cuando se crucen, usar adaptador **A↔Q** definido en el módulo temporal (resample/forward-fill explícito).
* **Fuentes**: EPH (trimestral), Censo 2010. Documentadas en CoreRef y catálogos.&#x20;

---

## QA de integridad (fallar el build si no se cumple)

1. **Unicidad**:

   * `uniq(HOGAR_REF_ID, Q)` en `pobreza_hogares`.
   * `uniq(ID, Q)` en `personas_ingresos_Q`.
   * `uniq(HOGAR_REF_ID, YEAR)` en `hogares_geo`; `uniq(ID, ANO4)` en `personas_geo`.

2. **Cobertura de FKs**:

   * `% faltantes` de `HOGAR_REF_ID` entre hechos y dimensiones < 0.1%.
3. **Consistencia temporal**:

   * Prohibir joins A↔Q sin adaptador explícito.
4. **Dominios**:

   * `Pobreza/Indigencia` ∈ {true,false}; `AGLOMERADO`, `PROV`, `DPTO` numéricos; `Region` en catálogo.
5. **Drift geo**:

   * Chequear que cambios en geo (YEAR vs Q) queden documentados (diffs de clave).
6. **Reproducibilidad**:

   * `sha256` verificado para cada export y `schema_ref`.

---

## Snippets de uso (merges canónicos)

~~~sql
-- Hogar trimestral con geo anual (join con YEAR derivado de Q)
SELECT h.*, g.PROV, g.DPTO, g.Region
FROM pobreza_hogares h
JOIN hogares_geo g
  ON g.HOGAR_REF_ID = h.HOGAR_REF_ID
 AND g.YEAR = EXTRACT(YEAR FROM h.Q);

-- Personas trimestral + hogar trimestral + geo
SELECT p.ID, p.Q, p.P47T_persona, h.Pobreza, h.Indigencia, gg.AGLOMERADO
FROM personas_ingresos_Q p
JOIN pobreza_hogares h USING (HOGAR_REF_ID, Q)
JOIN hogares_geo gg
  ON gg.HOGAR_REF_ID = p.HOGAR_REF_ID
 AND gg.YEAR = EXTRACT(YEAR FROM p.Q);
~~~

---

* Los merges `info_personas` e `info_hogares` y el set de columnas clave provienen de tus fichas de “Métricas de pobreza por hogar” y “Estructura de datos”.&#x20;
* La presencia de **outputs JSON/CSV** para resultados y series (con `observable`, `sintetico`, `Q`, `frac`) justificó separar hechos de dimensiones y fijar los contratos de exportación.&#x20;

