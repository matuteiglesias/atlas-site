---
id: referencia_nucleo_censo_2010_variables
title: "Censo 2010: variables y armonización Censo↔EPH"
slug: /referencia/nucleo/censo_2010_variables
module: referencia-nucleo
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [censo, eph, variables, armonizacion]
source_repo: <repo-url>
source_path: docs/referencia/nucleo/censo_2010_variables.mdx
time_grain: A
join_policy: "Censo anual; EPH trimestral; adaptación vía claves geo y armonización de categorías"
schema_ref: atlas.dbml#censo   # actualizar al ancla real del DBML
---

Página de referencia para: identificadores, variables clave de vivienda/persona y tabla de armonización Censo↔EPH. Sirve como base para población sintética anual y para joins con EPH.

## Identificadores y claves
- `VIVIENDA_REF_ID` identificador de vivienda  
- `HOGAR_REF_ID` identificador de hogar  
- `PERSONA_REF_ID` identificador de persona  
- `RADIO_REF_ID`, `DPTO`, `PROV`, `AGLOMERADO` claves geográficas  
- `ANO4` año de referencia (si corresponde en tus extractos)  
- `URP` zona urbana/rural (definir dominio exacto)

> Contrato de datos (mínimo): unicidad de `VIVIENDA_REF_ID`; consistencia de `HOGAR_REF_ID`↔`VIVIENDA_REF_ID`; `PERSONA_REF_ID` único por persona; dominios cerrados en variables categóricas.

## Variables de vivienda y hogar
Completar “Descripción” y “Dominio” con el cuestionario base del Censo y tu código de ingestión.

| Variable | Descripción (completar) | Tipo | Dominio resumido | Nota EPH |
|---|---|---:|---|---|
| `V01` | Tipo de vivienda particular | int | [1..8] | mapeo a categorías EPH (ver abajo) |
| `H05` | Material de pisos | int | […] | posibles colapsos de categorías |
| `H06` | Material de techo | int | […] | mapeo pendiente (ver abajo) |
| `H07` | Cielorraso | int | […] |  |
| `H08` | Agua en vivienda/terreno | int | […] |  |
| `H09` | Fuente del agua | int | […] | agregación a “Otra fuente” en EPH (reglas abajo) |
| `H10` | Tiene baño | int | […] |  |
| `H11` | Baño con desagüe | int | […] |  |
| `H12` | Tipo de desagüe | int | […] |  |
| `H13` | Baño compartido | int | […] | verificar lógica de mapeo |
| `H14` | Combustible de cocina | int | […] | mapeo pendiente y posible colapso |
| `H15` | Dormitorios | int | ≥0 |  |
| `H16` | Habitaciones (sin cocina/baño) | int | ≥0 | clip a 9 para compatibilidad EPH |
| `PROP` | Régimen de propiedad | int | […] |  |
| `TOTPERS` | Total personas hogar | int | ≥1 | control de consistencia hogar |

## Variables de persona (núcleo)
| Variable | Descripción (completar) | Tipo | Dominio resumido |
|---|---|---:|---|
| `P02` | Sexo | int | […] |
| `P03` | Edad | int | ≥0 |
| `P05` | Nacionalidad | int | […] |
| `P07` | Sabe leer | int | […] |
| `P08` | Asistencia escolar | int | […] |
| `P09` | Nivel cursa o cursó | int | […] |
| `P10` | Nivel completo | int | […] |
| `CONDACT` | Condición de actividad | int | […] |

> Otras variables de uso en etapas posteriores: `CAT_OCUP`, `CAT_INAC`, `CH07` (estado civil), banderas de ingresos (`INGRESO`, `INGRESO_NLB`, `INGRESO_JUB`, `INGRESO_SBS`), y variables laborales (`PP07*`, `PP08D1`, etc.) cuando cruces con EPH.

## Armonización Censo↔EPH (tablas de mapeo)

