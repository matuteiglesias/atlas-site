---
title: "Playbooks — Índice & convenciones"
slug: /playbooks
module: playbooks
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [playbooks, etl, geo, cpi, orchestration, publishing]
source_repo: <repo-url>
source_path: docs/playbooks/index.md
---

> Estado: esta sección lista **los playbooks planificados** y su propósito. Publicamos el índice primero; las páginas individuales se liberan en el próximo sprint. Mientras tanto, usá **Métodos**, **Referencia** y **Pocket** como base operativa.

## Qué es un playbook
Flujo **end-to-end** reproducible que deja artefactos versiónados. Plantilla estándar (cuando se publiquen):  
*Pre-reqs → Inputs → Pasos → QA → Fallos comunes → Outputs → Re-run (idempotencia).*

## Cross-refs inmediatos (ya disponibles)
- Métodos: [/docs/metodos/temporal_toolkit](/docs/metodos/temporal_toolkit), [/docs/metodos/etl_json_policies](/docs/metodos/etl_json_policies), [/docs/metodos/geo_integration_methods](/docs/metodos/geo_integration_methods), [/docs/metodos/charts_and_styles](/docs/metodos/charts_and_styles), [/docs/metodos/notebooks_estructura_refactor](/docs/metodos/notebooks_estructura_refactor)
- Referencia: [/docs/referencia/nucleo/dbml_fuente_de_verdad](/docs/referencia/nucleo/dbml_fuente_de_verdad), [/docs/referencia/nucleo/censo_2010_variables](/docs/referencia/nucleo/censo_2010_variables), [/docs/referencia/geo/geometrias_geoespaciales](/docs/referencia/geo/geometrias_geoespaciales)
- Operación: [/docs/operacion/ci_github_actions](/docs/operacion/ci_github_actions), [/docs/operacion/logging_etl](/docs/operacion/logging_etl), [/docs/operacion/results_path_naming](/docs/operacion/results_path_naming)

## Roadmap de Playbooks (planificados)
1. **ETL baseline métricas de pobreza (EPH)** — ingestión, normalización, deflactores, quick-plots.  
2. **Aggregates → GeoJSON (Mapbox)** — agregación, CRS/joins, contrato de salida.  
3. **Acumulación trimestral / Upsert** — merge determinístico + no-ops en re-run.  
4. **Orquestación de muestreos** — bucles por año, logs/exit codes, variante Dask/CLI.  
5. **Carto Recipe: mapa de circuitos** — pipeline parametrizado, export PNG.  
6. **CNE circuitos → IGN dpto/prov** — asociación por área dominante + validación.  
7. **Radios censales → aglomerados EPH** — join cookbook + anti-join checks.  
8. **CBA/CBT regionales** — flujo mensual y trimestral, publicación.  
9. **Train & evaluate (EPH)** — seeds, métricas y artefactos persistidos.  
10. **Nowcast mensual de pobreza (stub)** — Q→M, estacionalidad, regresiones diagnósticas.

## Convenciones a respetar en todos los playbooks
- **Idempotencia**: checksum + atomic write + no-overwrite.  
- **Claves & temporal**: `HOGAR_REF_ID`, `Q` (trimestral), `YEAR` (geo anual).  
- **CRS canónico**: ver [/docs/referencia/geo/geometrias_geoespaciales](/docs/referencia/geo/geometrias_geoespaciales).  
- **Logs & QA**: patrón `log_message` + verificación de filas pre/post-merge.  
- **Contratos mínimos**: naming, esquema, ejemplo de archivo, checksum, política temporal, citación.
