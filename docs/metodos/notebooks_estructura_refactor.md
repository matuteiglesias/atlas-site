---
id: metodos_notebooks_estructura_refactor
title: "Notebooks & refactor — estructura, modularización y contratos"
slug: /metodos/notebooks_estructura_refactor
module: metodos
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [notebooks, refactor, modularizacion, etl, catalogo]
source_repo: <repo-url>
source_path: docs/metodos/notebooks_estructura_refactor.md
---


Guía para estandarizar **cómo escribimos notebooks** (secciones y orden), **cómo extraemos funciones a módulos** sin globales y **cómo documentamos entradas/salidas** alineadas al Catálogo y al DBML. El objetivo: reproducibilidad, menor deuda técnica y onboarding rápido.

---

## 1) Estructura mínima de un notebook (plantilla)
Usá siempre las mismas secciones, en este orden:

1. **Imports & entorno**: librerías, seed, paths base.  
2. **Parámetros & config**: año/trimestre (`Q`), `frac`, rutas.  
3. **Carga de datos**: fuentes externas/internas.  
4. **Preprocesamiento**: limpieza, tipado, normalizaciones.  
5. **Transformaciones**: pasos nucleares (ETL/modelado).  
6. **Síntesis y QA**: checks de cardinalidades, NaN, rangos.  
7. **Exportes**: escritura de artefactos (con *naming* y checksum).  
8. **Registro**: tiempo de corrida, `source_repo@commit`.

Esta organización aparece en tus notas como “estructura lógica de celdas” y “framework por cuadernos” (preprocesamiento → principal → polis geo → guardar), útil para seguir un **pipeline** lineal y legible. <!-- removed contentReference -->

**Snippets de cabecera (recomendado):**
~~~python
# 1) Imports & entorno
import os, sys, json, datetime as dt
import numpy as np, pandas as pd
np.random.seed(123)

# 2) Parámetros & config
YEAR = 2019
Q = "2019Q1"
FRAC = 0.005
RESULTS_PATH = "../results"

# 3) Carga
# df = pd.read_csv(...)

# 4) Preprocesamiento
# ...

# 5) Transformaciones (llamadas a funciones del módulo /packages)
# ...

# 6) QA
# assert df.notna().all().all()

# 7) Exportes
# df.to_csv(...)

# 8) Registro
print("done", dt.datetime.utcnow().isoformat(), "Z")
~~~

---

## 2) Contratos mínimos por tipo de notebook

> **Regla**: todo notebook debe declarar su **Contrato de Entradas/Salidas** (IES).

### A. Preprocesamiento

* **Entradas**: rutas a fuentes crudas (EPH/Censo/Geo), *schemas* esperados, filtros.
* **Salidas**: tablas normalizadas (tipos, claves), *path* temporal.
* **QA**: columnas obligatorias presentes, dominios válidos, tamaño > 0.
  **Motivo**: separar IO/limpieza del resto del pipeline (reduce ruido y repetición).&#x20;

### B. Transformación (Ingresos ↔ Pobreza)

* **Entradas**: datasets preprocesados + parámetros (p. ej., escalas, deflactores).
* **Salidas**: tablas conformes a DBML (p. ej., `personas_ingresos_Q`, `pobreza_hogares`).
* **QA**: cardinalidades de joins, ausencia de globales, funciones puras.
  **Motivo**: extraer funciones recurrentes a módulos y pasar dependencias por argumentos. &#x20;

### C. Síntesis & Export

* **Entradas**: data consolidada; `grouper[]` y specs de agregación.
* **Salidas**: JSON/CSV **con contrato publicado** (ver páginas de *results\_path* y JSON).
* **QA**: `sha256`, tamaños esperados, ausencia de `Timestamp` crudo en JSON.
  **Motivo**: uniformidad de exports para front-ends y reproducibilidad.&#x20;

---

## 3) Modularización real (sin *globals*)

**Problema**: funciones que dependen de variables globales (p. ej., `ad_eq`, `DPTO_Region`, `CB_ipc`) dañan la reutilización y el testeo.
**Política**: **todas** las dependencias entran como **argumentos** de función; nada lee del *scope* global del notebook. Ejemplos en tus apuntes (refactor de `canasta`, `geo_hogares`, etc.).&#x20;

**Antes (antipatrón)**

~~~python
# usa ad_eq, DPTO_Region globales
def canasta(df):
    return df.merge(ad_eq).merge(DPTO_Region)
~~~

**Después (correcto)**

~~~python
def canasta(df, ad_eq, dpto_region, cb_ipc):
    out = df.merge(ad_eq).merge(dpto_region).merge(cb_ipc)
    out["CBA"] *= out["CB_EQUIV"]
    out["CBT"] *= out["CB_EQUIV"]
    return out
