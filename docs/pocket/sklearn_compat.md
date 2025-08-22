---
id: pocket_sklearn_compat
title: "Scikit-Learn — compatibilidad de modelos (pickle/joblib)"
slug: /pocket/sklearn_compat
module: apendice-pocket
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [sklearn, pickle, joblib, onnx, versionado, mlops]
source_repo: <repo-url>
source_path: docs/pocket/sklearn_compat.md
---

## Problema
Modelos serializados con **pickle/joblib** pueden **no cargar** o avisar de riesgos cuando la **versión de scikit-learn difiere** entre quien guardó y quien lee. Esto afecta reproducibilidad, despliegue y CI/CD.

## Opciones (de menor a mayor fricción)
1. **Cargar e ignorar la advertencia** si el modelo funciona *y* pasa tests de sanidad. Riesgo: cambios sutiles entre versiones.  
2. **Entorno “pinneado”**: *downgrade* temporal a la versión original → cargar → usar.  
3. **Re-serializar** en el entorno original y documentar dependencias.  
4. **Migrar a un formato más estable** (p.ej., **ONNX**) cuando el tipo de modelo lo permita.  
5. **Re-entrenar** en el entorno objetivo y volver a guardar (ideal si tenés datos/código).

> Regla práctica: si **no vas a re-entrenar**, priorizá *entorno pinneado + re-serialización* y dejá huella de versiones.

## Procedimiento recomendado (paso a paso)
1) **Identificá la versión fuente**  
- Si tenés metadata, usala; si no, probá versiones candidatas.

2) **Replicá el entorno & carga**
~~~bash
python -m venv .venv-model && source .venv-model/bin/activate
pip install "scikit-learn==<VERSION_ORIGEN>"
# opcional: joblib, numpy, scipy en versiones compatibles
~~~

3. **Valida y re-serializa**

~~~python
import joblib, platform, sklearn, sys
mdl = joblib.load("modelo.pkl")         # o pickle.load(...)
joblib.dump(mdl, "modelo_repacked.joblib")

meta = {
  "sklearn": sklearn.__version__,
  "python": sys.version.split()[0],
  "platform": platform.platform()
}
print(meta)  # guarda esto junto al artefacto
~~~

4. **Opcional: exportar a ONNX** (si aplica)

~~~python
# ejemplo genérico; requiere skl2onnx e inferir tipos de entrada
from skl2onnx import to_onnx
onx = to_onnx(mdl, initial_types=[('X', FloatTensorType([None, n_features]))])
with open("modelo.onnx", "wb") as f: f.write(onx.SerializeToString())
~~~

> No todos los estimadores están soportados; verificá compatibilidad.

5. **Documentá el entorno**
   Guarda `requirements.txt`, versión de sklearn, hash de datos/commit, métricas y un **model\_card.md** simple.

~~~bash
pip freeze > requirements.txt
~~~

## Snippets útiles

* **Downgrade puntual**:

~~~bash
pip install "scikit-learn==1.0.2"
~~~

* **Wrapper de carga con guardas**:

~~~python
import warnings, joblib
with warnings.catch_warnings():
    warnings.simplefilter("always")  # o "ignore" bajo control de QA
    mdl = joblib.load("modelo.joblib")
~~~

## Checklist de QA (mínimo)

* `predict`/`score` reproducen métricas de referencia (± tolerancia).
* No hay *feature drift*: columnas, orden, `dtype` coinciden con lo esperado.
* Artefactos guardados con: **modelo**, **requirements.txt**, **metadata.json** (versiones, hash, fecha), y **model\_card.md**.

## Nota de operación

Para evitar esta clase de incidentes:

* **Pin de versiones** (fijar sklearn y dependencias) en entrenamiento e inferencia.
* Serializar con **joblib** y guardar **metadata** junto al artefacto.
* Considerar **ONNX** o **pipelines de retraining** automatizados cuando el cambio de versión sea inevitable.
