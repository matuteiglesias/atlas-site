---
id: metodos_etl_json_policies
title: "Políticas de JSON y export (estructura, compresión, timestamps, merges)"
slug: /metodos/etl_json_policies
module: metodos
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [json, export, jerarquico, compresion, timestamps]
source_repo: <repo-url>
source_path: docs/metodos/etl_json_policies.md
---


Guía práctica para publicar/consumir JSON en el Atlas: **cómo estructurarlo**, **cómo serializar timestamps**, **cómo fusionar series jerárquicas por Q** sin perder históricos, y **cuál es el contrato mínimo** de salida para resúmenes (agregados). El objetivo es minimizar tamaño y fallos de E/S sin romper compatibilidad con front-ends. <!-- removed contentReference -->

---

## Principios de diseño (resumen operativo)
1) **Normalizá lo repetitivo**: mover constantes (`base`, `frac`, etc.) a metadatos y referenciar por ID (evita redundancia). <!-- removed contentReference -->
2) **Comprimí al publicar**: JSON + `gzip` (o `brotli`) en disco/transporte; coste de CPU marginal vs. ahorro grande. <!-- removed contentReference -->
3) **Evaluá binarios para volumen**: cuando el consumidor es batch, preferí Parquet/Feather/MessagePack (y dejá JSON solo para web/SDK). <!-- removed contentReference -->
4) **Indexá si consultás mucho**: para consultas repetidas, un `.sqlite` con índices puede ganar en latencia y tamaño. <!-- removed contentReference -->
5) **Estructura jerárquica**: empaquetá series por `observable → sintetico → data[grouper] → {Q: valor}`, con `metadata.last_updated` separado. <!-- removed contentReference -->

> Nota: si una política entra en conflicto con front-ends existentes, priorizar compatibilidad y documentar la excepción.

---

## Contratos de exportación (cierra *MISSING_CONTRACTS* de 0125)

### A) Series jerárquicas (resultados por Q)
- **Esquema**:
  ~~~json
  {
    "observable": {
      "sintetico": {
        "metadata": { "last_updated": "YYYY-MM-DDTHH:MM:SSZ", "schema_version": "1.0.0", "frac": 0.005, "base": "H" },
        "data": {
          "Total_pais": { "2019Q1": 0.23, "2019Q2": 0.24 },
          "AGLO_si=True": { "2019Q1": 0.31 }
        }
      }
    }
  }
~~~

* **Claves**: `observable` (métrica), `sintetico` (serie), `data[grouper][Q]`.

* **Regla**: agregar Q **sin** sobrescribir trimestres previos.&#x20;

* **Export**:

~~~yaml
exports:
  - path: /results/result_H_Q-Total_pais_0.005.json
    sha256: "<sha256>"
    compression: "gzip"
    schema_version: "1.0.0"
    last_updated: "YYYY-MM-DDTHH:MM:SSZ"
~~~

### B) Resúmenes/estadísticos (pipeline de 0125)

* **Entrada**: registros a nivel persona/hogar; `grouper[]`, funciones de agregación (incl. percentiles).
* **Salida JSON** (contrato mínimo):

~~~json
{
  "metadata": { "grouper": ["PROV","DPTO"], "period": "2016Q1..2025Q2", "schema_version":"1.0.0" },
  "stats": [
    { "PROV": 2, "DPTO": 13, "n": 12034, "mean_ingreso": 70800, "p10": 22000, "p50": 65000, "p90": 145000 }
  ]
}
~~~

* **Nombres**: `summary_[base]_[grouper-join('_')]_[period].json`
* **Garantías**: tipos consistentes, sin NaNs en claves, y `stats` como array de objetos.
* **Racional**: el script actual transforma individuos→agregados y guarda JSON; aquí fijamos estructura y metadatos.&#x20;

---

## Serialización de timestamps (sin TypeError)

**Problema típico**: `TypeError: Object of type Timestamp is not JSON serializable`.
**Soluciones recomendadas**:

1. **Encoder custom** (cubre `pd.Timestamp`):

~~~python
import json, pandas as pd

class CustomEncoder(json.JSONEncoder):
    def default(self, obj):
        if isinstance(obj, pd.Timestamp):
            return obj.strftime('%Y-%m-%d')
        return super().default(obj)

