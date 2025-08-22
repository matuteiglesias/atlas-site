---
id: playbooks_artefactos_modelos_rfc
title: "Artefactos intermedios de modelos RFC"
slug: /playbooks/artefactos_modelos_rfc
module: playbooks
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [modelos, artefactos, rfc, reproducibilidad, frac]
source_repo: <repo-url>
source_path: docs/playbooks/artefactos_modelos_rfc.mdx
---


Estandar para artefactos intermedios de entrenamiento y evaluacion de modelos. Aplica a playbooks de entrenamiento en EPH y al nowcast mensual.

## Patron de nombres
- Formato: `RFC[STAGE]_[FRAC]_[DATE]_[COUNTRY].csv`
  - `[STAGE]`: etapa del pipeline, ejemplo `1`, `2`, `3`
  - `[FRAC]`: fraccionamiento, ejemplo `0.01`
  - `[DATE]`: fecha ISO `YYYY-MM-DD`
  - `[COUNTRY]`: ISO3, ejemplo `ARG`
- Ejemplo: `RFC1_0.01_2018-05-15_ARG.csv`

~~~yaml
exports:
  - path: /model/results/RFC1_0.01_2018-05-15_ARG.csv
    sha256: "<sha256>"
    stage: 1
    date: "2018-05-15"
    country: "ARG"
    frac: 0.01
    source_repo: "<org/repo>@<commit>"
    seed: 123
    model_version: "<tag>"
~~~

## Reglas de reproducibilidad

* Fijar semillas y versionar dependencias del entorno
* Registrar `source_repo@commit`, `seed`, `model_version`
* Inmutabilidad: si cambia el contenido, emitir un nuevo archivo con nuevo `sha256`

## QA minimo

1. Forma esperada de columnas y tipos por etapa
2. Coherencia de metricas frente a baseline de la etapa anterior
3. Ausencia de NaN en columnas clave
4. Compatibilidad de join con datasets canonicos si aplica

## Politicas de almacenamiento

* Directorio `model/results` por defecto
* No publicar `RFC*` en la carpeta de exports canonicos
* Retencion acorde al ciclo de evaluacion del modelo

## Referencias cruzadas

* Playbook 9 Entrenamiento y evaluacion en EPH
* Playbook 10 Nowcast mensual
* Politicas de versionado y fijacion de entorno

~~~
