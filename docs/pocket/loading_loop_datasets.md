---
id: pocket_loading_loop_datasets
title: "Loop para cargar datasets"
slug: /pocket/loading_loop_datasets
module: apendice-pocket
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [etl, pandas, io, loops]
source_repo: <repo-url>
source_path: docs/pocket/loading_loop_datasets.md
---

## Propósito
Construir rutas a partir de **combinaciones** (fuente/unidad/tiempo), validar existencia y cargar **muestras** (`nrows`) en un diccionario `{filename: DataFrame}` para inspección rápida. <!-- removed contentReference -->

## Variante 1 — tiempo fijo (ej. T=4)
~~~python
import pandas as pd, os

base_dir = "/path/reg_data"
sources = ["acled", "afb_places", "CN", "WBad", "WBkg"]
units = ["a1", "a2", "a3", "l1", "l2", "l3"]
years = [2000, 2001]

datasets = {}
for src in sources:
    for unit in units:
        for yr in years:
            fn = f"agg_{src}_africa_{unit}_4y{yr}_year.csv"
            fp = os.path.join(base_dir, fn)
            if os.path.exists(fp):
                datasets[fn] = pd.read_csv(fp, nrows=5)
~~~



## Variante 2 — tiempos combinados (2–4 años)

~~~python
import pandas as pd, os

base_dir = "/path/reg_data"
sources = ["acled", "afb_places", "CN", "WBad", "WBkg"]
units = ["a1", "a2", "a3", "l1", "l2", "l3"]
times = [f"{i}y{yr}" for i in range(2, 5) for yr in [2000, 2001]]

datasets = {}
for src in sources:
    for unit in units:
        for t in times:
            fn = f"agg_{src}_africa_{unit}_{t}_year.csv"
            fp = os.path.join(base_dir, fn)
            if os.path.exists(fp):
                datasets[fn] = pd.read_csv(fp, nrows=5)
~~~



## Consejos

* `nrows` acelera iteración inicial; al escalar, considerar **dtypes** y **usecols**.
* Si la carpeta es grande, preferí `glob` para listar y filtrar por patrón.
* Para testear pipelines, exportá *samples* representativos y usá estos loops como **smoke tests**.

## QA mínimo

* `len(datasets)` coincide con cantidad de archivos **existentes** que matchean patrón.
* Validar columnas mínimas esperadas por fuente/unidad (asserts con `set.issubset`).
