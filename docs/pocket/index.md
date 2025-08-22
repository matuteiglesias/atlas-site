---
title: "Pocket — Atajos & micro-recetas"
slug: /pocket
module: pocket
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [pocket, tips, qa, cli, charts, json, geo, ops]
source_repo: <repo-url>
source_path: docs/docs/pocket/index.md
---

Pequeñas guías **copy-paste** para resolver bloqueos comunes (CLI, Pandas, JSON, Geo, Charts, Ops). Son **complemento** de *Métodos* y futuros *Playbooks*; priorizan reproducibilidad, QA y contratos mínimos.

## Cómo usar Pocket
- Buscá por tema (CLI/JSON/Geo/Charts).  
- Cada página arranca con **one-liner**, **inputs/precondiciones** y **snippet mínimo**.  
- Si la receta crece o produce artefactos, migra a *Playbook*.

## Puntos de entrada útiles
- CLI & Ops:  
  - [/docs/pocket/cli_ipynb_search](/docs/pocket/cli_ipynb_search) — grep/find recursivo en `.ipynb`.  
  - [/docs/pocket/find_sed_html](/docs/pocket/find_sed_html) — batch-update HTML (dry-run/backup).  
  - [/docs/pocket/github_secrets_quickref](/docs/pocket/github_secrets_quickref) — listado y uso de secrets.  
  - [/docs/pocket/git_push_internals](/docs/pocket/git_push_internals), [/docs/pocket/git_large_files](/docs/pocket/git_large_files), [/docs/pocket/selenium_chromedriver](/docs/pocket/selenium_chromedriver).

- JSON & Serialización:  
  - [/docs/pocket/json_quickref](/docs/pocket/json_quickref) — cargar/navegar anidamientos.  
  - [/docs/pocket/json_timestamps_encoder](/docs/pocket/json_timestamps_encoder) — encoder + checklist.  
  - [/docs/pocket/json_deep_merge_qseries](/docs/pocket/json_deep_merge_qseries) — merge jerárquico Q-series.  
  - [/docs/pocket/json_style_color_mapbox](/docs/pocket/json_style_color_mapbox) — parches de color/escala (Mapbox).

- Pandas / ETL:  
  - [/docs/pocket/log_merge_qc](/docs/pocket/log_merge_qc) — logging + QA pre/post-merge.  
  - [/docs/pocket/concat_csvs](/docs/pocket/concat_csvs), [/docs/pocket/loading_loop_datasets](/docs/pocket/loading_loop_datasets).  
  - Import & DataFrame tips: [/docs/pocket/import_gotchas](/docs/pocket/import_gotchas), [/docs/pocket/dataframe_drop_correction](/docs/pocket/dataframe_drop_correction), [/docs/pocket/insert_column_quickref](/docs/pocket/insert_column_quickref).

- Charts & Geo:  
  - [/docs/pocket/chart_scaffold](/docs/pocket/chart_scaffold) — secuencia fija Matplotlib.  
  - [/docs/pocket/dataframe_styling](/docs/pocket/dataframe_styling), [/docs/pocket/interactive_ts_js](/docs/pocket/interactive_ts_js).  
  - Geo pre-checks y overlays: [/docs/pocket/geopandas_plot_prechecks](/docs/pocket/geopandas_plot_prechecks), [/docs/pocket/geo_overlay_scatter_box](/docs/pocket/geo_overlay_scatter_box), [/docs/pocket/geopandas_hatches](/docs/pocket/geopandas_hatches).

## Cross-refs
- Métodos: [/metodos/charts_and_styles](/metodos/charts_and_styles), [/metodos/etl_json_policies](/metodos/etl_json_policies), [/metodos/geo_integration_methods](/metodos/geo_integration_methods), [/metodos/temporal_toolkit](/metodos/temporal_toolkit)  
- Operación: [/operacion/ci_github_actions](/operacion/ci_github_actions), [/operacion/logging_etl](/operacion/logging_etl)
