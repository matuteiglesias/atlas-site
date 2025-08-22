---
id: pocket_concat_csv_patterns
title: "Concat CSVs — 3 patrones"
slug: /pocket/concat_csv_patterns
module: apendice-pocket
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [etl, pandas, csv, concat]
source_repo: <repo-url>
source_path: docs/pocket/concat_csv_patterns.md
---

## Propósito
Tres recetas recurrentes para concatenar CSVs con **pandas**: (A) vertical, (B) con **nombre de archivo** como columna y (C) **horizontal** por unidad. <!-- removed contentReference -->

## A) Vertical (stack)
~~~python
import pandas as pd, glob

files = glob.glob("data/*.csv")
dfs = (pd.read_csv(f) for f in files)
df = pd.concat(dfs, ignore_index=True)
~~~

**Útil** cuando el esquema es homogéneo; si hay columnas desalineadas, revisá `join='outer'`/`'inner'`.

## B) Vertical + “filename-as-col”

~~~python
import os, pandas as pd, glob

rows = []
for f in glob.glob("data/*.csv"):
    tmp = pd.read_csv(f)
    tmp["source_file"] = os.path.basename(f)
    rows.append(tmp)

df = pd.concat(rows, ignore_index=True)
~~~

**Ventaja**: trazabilidad y auditoría simple (origen por registro).&#x20;

## C) Horizontal por unidad (join lado a lado)

~~~python
import pandas as pd

# Cada CSV tiene una clave 'unit' y columnas distintas a unir
df_a = pd.read_csv("a.csv")  # unit, var_a1, var_a2
df_b = pd.read_csv("b.csv")  # unit, var_b1
df_c = pd.read_csv("c.csv")  # unit, var_c1, var_c2

df = df_a.merge(df_b, on="unit", how="outer").merge(df_c, on="unit", how="outer")
~~~

**Atención**: documentar cardinalidades esperadas y usar `validate`.&#x20;

## Notas de performance

* **Lectura en chunks** si cada CSV es grande (`pd.read_csv(..., chunksize=...)` + `pd.concat`).
* Forzar `dtype` para evitar “object” inflado.
* Evitar concatenar dentro de loops crecientes (O(n²)): acumular en lista y **un solo** `concat`.

## QA mínimo

* Chequear `df.shape` vs suma de fuentes (vertical) o universo esperado (horizontal).
* Verificar duplicados en clave (`df.duplicated(['unit']).sum()` en joins 1:1).
* Si hay columnas homónimas, renombrar antes de concatenar/mergear.

