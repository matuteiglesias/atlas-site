---
id: pobreza_hogares_metricas
title: "Métricas de pobreza por hogar (trimestre, año)"
slug: /catalogo/pobreza_hogares_metricas
module: catalogo
version: 0.2.0
status: draft
owners: [atlas-core]
tags: [pobreza, hogares, metricas, Q]
source_repo: <repo-url>
source_path: docs/catalogo/pobreza_hogares_metricas.mdx
schema_ref: /downloads/atlas.dbml#pobreza_hogares
time_grain: Q
join_policy: "HOGAR_REF_ID+Q para hechos; HOGAR_REF_ID+YEAR para geo via adaptador temporal"
---

# Descripción
Conjunto de métricas que caracterizan la situación de pobreza a nivel de hogar por trimestre y año. Origen de cálculo: notebook “3. Cálculo de Pobreza.ipynb”. <!-- removed contentReference -->

## Columnas mínimas y tipos
| Columna          | Tipo     | Descripción breve                                                                                                  |
|------------------|----------|---------------------------------------------------------------------------------------------------------------------|
| HOGAR_REF_ID     | string   | Identificador estable de hogar                                                                                      |
| Q                | date     | Ancla trimestral ISO (por ejemplo 2024-02-15)                                                                       |
| P47T_hogar       | numeric  | Ingreso monetario total del hogar en términos reales                                                                |
| CBA              | numeric  | Valor de Canasta Básica Alimentaria por adulto equivalente en moneda base                                           |
| CBT              | numeric  | Valor de Canasta Básica Total por adulto equivalente en moneda base                                                 |
| CB_EQUIV         | numeric  | Escala de equivalencia del hogar (adulto equivalente del hogar)                                                     |
| Pobreza          | boolean  | Indicador de pobreza del hogar                                                                                       |
| Indigencia       | boolean  | Indicador de indigencia del hogar                                                                                    |
| gap_pobreza      | numeric  | Brecha relativa respecto de la CBT equivalente                                                                      |
| gap_indigencia   | numeric  | Brecha relativa respecto de la CBA equivalente                                                                      |

> Las columnas y su foco aparecen en tus resúmenes de `pobreza_hogares`. <!-- removed contentReference -->

## Definiciones operativas
Para evitar ambigüedades, fijamos reglas explícitas de decisión y brecha. Estas reglas se vuelven parte del contrato.

- Cestas equivalentes del hogar  
  `CBT_hogar = CBT * CB_EQUIV`  
  `CBA_hogar = CBA * CB_EQUIV`

- Indicadores booleanos  
  `Pobreza = (P47T_hogar < CBT_hogar)`  
  `Indigencia = (P47T_hogar < CBA_hogar)`

- Brechas (censuradas a [0,1] si corresponde al uso)  
  `gap_pobreza   = max(0, 1 - P47T_hogar / CBT_hogar)`  
  `gap_indigencia= max(0, 1 - P47T_hogar / CBA_hogar)`

Notas técnicas:
- Si trabajás con canastas mensuales y `Q` es trimestral, consolidar canastas del trimestre antes del cómputo (media geométrica o aritmética, elegir y documentar en la ficha de CBA/CBT).  
- Si `CB_EQUIV` cambia por estructura etaria, la serie de canastas equivalentes es específica del hogar y del período.

## Dependencias de upstream
- `ingresos_ajustados` por hogar o agregación desde persona con deflactores IPC base 2016=100  
- `hogares_geo` para agregaciones territoriales por año  
- `CBA/CBT` mensuales con escala de equivalencia  
La presencia de estas tablas y merges está en tus notas de estructura y merges canónicos. <!-- removed contentReference -->

## Contrato de exportación
- Archivo canónico: `exports/pobreza_hogares_Q.csv`  
- Codificación: UTF-8  
- Delimitador: coma  
- Orden de columnas recomendado:  
  `HOGAR_REF_ID,Q,P47T_hogar,CBA,CBT,CB_EQUIV,Pobreza,Indigencia,gap_pobreza,gap_indigencia`

~~~yaml
exports:
  - path: /exports/pobreza_hogares_Q.csv
    sha256: "<sha256-csv-pobreza>"
    rows: "<n-registros>"
    sample_period: "2016Q1..2025Q2"
~~~

## Reglas de QA

1. Unicidad de clave: `uniq(HOGAR_REF_ID, Q)`
2. Dominio booleano: `Pobreza, Indigencia ∈ {true,false}`
3. Coherencia de brechas:

   * si `Pobreza = false` entonces `gap_pobreza = 0`
   * si `Indigencia = false` entonces `gap_indigencia = 0`
   * brechas no negativas y acotadas
4. Coherencia de canastas: `CBT >= CBA`, `CB_EQUIV >= 0`
5. Ingresos: `P47T_hogar >= 0` y no nulos. Si hay ceros estructurales, documentar tratamiento.
6. Integridad de joins: porcentaje de `HOGAR_REF_ID` ausentes en `hogares_geo` por año menor a umbral.
   Las claves y merges fueron descritos en tus apuntes de datasets y relaciones.&#x20;

## Consideraciones de temporalidad

* `Q` es la unidad de decisión de pobreza.
* Las canastas son mensuales. Se debe declarar la política de agregación a `Q` y evitar fugas de NaN al resampleo.
* Cuando se publiquen agregados anuales, explicitar si se trata de promedio simple de trimestres, promedio ponderado o último trimestre disponible.

## Ejemplos de uso

SQL para enriquecer con geo anual:



~~~sql

SELECT h.*, g.PROV, g.DPTO, g.Region
FROM pobreza_hogares h
JOIN hogares_geo g
  ON g.HOGAR_REF_ID = h.HOGAR_REF_ID
 AND g.YEAR = EXTRACT(YEAR FROM h.Q);

~~~



Pseudocódigo de cálculo:

~~~python
CBT_hogar = CBT * CB_EQUIV
CBA_hogar = CBA * CB_EQUIV
Pobreza = P47T_hogar < CBT_hogar
Indigencia = P47T_hogar < CBA_hogar
gap_pobreza = max(0, 1 - P47T_hogar / CBT_hogar)
gap_indigencia = max(0, 1 - P47T_hogar / CBA_hogar)
~~~

## Errores comunes y cómo evitarlos

* Mezclar A con Q sin adaptador temporal. Mantener la regla A↔Q del modelo.
* Usar canastas nominales con ingresos deflactados o viceversa. Alinear siempre ambas magnitudes.
* No censurar brechas negativas cuando ingresos superan la canasta.
* Duplicación por hogares que cambian de localización anual. El join debe fijar `YEAR = year(Q)` al cruzar con geo.
  Estos puntos están implícitos en tu documentación de estructuras y merges.&#x20;

## Procedencia y contexto

* Dataset definido en tu índice de métricas de pobreza por hogar y descrito como producto del notebook “3. Cálculo de Pobreza.ipynb”.&#x20;
* Estructura de tablas y relaciones con `personas_ingresos_Q` y `hogares_geo` documentada en tus notas de estructura de datos.&#x20;

~~~