---
id: workflow-diagrams
title: "Arquitectura de Datos & Diagramas de Flujo"
slug: /atlas/workflow-diagrams
sidebar_label: Workflow Diagrams
description: Vista consolidada de diagramas (Graphviz), guía de documentación concisa y notas de datasets geoespaciales para el Atlas.
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';
import Admonition from '@theme/Admonition';

## Propósito

Esta página concentra:
- Diagramas de flujo del pipeline (Graphviz) y su estado de consolidación.
- Pautas cortas para **documentar flujos** (data flow + processing highlights).
- Puntos de anclaje a **datasets geoespaciales** usados en el Atlas.

## Diagrama maestro (snapshot)

> Sube tus SVG/PNG a `static/img/workflows/` y enlaza aquí la versión vigente.

![Atlas Master Workflow](/img/workflows/atlas-master.svg)

<Admonition type="tip" title="Consejo de mantenimiento">
Mantener un **único** diagrama maestro y subdiagramas por módulo. Los PR que cambien el pipeline deben actualizar las imágenes y anotar el cambio en el _Changelog_ al final de la página.
</Admonition>

## Fragmentos Graphviz (para regenerar)

<Admonition type="info" title="Cómo usar">
Guarda estos _snippets_ `.dot` en `assets/graphviz/` y compílalos a SVG en CI o localmente.
</Admonition>

<Tabs>
  <TabItem value="etl" label="ETL baseline (dot)">
  

</TabItem>
<TabItem value="orchestration" label="Orquestación (dot)">

~~~dot
digraph ETL {
  rankdir=LR;
  node [shape=box, style=rounded];
  subgraph cluster_ingest {
    label="Ingesta";
    eph [label="EPH microdatos"];
    censo [label="Censo 2010"];
  }
  prep [label="Preprocesamiento"];
  model [label="Modelado / Métricas"];
  geo [label="Geo join / Export GeoJSON"];
  pub [label="Publicación (GCS/Mapbox)"];

  eph -> prep; censo -> prep;
  prep -> model -> geo -> pub;
}

digraph Orchestration {
  rankdir=LR;
  node [shape=box, style=rounded];
  source [label="Repo microdatos"];
  dispatch [label="repository_dispatch"];
  atlas [label="Atlas CI/CD"];
  build [label="Reproceso / Build"];
  publish [label="Publicación artefactos"];
  source -> dispatch -> atlas -> build -> publish;
}
~~~

  </TabItem>
</Tabs>

## Data Flow & Processing Highlights (plantilla mínima)

Usar esta estructura corta en cada sección/modo del pipeline:

* **Pipeline Diagram**: insertar el subdiagrama correspondiente (o link al SVG).
* **Step Descriptions**: 3–6 bullets con los pasos principales (p. ej. *“Preprocessing of incomes”*, *“Poverty calculations”*).
* **Highlights**: listar transformaciones/cleaning/feature engineering clave y supuestos operativos.

> Esta pauta resume el “qué mostrar” en cada página de módulo para que la documentación sea uniforme y rápida de leer.

## Datasets geoespaciales (puntos de anclaje)

Utilizamos capas oficiales (IGN/CONICET) como base para joins y visualizaciones. Documenta para cada capa:

* **Nombre / ruta de origen** (ej.: shapefile oficial de fracciones/departamentos).
* **Atributos clave** (IDs, nombres normalizados) y **CRS** canónico.
* **Contratos** de publicación (p. ej., cómo derivan los GeoJSON de salida).

> Ejemplo (descriptivo):
>
> * **Fuente**: fracciones 2010 (CONICET, shapefile disuelto).
> * **Clave**: IDs de provincia/departamento/fracción normalizados a nuestro keyring.
> * **Uso**: join con métricas del Atlas y export a `*.geojson` para Mapbox.

## Pendientes de consolidación

* **Diagramas**: unificar variantes en un **único** documento maestro y subdiagramas por módulo.
* **Encabezados**: completar secciones marcadas como “headers-only” con su diagrama/desc.
* **Rutas/CRS**: revisar que las rutas de origen y el CRS estén documentados de forma estable.

## Changelog (breve)

* *YYYY-MM-DD*: Actualización de diagrama maestro y enlaces a subdiagramas.
* *YYYY-MM-DD*: Añadida plantilla de “Data Flow & Processing Highlights”.
* *YYYY-MM-DD*: Revisadas referencias a datasets geoespaciales.
