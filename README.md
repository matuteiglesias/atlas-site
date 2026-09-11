# Poverty Ecosystem Engineering Docs

Sitio de documentación de ingeniería para el ecosistema de medición de pobreza de Argentina.

La función principal de este repositorio es mantener una **vista autoritativa de la arquitectura cross-repo**: qué sistema posee cada responsabilidad científica, qué artefactos cruzan boundaries, qué identidades y clocks deben preservarse, qué contratos están implementados y cuáles siguen siendo target architecture.

> Los repositorios productores siguen siendo autoridad sobre su propia ciencia, implementación, releases y QA. Este sitio es autoridad sobre la arquitectura e integración del ecosistema; no replica ni reemplaza la lógica de producción.

## Entrada recomendada

La documentación nueva y autoritativa vive en [`docs/architecture/`](docs/architecture/):

1. **Authority and engineering principles** — precedencia y reglas de diseño.
2. **System map** — mapa de repositorios y responsabilidades.
3. **Contracts and release chain** — artifacts y handoffs.
4. **Semantics, identities, and clocks** — distinciones que no pueden comprimirse.
5. **Current state and migration** — qué existe hoy vs. target state.
6. **Working-reference policy** — cómo se conserva y subordina el material histórico/retrieved.

La idea rectora es:

> **Rich science inside; boring contracts between systems.**

Cada instrumento puede contener ciencia compleja. La integración entre instrumentos debería reducirse, en lo posible, a releases inmutables con manifest, IDs exactos, QA, limitaciones y checksums.

## Mantenimiento por agentes

Los agentes y maintainers deben empezar por [`AGENTS.md`](AGENTS.md) y [`docs/maintenance/00_START_HERE.md`](docs/maintenance/00_START_HERE.md). Ese bundle define precedencia de evidencia, estados, workflow de refresh y carry state. Los `CODEX_*` y `WORK_PACKET_*` históricos no son la guía operativa actual.

## Ecosistema cubierto

El sitio documenta la integración entre, entre otros:

- `microdatos-EPH-INDEC` — adquisición EPH;
- `income-modeling-eph` — ciencia EPH-only y neutral analysis frame;
- `eph-censo-aligner` — semántica EPH/Censo;
- `samplerCensoARG` — sample/frame censal;
- `encuestador-de-hogares` — target de inferencia EPH -> Censo y welfare;
- `IPC-Argentina` — precios y semántica monetaria;
- `canastasINDEC` — canastas/threshold inputs;
- `indice-pobreza-UBA` — método y estimación de pobreza;
- `argentina-geography` — autoridad geográfica;
- `argentina-poverty-atlas` — consumidor público.

## Qué pasa con la documentación anterior

Este repositorio nació antes que la arquitectura actual y contiene páginas recuperadas sobre métodos, operación, referencias, catálogo, pocket recipes y playbooks. Parte de ese material sigue siendo útil y **no se elimina por defecto**.

Sin embargo, esas páginas quedan subordinadas a la arquitectura de `docs/architecture/`. Son working/reference material salvo que una página identifique explícitamente owner, source, scope, status, date y consistencia con el productor actual.

No se requiere un rewrite masivo. Se promueven o corrigen páginas cuando vuelven a ser relevantes para una decisión o consumidor real.

## Autoridad y precedencia

Ante una discrepancia:

1. el release/contrato del **repo productor** gobierna el significado de un artifact concreto;
2. este sitio gobierna la **arquitectura de integración** y el target boundary entre sistemas;
3. notas, notebooks, scripts históricos y páginas retrieved son evidencia de apoyo.

Una divergencia entre (1) y (2) se registra como deuda/migración; no se oculta.

## Desarrollo local

Requiere Node.js >=18. El repositorio mantiene `package-lock.json`, por lo que la verificación reproducible usa npm:

```bash
npm ci
npm run build
```

La verificación de deployment existente puede ejecutarse con:

```bash
python scripts/verify_deployment_config.py
```

Un build correcto prueba el sitio, no la validez científica ni la frescura de los sistemas documentados.

## Regla de mantenimiento

Un cambio debe mejorar al menos una de estas propiedades:

- claridad de autoridad;
- boundary entre sistemas;
- contrato de artifact;
- trazabilidad de estado actual vs. target;
- semántica/identidad/temporalidad;
- navegabilidad hacia la evidencia autoritativa.

Evitar introducir una nueva abstracción sólo para deduplicar código o documentación. La arquitectura se extrae de responsabilidades científicas reales y consumidores reales.
