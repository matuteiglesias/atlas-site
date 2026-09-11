---
title: "Poverty Ecosystem Engineering"
sidebar_position: 1
slug: /
version: 1.1.0
status: current
owners: [poverty-ecosystem-engineering]
source_repo: https://github.com/matuteiglesias/atlas-pobreza-docs
source_path: docs/index.md
---

# Poverty Ecosystem Engineering

Este sitio es la **memoria de ingeniería y arquitectura cross-repo** del ecosistema argentino de medición de pobreza.

La pregunta central no es “¿en qué notebook está este cálculo?”, sino:

> **¿qué sistema tiene autoridad para transformar qué evidencia, bajo qué contrato, y qué debe quedar explícito cuando el resultado cruza al siguiente instrumento?**

## Empezar por la arquitectura

La sección **Engineering architecture** es la entrada autoritativa:

- [Authority and engineering principles](./architecture/00-authority-and-principles.md)
- [System map](./architecture/01-system-map.md)
- [Contracts and release chain](./architecture/02-contracts-and-release-chain.md)
- [Semantics, identities, and clocks](./architecture/03-semantics-identities-and-clocks.md)
- [Current state and migration](./architecture/04-current-state-and-migration.md)
- [Working-reference policy](./architecture/05-working-reference-policy.md)
- [Engineering backlog](./architecture/06-engineering-backlog.md)
- [EPH, Census sampling and transport science](./architecture/07-eph-census-scientific-decomposition.md)
- [EncUESTADOR functional contract](./architecture/08-encuestador-functional-contract.md)
- [Automation and refresh loop](./architecture/09-automation-and-refresh-loop.md)

La regla rectora sigue siendo:

> **Rich science inside; boring contracts between systems.**

## Estado del sistema — 11 Sep 2026

La cadena central ya no es solamente target architecture. A nivel de investigación se ha probado una integración real que conecta:

```text
EPH 2024-Q3
    +
CPV-2010 target-year sample
    ↓
real semantic feature plane
    ↓
household-safe transport science
    ↓
full Census research commissioning
    ↓
predictive household welfare
    ↓
quarter-specific poverty inputs
    ↓
predictive FGT measurement
```

Esto es **research commissioning**, no una publicación de estadísticas oficiales.

Los límites principales hoy ya no son “falta construir el pipeline”, sino soporte/transport shift, temporalidad de estados donor-vintage, uncertainty agregada y replicación en otro período.

El último tramo hacia publicación todavía está en transición: el productor real provincia+nación de Poverty y el ingest de release real en Atlas están validados en PRs abiertos y no deben describirse como comportamiento canónico de `main` hasta que se integren.

## Separaciones que importan

La arquitectura mantiene explícitamente:

```text
EPH-only science
!=
EPH -> Census transport

semantic comparability
!=
target-period temporal validity

EPH survey weights
!=
Census selection probability
!=
Poverty analysis semantics

point household welfare
!=
predictive household welfare distribution
!=
aggregate poverty-estimate uncertainty
```

Estas distinciones evitan que un artifact válido propague una interpretación equivocada al siguiente sistema.

## Material anterior: útil, pero subordinado

Métodos, Operación, Referencia, Catálogo, Pocket y Playbooks históricos se conservan como **working/reference material**. Algunas páginas son excelentes ayudas prácticas; otras documentan estados ya superados.

No definen por sí solas un boundary, artifact contract o autoridad científica. Se promueven sólo cuando vuelven a ser relevantes y pueden anclarse en evidencia actual.

## Para quién es

Este sitio sirve a:

- ingenieros que necesitan ubicar correctamente una nueva responsabilidad;
- investigadores que necesitan reconstruir lineage, supuestos y caveats antes de interpretar un resultado;
- maintainers y agentes que necesitan continuar el sistema sin memoria oral;
- colaboradores que necesitan incorporarse sin recorrer años de scripts/notebooks históricos.

Para mantenimiento autónomo, la entrada es el `AGENTS.md` del repositorio y `docs/maintenance/00_START_HERE.md`. El carry state registra qué revisions de productores fueron inspeccionadas en el último refresh.

La documentación debe facilitar onboarding y continuation sin convertirse en una segunda implementación de los sistemas que describe.
