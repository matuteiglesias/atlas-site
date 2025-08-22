---
id: operacion_results_path_naming
title: "Procesados en results_path y contrato de nombres"
slug: /operacion/results_path_naming
module: operacion
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [results_path, contrato, nombres, derivados, frac, grouper]
source_repo: <repo-url>
source_path: docs/operacion/results_path_naming.mdx
---


Estandar para archivos derivados de analisis rapido ubicados en `results_path`. No reemplazan a los exports canonicos de catalogo.

## Patron de nombres
- Formato: `result_[base_str]_Q-[grouper]_[FRAC].csv`
  - `[base_str]`: subpoblacion o base, ejemplo `P`, `H`, `M24`
  - `Q`: ancla trimestral (se reporta en columna o metadata)
  - `[grouper]`: columnas de agrupacion. Si son multiples usar `+` como separador y un orden determinista, ejemplo `PROV+DPTO`
  - `[FRAC]`: fraccionamiento, ejemplo `0.005`
- Ejemplo: `result_H_Q-Total_pais_0.005.csv`

~~~yaml
exports:
  - path: /results/result_H_Q-Total_pais_0.005.csv
    sha256: "<sha256>"
    sample_period: "2016Q1..2025Q2"
    grouper: ["Total_pais"]
    frac: 0.005
~~~

## Reglas

* Los `result_*` son artefactos intermedios. Los exports canonicos viven en fichas de catalogo
* Prohibido sobrescribir nombres usados por canonicos
* `[grouper]` debe documentar orden y separador `+`
* `[FRAC]` usa punto decimal en el nombre de archivo

## QA minimo

1. Columnas clave presentes segun definicion de cada receta
2. Tipos consistentes con los datasets de origen
3. Ausencia de duplicados inesperados en la clave de agregacion
4. Trazabilidad: agregar metadata de origen `source_repo@commit` y parametros de ejecucion

## Retencion y limpieza

* Retencion por periodo rodante definido por entorno
* Limpieza controlada conforme a naming para evitar borrar exports canonicos

## Referencias cruzadas

* DBML fuente de verdad
* Fichas de catalogo de los exports canonicos
* Playbooks que generan estos resultados

~~~