~~~

* Beneficio: funciones **puras**, fáciles de mover a `funciones.py` / paquete, con tests directos.&#x20;

---

## 4) Refactor de funciones de transformación (criterios)

Guía para desarmar “super-funciones” en piezas testeables (tu doc 0222):

* **Dividir por responsabilidad**: educación/codificación, cálculo de pobreza, merges, guardado.&#x20;
* **Evitar *hard-coding***: mappings y reglas a **YAML/JSON** o parámetros.&#x20;
* **Legibilidad**: docstrings, nombres claros, comentarios en pasos no obvios.&#x20;
* **Errores controlados**: `try/except` con mensajes y contexto (columna/fase).&#x20;
* **Vectorizar cuando se pueda** y revisar índices/joins para performance.&#x20;

**Esqueleto sugerido**

~~~python
def transform_ingresos(df, cfg):
    df1 = recod_educacion(df, cfg["map_educ"])          # sin hard-coding
    df2 = merge_geo(df1, cfg["geo_paths"])
    df3 = compute_poverty(df2, cfg["poverty_params"])
    return df3
~~~

---

## 5) Organización del proyecto: notebooks ↔ módulos

Tus notas proponen dos variantes; ambas válidas, con trade-offs:

* **Monolítico `analysis_functions.py`**: rápido de armar, escala limitado.&#x20;
* **Modular (`modules/preprocessing.py`, `income_transformation.py`, …)**: mejor separación y testeo; preferido si crece.&#x20;

**Reglas comunes**

* Importar **siempre** desde módulos; el notebook solo **orquesta**.
* Rutas y nombres de archivo como **parámetros** (no literales en funciones).
* Tests rápidos para cada módulo crítico (ver §7).

---

## 6) Documentación de datasets (vincular a Catálogo/DBML)

No dupliques definiciones en notebooks. Para cada output:

* **Apuntar a la ficha** del Catálogo (nombres, columnas mínimas, `sha256`).
* **Respetar DBML** para claves/relaciones (hogar/persona/geo).
* **Mantener un índice** de “inputs/outputs” por notebook que referencie fichas relevantes.
  Esto ya está sugerido en tu material de **estructura de conjuntos de datos**: separar “Fuentes de datos” vs “Bases derivadas”, y normalizar encabezados.&#x20;

**Mini-índice (ejemplo)**

~~~md
### Outputs de este notebook
- personas_ingresos_Q → ver /catalogo/personas_ingresos_Q
- pobreza_hogares_Q → ver /catalogo/pobreza_hogares_metricas
~~~

---

## 7) QA, reproducibilidad y CI (checklist)

* **Determinismo**: fijar semillas; registrar `source_repo@commit`.
* **Contratos IES** presentes y completos.
* **Sin NaN inesperados** en claves/medidas tras cada merge.
* **Sin globales**: linterna simple que escanee funciones y alerte usos de variables externas.
* **Tiempo & memoria**: log por fase (carga, transform, export).
* **Doctests/pytest**: tests atómicos para funciones extraídas.

---

## 8) Plantillas rápidas (para pegar)

### 8.1. Header estándar del proyecto

~~~python
# paths y seeds
from pathlib import Path
ROOT = Path(__file__).resolve().parents[1]
DATA = ROOT / "data"
RESULTS = ROOT / "results"
SEED = 123
~~~

### 8.2. Contrato IES dentro del notebook

~~~python
IES = {
  "inputs": ["EPH_personas.csv", "ad_eq.csv", "CB_ipc.parquet"],
  "outputs": ["exports/pobreza_hogares_Q.csv"],
  "depends_on": ["dbml_fuente_de_verdad", "pobreza_hogares_metricas"]
}
print(json.dumps(IES, indent=2))
~~~

### 8.3. Cierre con hash y log

~~~python
import hashlib, json
def sha256_file(path):
    h=hashlib.sha256()
    with open(path,'rb') as f:
        for chunk in iter(lambda: f.read(8192), b''):
            h.update(chunk)
    return h.hexdigest()

out = RESULTS / "exports" / "pobreza_hogares_Q.csv"
print("sha256:", sha256_file(out))
print("commit:", os.environ.get("GIT_COMMIT","<unknown>"))
~~~

---

## 9) Errores típicos (y cómo evitarlos)

* **Funciones que leen globales** → pasá deps por argumentos (ver §3).&#x20;
* **Transformaciones *hard-coded*** → externalizá a config (YAML/JSON).&#x20;
* **Import/NameError** por módulos mal ubicados/nombres repetidos → consolidar `funciones.py`/`modules/*` y reiniciar kernel al mover.&#x20;
* **Estructuras de doc desparejas** → seguir la propuesta de encabezados uniformes para las secciones de datasets.&#x20;

