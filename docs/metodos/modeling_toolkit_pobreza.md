---
id: metodos_modeling_toolkit_pobreza
title: "Modeling toolkit — regresión lineal simple para pobreza"
slug: /metodos/modeling_toolkit_pobreza
module: metodos
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [modeling, regression, poverty, emae, ripte, cba, ipc]
source_repo: <repo-url>
source_path: docs/metodos/modeling_toolkit_pobreza.md
---


Utilidad mínima (pero rigurosa) para **ajustes lineales** orientados a series de pobreza. Sirve para exploración, *sanity checks* y como baseline reproducible —no reemplaza los modelos del pipeline principal. El contexto y motivación vienen del análisis con Censo 2010, integración geoespacial y métricas derivadas. <!-- removed contentReference -->

---

## 1) Especificación del modelo (baseline)
Tomamos como dependiente una serie mensual/trimestral de **pobreza** (conteo o tasa). Predictoras: **empleo** (público/privado/resto), **actividad** (EMAE, centrada) y **asequibilidad** (CB/RIPTE, IPC/RIPTE; centradas). Señales esperadas: empleo ↓ pobreza (coeficientes negativos), costo de vida relativo ↑ pobreza (coeficientes positivos), actividad ↑ ↓ pobreza. <!-- removed contentReference -->

