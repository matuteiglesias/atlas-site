---
id: pocket_json_deep_merge_qseries
title: "merge_jsons (series Q)"
slug: /pocket/json_deep_merge_qseries
module: apendice-pocket
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [json, merge, timeseries, quarterly, metadata]
source_repo: <repo-url>
source_path: docs/pocket/json_deep_merge_qseries.md
---

## Propósito
Fusión **jerárquica** de estructuras JSON (observable → sintético → `data` por agrupador → **Q**), **sin perder históricos** y actualizando solo lo nuevo. Evita el overwrite por `.update()` plano. <!-- removed contentReference -->

## Estructura objetivo (esquema mental)
~~~text
{
  <observable>: {
    <sintetico>: {
      "metadata": {"last_updated": "...", ...},
      "data": {
        <grouper_value>: {
          "2019Q4": <valor>,
          "2020Q1": <valor>,
          ...
        },
        ...
      }
    },
    ...
  },
  ...
}
~~~

La fusión correcta **agrega/actualiza** por clave `Q` **sin borrar** trimestres previos y refresca `metadata.last_updated`.&#x20;

## Implementación recomendada

~~~python
def merge_jsons(main_data: dict, new_data: dict) -> dict:
    """
    Funde estructuras jerárquicas sin perder históricos trimestrales.
    Niveles: observable -> sintetico -> data[grouper][Q] = valor
    - Actualiza 'metadata.last_updated' con el del bloque nuevo.
    - No hace overwrite masivo: actualiza Q puntuales.
    """
    for observable, metrics in new_data.items():
        if observable not in main_data:
            main_data[observable] = {}

        for sintetico, details in metrics.items():
            # Inicializar nodo si no existe
            if sintetico not in main_data[observable]:
                main_data[observable][sintetico] = {
                    "metadata": details.get("metadata", {}),
                    "data": {}
                }
            else:
                # Refrescar solo el last_updated (o mergear metadata si aplica)
                if "metadata" in details and "last_updated" in details["metadata"]:
                    main_data[observable][sintetico]["metadata"]["last_updated"] = \
                        details["metadata"]["last_updated"]

            # Merge fino por agrupador y Q
            for grp_val, ts in details.get("data", {}).items():
                main_node = main_data[observable][sintetico]["data"].setdefault(grp_val, {})
                # ts es dict de Q->valor: actualizar uno a uno
                for q, v in ts.items():
                    main_node[q] = v
    return main_data
~~~

Este patrón condensa tus iteraciones jerárquicas previas y corrige el **overwrite de series** que generaba pérdida de Qs.&#x20;

## Política de no-overwrite

* **Regla**: escribir por **Q** concreta; nunca reemplazar `data[grp_val]` entero.
* **Metadata**: refrescar **solo** `last_updated` (o merge selectivo).&#x20;

## QA mínimo

* Antes/después: contar Qs únicos por `grp_val`.
* Idempotencia: aplicar dos veces `merge_jsons(main, new)` no cambia el resultado.
* Verboz: opcionalmente loguear altas/updates por (`observable`, `sintetico`, `grp_val`, `Q`).&#x20;

## Errores comunes y cómo evitarlos

* **`.update()` al nivel equivocado** → pisa el dict completo de Qs. Solución: loop granular por Q.&#x20;
* **Introducir listas de dicts para series** → complica dedupe. Preferí `dict` `{Q: valor}`.&#x20;
* **No inicializar nodos intermedios** → `KeyError`. Usar `setdefault`/`get`.

## Relacionados

* **/metodos/etl\_json\_policies** (serialización, compresión y contratos).
* **Pocket → logging & QA de merges** para checks de filas y diffs.

