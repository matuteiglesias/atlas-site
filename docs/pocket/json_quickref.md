---
id: pocket_json_quickref
title: "JSON — quick-ref Python"
slug: /pocket/json_quickref
module: apendice-pocket
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [json, python, quickref, io]
source_repo: <repo-url>
source_path: docs/pocket/json_quickref.md
---

## Propósito
Cargar un JSON, navegar anidamientos comunes y rescatar claves sin romper el pipeline. (Ver también: **/metodos/etl_json_policies** para contratos y serialización.)

## Cargar y acceder (patrón mínimo)
~~~python
import json
from pathlib import Path

# 1) Cargar desde archivo
with Path("path/to/file.json").open("r", encoding="utf-8") as f:
    data = json.load(f)

# 2) Acceso directo a claves anidadas (ejemplo típico)
desired = data["true"]["Total"]["mean"]["data"]

# 3) Acceso defensivo (evita KeyError y documenta defaults)
d_true   = data.get("true", {})
d_total  = d_true.get("Total", {})
d_mean   = d_total.get("mean", {})
desired2 = d_mean.get("data", [])
~~~

Este patrón resume tus ejemplos de acceso anidado en estructuras tipo `data["true"]["Total"]["mean"]["data"]`.&#x20;

### Variantes útiles

* **`json.loads`** si la fuente es una **cadena** (no un archivo).
* **Encoding**: fuerza `utf-8` para evitar sorpresas en contenedores.
* **Tipos numéricos**: valida que los números entren como `int/float` (no strings).

## Contrato mínimo (lectura segura)

* **Estructura esperada** documentada en la ficha (ruta/clave raíz).
* **Defaults explícitos** en `.get()` para listas/dicts vacíos.
* **Errores claros**: encapsulá el acceso en una función que levante `ValueError` con contexto si falta una clave crítica.

## Anti-patrones frecuentes

* **Encadenar `[...]` sin checks** → rompe ante faltantes (preferí `.get`).
* **Modificar `data` in-place** durante iteraciones profundas → hacé copias locales o componé un nuevo dict.
* Exponer paths absolutos o sensibles en logs.

## QA rápido

* `assert isinstance(desired, (list, dict))` según contrato.
* `sum(1 for _ in json.dumps(data))` no debería explotar; si falla, revisa serialización (ver **etl\_json\_policies**).