~~~latex
\[
\text{pobres} = C - \alpha_1 (\# \text{emp\_pub}) - \alpha_2 (\# \text{emp\_priv}) - \alpha_3 (\# \text{emp\_resto})
+ \beta \, (\text{EMAE} - \overline{\text{EMAE}})
+ \gamma \left(\frac{\text{CB}}{\text{RIPTE}} - \overline{\frac{\text{CB}}{\text{RIPTE}}}\right)
+ \delta \left(\frac{\text{IPC}}{\text{RIPTE}} - \overline{\frac{\text{IPC}}{\text{RIPTE}}}\right)
\]
~~~


- Racional económico y consideraciones: **interacciones** potenciales, **efectos rezagados** (p. ej. EMAE→pobreza con retraso), y **multicolinealidad** (EMAE vs empleo); evaluar regularización si hace falta. <!-- removed contentReference -->

---

## 2) Contrato mínimo (inputs/outputs)
**Entradas**
- Series `indice_tiempo` (M o Q) alineadas y sin saltos; columnas:
  - `Empleo_Publico`, `Empleo_Privado`, `Empleo_Resto`
  - `EMAE` (o desviación vs. media)
  - `CB_RIPTE`, `IPC_RIPTE` (o versiones centradas)
  - `valor` (pobres: número o tasa)
- Opcional: lags p (`EMAE_lag1`, …), *dummies* estacionales.

**Salida**
- `fitted_valor`, `residuo`, métricas (`R2`, MAE, RMSE), y **coeficientes/intercepto**.
- Artefactos recomendados:
  - `model_linear.json` (coeficientes, signos, *standard errors* si SM/OLS)
  - `fitted.parquet` (con fitted/residuos)
  - (opcional) `model.pkl` scikit-learn, con *pin* de versión.

**Garantías**
- Alineación temporal exacta; sin NaNs en **X** tras el preprocesado seleccionado (ver §3).  
- Trazabilidad: `source_repo@commit`, ventana temporal y escalas usadas. <!-- removed contentReference -->

---

## 3) NaNs: dos rutas operativas (eligir una)
> Evitá “parches invisibles”. Documentá la política elegida en el notebook.

**Ruta A — `dropna` explícito (simple y clara)**
- Filtrar filas con NaNs en X e y; ajustar y reportar *n* efectivo.  
- Ventaja: reproducible y transparente; Desventaja: reduce muestra si faltantes son frecuentes.

**Snippet (A)**
~~~python
from sklearn.linear_model import LinearRegression
X_cols = ['Empleo_Privado','Empleo_Publico','Empleo_Resto',
          'EMAE_Centered','CB_RIPTE_Centered','IPC_RIPTE_Centered']
df_ = dep_var.dropna(subset=X_cols + ['valor']).copy()
X, y = df_[X_cols].values, df_['valor'].values
m = LinearRegression().fit(X, y)
df_['fitted_valor'] = m.predict(X)
~~~



**Ruta B — *masked arrays* (conservar estructura)**

* Usar *masked arrays* para ignorar NaNs al ajustar, sin reindexar.
* Ventaja: preserva el `indice_tiempo` original; Desventaja: mayor complejidad.

**Snippet (B)**

~~~python
import numpy as np
from numpy import ma
from sklearn.linear_model import LinearRegression

X = np.array([ma.masked_invalid(dep_var[c].values) for c in X_cols]).T
y = ma.masked_invalid(dep_var['valor'].values)
m = LinearRegression().fit(ma.compress_rows(X), ma.compressed(y))
mask_valid = ~dep_var[X_cols].isnull().any(axis=1)
dep_var.loc[mask_valid, 'fitted_valor'] = m.predict(ma.compress_rows(X))
~~~



---

## 4) Preparación de datos (mínimos)

* **Centrado**: restar la media a EMAE y a las razones CB/RIPTE, IPC/RIPTE (facilita interpretación de C).&#x20;
* **Escalas**: usar unidades consistentes (empleo en miles, pobreza en miles o %).
* **Alineación**: si mezclás mensual/Q, aplicá tu adaptador temporal (ver toolkit temporal).
* **Lags e interacciones (opcional)**: probar 1–2 rezagos en EMAE/empleo; evaluar interacción `EMAE × empleo`.&#x20;

---

## 5) Ajuste y diagnóstico

**Ajuste (sklearn)**

~~~python
from sklearn.metrics import r2_score, mean_absolute_error, mean_squared_error
yhat = m.predict(X)
R2 = r2_score(y, yhat); MAE = mean_absolute_error(y, yhat); RMSE = mean_squared_error(y, yhat, squared=False)
~~~

**Chequeos mínimos**

* **Signos esperados**: α’s (empleo) < 0; γ, δ (CB/RIPTE, IPC/RIPTE) > 0; β (EMAE) < 0.&#x20;
* **Residuos**: evaluar patrón temporal (autocorrelación); si fuerte, considerar HAC o diferencias.
* **Multicolinealidad**: inspeccionar correlaciones/vif; considerar Ridge/Lasso si inestables.&#x20;

**Plot de ajuste** (opcional, para inspección rápida)

~~~python
import matplotlib.pyplot as plt
ax = dep_var.set_index('indice_tiempo')[['valor','fitted_valor']].plot()
ax.set_title('Valor vs Ajuste (lineal)'); ax.grid(True)
~~~

---

## 6) Export y trazabilidad

* **`fitted.parquet`** con `indice_tiempo, valor, fitted_valor, residuo`.
* **`model_linear.json`**: coeficientes, intercepto, métricas, ventana temporal, columnas usadas.
* **`model.pkl`** (opcional) + *pin* de scikit-learn.

> Mantener compatibilidad con políticas de exportación JSON (encoder de timestamps, escritura atómica). Ver *metodos/etl\_json\_policies*.

---

## 7) Límites y extensiones

* Este baseline **no** modela estructura de error ni cambios de régimen; úsalo como **“termómetro”** y para validar signos/ordenes de magnitud.
* Para producción, migrar a: lags seleccionados por criterio, dummies estacionales, regularización, y validación *out-of-sample*.

---

## 8) Errores comunes

* Ajustar con NaNs “silenciosos” → elegir **Ruta A o B** y dejar rastro en logs.&#x20;
* No centrar CB/RIPTE e IPC/RIPTE → intercepto poco interpretable.&#x20;
* Confluir *conteo* y *tasa* de pobreza sin escalar → coeficientes sin sentido.
* Omitir la trazabilidad temporal → difícil comparar runs.

---

## 9) Referencias cruzadas

* **Temporal toolkit** (alineación M↔Q, desestacionalización básica).
* **Catálogo** (definiciones de métricas) y **DBML** (claves y granularidades).
* **ETL JSON** (contratos de exportación y merges jerárquicos).
 