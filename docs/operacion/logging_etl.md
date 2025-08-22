---
id: operacion_logging_etl
title: "Operación — logging y trazabilidad en ETL"
slug: /operacion/logging_etl
module: operacion
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [ops, logging, etl, performance]
source_repo: <repo-url>
source_path: docs/operacion/logging_etl.md
---

#
Visibilidad de progreso y performance en scripts ETL: *elapsed time*, etapas, merges y outputs. <!-- removed contentReference -->

## Patrón de logging
- Envoltura `log_message(msg, start_time)` con timestamp y duración por bloque. <!-- removed contentReference -->
- Mensajes por **ciclo temporal** (Q/año), **merge** (columnas comunes) y **export** (ruta final). <!-- removed contentReference -->

## Contrato mínimo
- **Nivel**: INFO por defecto; DEBUG para muestra/shape de tablas.
- **Salida**: consola + archivo rotado por fecha.
- **Campos**: etapa, Q/año, grouper/base_str, filas afectadas.

## Ejemplo base
Indicar hooks “Carga → Transformación → Merge → Guardado” con tiempos y filenames. <!-- removed contentReference -->