# uso
json.dump(data_hierarchy, open("out.json","w"), cls=CustomEncoder)
~~~

Esto intercepta objetos `Timestamp` en cualquier nivel de la jerarquía.&#x20;

2. **Conversión profunda** (antes de exportar):

~~~python
def deep_convert_ts(x):
    import pandas as pd
    if isinstance(x, dict):
        return {k: deep_convert_ts(v) for k, v in x.items()}
    if isinstance(x, list):
        return [deep_convert_ts(v) for v in x]
    if isinstance(x, pd.Timestamp):
        return x.isoformat()
    return x
~~~

Aplícalo sobre la estructura si el encoder no está disponible en el consumidor.&#x20;

3. **DataFrames a string** (si hay columnas datetime en `df`):

~~~python
for col in df.columns:
    if df[col].dtype == 'datetime64[ns]':
        df[col] = df[col].astype(str)
~~~

Conviene hacerlo **antes** de `to_dict(orient="records")`.&#x20;

---

## Merge de JSON jerárquico (dos etapas, sin perder históricos)

**Decisión**: construir datos del **Q actual** (`current_data`) y **fusionarlos** en la estructura acumulada (`all_data`) **sin sobrescribir** trimestres previos; actualizar solo el `last_updated`.&#x20;

~~~python
def merge_jsons(main_data, new_data):
    # Estructura: observable -> sintetico -> data[grouper][Q] = valor
    for observable, metrics in new_data.items():
        if observable not in main_data:
            main_data[observable] = {}
        for sintetico, details in metrics.items():
            if sintetico not in main_data[observable]:
                main_data[observable][sintetico] = { 'metadata': details['metadata'], 'data': {} }
            else:
                main_data[observable][sintetico]['metadata']['last_updated'] = details['metadata']['last_updated']
            for grp_val, timeseries in details['data'].items():
                if grp_val not in main_data[observable][sintetico]['data']:
                    main_data[observable][sintetico]['data'][grp_val] = {}
                for q, value in timeseries.items():
                    main_data[observable][sintetico]['data'][grp_val][q] = value
    return main_data
~~~

* Evitá `.update()` a nivel superior: puede **pisar** series completas; hacé **merge profundo** por `q`.&#x20;
* Si preferís listas de entradas `{q: valor}`, inspeccioná duplicados antes de `append`.&#x20;
* Agregá *logging* verboso en altas/updates de `q` para trazabilidad.&#x20;

---

## Eficiencia y publicación

* **Compresión**: almacenar `.json.gz` y servir con `Content-Encoding: gzip` cuando sea posible.&#x20;
* **Minimización**: evitar claves/valores redundantes; constantes a `metadata`.&#x20;
* **Formatos alternativos**: para pipelines analíticos grandes, publicar en Parquet/MessagePack además de JSON.&#x20;
* **Catálogo**: para `result_*` y nombres de `results_path`, ver la página de naming y no duplicar contratos aquí.

---

## Manejo de errores y recuperación

* **`JSONDecodeError`**: usualmente archivo mal formado/truncado; validar apertura y formato antes de merge.&#x20;
* **Truncado en escritura**: usá escritura atómica: volcar a `tmp` y `os.replace(tmp, final)`. Confirmar cierre/flush.&#x20;
* **Diagnóstico**: imprimir columna/posición del error y revisar final del archivo; si no hay backup, **regenerar** desde fuente.&#x20;

---

## QA mínimo

1. **Esquema**: validar presencia de `metadata` y `data`, y que `data[grouper]` sea dict de `{Q: valor}`.&#x20;
2. **Timestamps**: ni un `Timestamp` crudo en la estructura (encoder o conversión previa).&#x20;
3. **Históricos**: al insertar un Q nuevo, los Q previos permanecen; muestrear claves antes/después.&#x20;
4. **Tamaño**: ratio de compresión objetivo (`.json.gz`/`.json`) y conteo de filas/entradas esperado.&#x20;
5. **Determinismo**: mismos insumos ⇒ mismo hash `sha256` del export (salvo cambios de metadatos).

---

## Referencias cruzadas

* **Operación → results\_path\_naming** (naming `result_*` y reglas de no colisión).
* **Métodos → temporal\_toolkit** (estructura Q/M/A y adaptadores).

~~~
