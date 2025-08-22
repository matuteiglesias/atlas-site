---
id: pocket_json_timestamps_encoder
title: "Timestamps → JSON (encoder + checklist)"
slug: /pocket/json_timestamps_encoder
module: apendice-pocket
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [json, timestamp, pandas, encoder, serialization]
source_repo: <repo-url>
source_path: docs/pocket/json_timestamps_encoder.md
---

## Propósito
Evitar `TypeError: Object of type Timestamp is not JSON serializable` en exportes jerárquicos: **encoder** custom para `pandas.Timestamp`, conversión defensiva en `DataFrame` y *troubleshooting* profundo. <!-- removed contentReference -->

> Para políticas de formato/base temporal y compresión, ver **/metodos/etl_json_policies**.

## Encoder recomendado (ISO-8601)
~~~python
import json
import pandas as pd

class AtlasJSONEncoder(json.JSONEncoder):
    def default(self, obj):
        if isinstance(obj, pd.Timestamp):
            # fecha-only; si necesitás datetime, usa .isoformat()
            return obj.strftime("%Y-%m-%d")
        return super().default(obj)

# uso:
# json.dump(payload, fp, cls=AtlasJSONEncoder)
~~~

Funciona aunque aparezcan `Timestamp` en niveles profundos del objeto.&#x20;

## Checklist antes de exportar

### 1) Limpiar columnas datetime en DataFrames

~~~python
for col in df.columns:
    if str(df[col].dtype).startswith("datetime64"):
        df[col] = df[col].astype(str)  # o dt.strftime(...) según contrato
~~~

Evita que `to_dict()` inyecte `Timestamp` en `data_hierarchy`.&#x20;

### 2) Timestamps generados en código → string

~~~python
import datetime as dt
timestamp = dt.datetime.now().isoformat()  # ya es str
~~~

Asegura el tipo correcto desde el origen.&#x20;

### 3) Conversión profunda (si hay anidamientos mixtos)

Si el payload mezcla listas/dicts:

* Recorre recursivo y castea `Timestamp` → string (o deja que el **encoder** lo capture).
* Añadí **logging** para imprimir claves problemáticas cuando aparezca el `TypeError`.&#x20;

## Anti-patrones comunes

* Confiar **solo** en `astype(str)` si hay estructuras **fuera** de `DataFrame`.
* Mutar el payload a mitad de pipeline sin tests de serialización.
* Exportar sin fijar un formato **único** (fecha vs datetime) → inconsistencia aguas abajo.

## QA mínimo

* `json.dumps(payload, cls=AtlasJSONEncoder)` corre sin excepciones.
* Búsqueda negativa: no debe aparecer `"Timestamp("` en el JSON serializado.
* Si usás fecha-only, testea que todas las claves relevantes cumplan `YYYY-MM-DD`.

## Troubleshooting rápido

* **Sigue fallando**: hay un `Timestamp` en otra rama del objeto → agrega logs o castea esa rama explícitamente.
* **Valores string “raros”**: valida *timezone awareness* y el **estándar** (fecha-only vs datetime).&#x20;