### V01 Tipo de vivienda particular
| Censo | Etiqueta Censo | EPH destino | Etiqueta EPH |
|---:|---|---:|---|
| 1 | Casa | 1 | Casa |
| 2 | Rancho | 6 | Otros |
| 3 | Casilla | 6 | Otros |
| 4 | Departamento | 2 | Departamento |
| 5 | Pieza en inquilinato | 3 | Pieza de inquilinato |
| 6 | Pieza en hotel familiar o pensión | 4 | Pieza en hotel/pensión |
| 7 | Local no construido para habitación | 5 | Local no construido para habitación |
| 8 | Vivienda móvil | 6 | Otros |

### H06 Material de techo  <!-- TODO -->
Notas:
- Trazar correspondencia uno a uno con categorías EPH equivalentes.  
- Verificar el caso “Otro” y el rótulo “N/S. Depto en propiedad horizontal” que figura en notas, suena inconsistente para esta variable.  
- Completar tabla una vez validadas las categorías oficiales.

### H09 Fuente del agua
Regla de colapso a “Otra fuente” en EPH:
- 4 Pozo → “Otra fuente”  
- 5 Cisterna → “Otra fuente”  
- 6 Lluvia/río/canal/arroyo/acequia → “Otra fuente”

### H16 Habitaciones
- Política: clip en 9 (valores > 9 se recortan a 9).

### H14 Combustible de cocina  <!-- TODO importante -->
Propuesta preliminar (revisar con catálogo EPH):
- 2 Gas a granel (zeppelin) → “Otro”  
- 3 Gas en tubo → “Gas de tubo/garrafa”  
- 4 Gas en garrafa → “Otro”  ← revisar, podría unificarse con “Gas de tubo/garrafa”  
- 5 Electricidad → “Otro”  
- 6 Leña o carbón → “Kerosene/leña/carbón”  
- 7 Otro → “Otro”  
- 8 … → 9 (NS/NC)  ← confirmar significado de “8” en Censo

> Recomendación: documentar el catálogo completo de categorías Censo y EPH y explicitar la política de colapso por variable, con pruebas de cobertura (todas las categorías de Censo deben estar mapeadas).

## Contrato mínimo de dataset
- **Archivo de origen**: `censos/Censo_2010/Censo_2010_Manzanas_radio.csv`  
- **Columnas mínimas**: identificadores (`VIVIENDA_REF_ID`, `HOGAR_REF_ID`, `PERSONA_REF_ID`), claves geo (`RADIO_REF_ID`, `DPTO`, `PROV`, `AGLOMERADO`), variables de vivienda `H05`–`H16`, `V01`, `PROP`, variables de persona `P01`–`P10`, `CONDACT`.  
- **Checksum**: `<sha256>`  
- **Frecuencia**: estático (año 2010)  
- **Licencia/Fuente**: indicar organismo y términos de uso

## QA mínimo
1) **Cobertura de mapeos**: 100 por ciento de categorías de Censo con destino en EPH, sin clases huérfanas.  
2) **Dominios válidos**: validación por variable (fallar build si aparecen códigos fuera de catálogo).  
3) **Reglas específicas**: clip de `H16` aplicado y testeado; colapsos de H09 y H14 exhaustivos.  
4) **Claves**: unicidad de `VIVIENDA_REF_ID`; consistencia `HOGAR_REF_ID` dentro de `VIVIENDA_REF_ID`; consistencia geo.  
5) **Reportes**: tabla resumen de frecuencias por categoría antes y después de la armonización, con diferencias absolutas y relativas.

## Pendientes
- Completar descripciones y dominios de `V01`, `URP`, `H05`–`H16`, `PROP`.  
- Cerrar tablas de H06 y H14 con el catálogo oficial EPH y validar cobertura.  
- Enlazar a `atlas.dbml#censo` con el bloque real del DBML y a la ficha de población sintética anual.
