---
id: pocket_log_message_merge_qc
title: "log_message + QA de merges"
slug: /pocket/log_message_merge_qc
module: apendice-pocket
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [etl, logging, merges, qa]
source_repo: <repo-url>
source_path: docs/pocket/log_message_merge_qc.md
---

## Propósito
Patrón de **logging** con timestamp + elapsed y **verificaciones mínimas** para merges (filas pre/post, columnas en común, cobertura). Este esquema aparece en tus scripts de producción y acelera diagnóstico de cuellos de botella y uniones problemáticas. <!-- removed contentReference -->

## `log_message` (timestamp + elapsed)
~~~python
import time

def log_message(msg, t0=None):
    now = time.time()
    stamp = time.strftime('%Y-%m-%d %H:%M:%S', time.localtime(now))
    dt = f' | elapsed: {now - t0:.2f}s' if t0 else ''
    print(f'[{stamp}] {msg}{dt}')
~~~

Usado para marcar etapas (extracción, transformaciones, sampleo, merge, compute, guardado).&#x20;

## QA de merges (pre/post filas + columnas en común)

~~~python
t_merge = time.time()

common_cols = left.columns.intersection(right.columns).tolist()
log_message(f"Merging on columns: {common_cols}")  # guarda evidencia de claves reales
rows_before = len(left)

out = left.merge(
    right[common_cols + ['ALGUNA_COL_EXTRA']],  # evita arrastrar columnas inútiles
    on=common_cols, how='left', validate='m:1'  # elige validate según cardinalidad esperada
)

rows_after = len(out)
if rows_after != rows_before:
    log_message("WARNING: row count changed after merge", t_merge)
else:
    log_message("Merge completed", t_merge)
~~~

Este patrón ya figura en tus notas (comparando filas y logueando columnas de unión). &#x20;

### Cuando *no* alcanza contar filas

* `indicator=True` para medir **anti-joins** (`_merge=='left_only'` o `'right_only'`).
* `validate` (p.ej., `m:1`, `1:1`) obliga la cardinalidad esperada (falla temprano).
* Reportar **tasa de cobertura**: `out['_merge'].value_counts(normalize=True)`.

## Checklist operativo

* Claves limpias (tipos y padding), sin NaNs.
* Subset de columnas de `right` (reduce memoria y colisiones de nombres).
* Post-merge: asserts de **unicidad** si se esperaba `1:1`.

> Complementa `operacion/logging_etl.md` y los contratos de claves en **/referencia/nucleo/dbml\_fuente\_de\_verdad**.
