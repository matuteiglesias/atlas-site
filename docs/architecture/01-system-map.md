---
title: System map
sidebar_position: 2
status: current
owners: [poverty-ecosystem-engineering]
---

# System map

The poverty ecosystem is a chain of source/reference authorities, scientific transformation instruments and public consumers. Repository separation follows scientific authority: a producer may be sophisticated internally, while cross-repository handoffs should remain explicit artifacts with exact identities and limitations.

## Current architecture — September 2026

```text
                         SOURCE / REFERENCE AUTHORITIES

 microdatos-EPH-INDEC          CPV-2010 + target mass        IPC-Argentina
 official EPH quarters                 |                   monetary references
        |                               |                         |
        |                               v                         v
        |                       samplerCensoARG              canastasINDEC
        |                     frame + target-year            poverty-line /
        |                     household sample               basket inputs
        |                               |                         |
        +------------+                  |                         |
                     |                  |                         |
                     v                  v                         |
               eph-censo-aligner                                  |
             real semantic feature plane                          |
                     |                                            |
                     +--------------------+                       |
                                          |                       |
                                          v                       |
                                encuestador-de-hogares <----------+
                              household-safe EPH transport
                              Census research commissioning
                              predictive household welfare
                                          |
                                          v
                                   indice-pobreza-UBA
                              method + threshold binding + FGT
                                          |
                                          v
                               poverty-estimate-release/v2
                                          |
                                 +--------+--------+
                                 |                 |
                                 v                 v
                       argentina-poverty-atlas    engineering docs
                         public consumer          this repository
```

`income-modeling-eph` remains a first-class **parallel EPH-only research instrument**. It now has a real-data-proven neutral `research.eph-analysis-frame@1` boundary and a governed income-study cohort builder, but it is not the active Census-scoring runtime. Its feature contract is also useful as bounded scientific evidence when `encuestador-de-hogares` defines an EPH-only information ceiling.

`argentina-geography` remains the geography authority and supplies exact geography identities/releases independently from poverty values and threshold semantics.

## Repository roles

| Repository | Current engineering role | Owns | Explicitly does not own |
| --- | --- | --- | --- |
| `microdatos-EPH-INDEC` | EPH acquisition authority | official-source acquisition, normalized quarter release, custody/provenance | analytical targets, cross-source semantics, models |
| `income-modeling-eph` | EPH-only research + neutral analysis-frame producer | source-backed EPH analysis frame, study cohorts, EPH model experiments/evidence | Census scoring, poverty estimation |
| `eph-censo-aligner` | EPH↔Census semantic authority | reviewed concept mappings, recodes, support/schema gates, aligned real feature plane | model validity, Census sampling, poverty estimands |
| `samplerCensoARG` | Census frame/sample authority | exact donor-frame custody, target-year household selection, complete membership, selection probability/design QA | welfare inference, final poverty weights/estimands |
| `encuestador-de-hogares` | survey-to-Census welfare inference | household-safe fitting/evaluation, information-plane experiments, Census scoring, transport diagnostics, predictive welfare handoff | source acquisition, semantic authority, poverty thresholds/FGT |
| `IPC-Argentina` | analytical price / monetary-reference authority | versioned conversion candidates, source locks, maturity/coverage metadata | official IPC authority, poverty basket geography |
| `canastasINDEC` | poverty-threshold input producer | source-backed CBA/CBT candidate path, quarter-specific bounded integration slices | Census geography, poverty estimation |
| `indice-pobreza-UBA` | poverty measurement/estimation authority | adult-equivalence/method semantics, poverty/indigence thresholds, deterministic + predictive FGT, poverty release contract | model fitting, Census acquisition, browser aggregation |
| `argentina-geography` | geography authority | exact sources, native IDs, Geography/Relation releases and governed territorial bindings | poverty estimands, sampling, welfare inference |
| `argentina-poverty-atlas` | public release consumer | product UX, geography/fact joining, lineage/methodology presentation | poverty recomputation, model inference, geometry authority |
| `atlas-pobreza-docs` | ecosystem engineering documentation authority | cross-repo architecture, state/migration map, contract interpretation and maintenance guidance | producer implementation or scientific values |

## Two EPH questions remain distinct

1. **EPH-only science:** what can be learned about income/welfare inside EPH using scientifically valid EPH observables?
2. **EPH→Census transport:** what can be inferred for an exact Census-derived scoring frame using only an approved, transportable information plane plus explicitly modeled uncertainty?

The first is primarily `income-modeling-eph`. The second is `encuestador-de-hogares`. A better EPH-only feature set is an information ceiling, not automatic permission to use those fields on Census.

## Real-data chain now proven at research level

The Sep 10–11 integration crossed several formerly-proposed boundaries with exact real parents:

- EPH 2024-Q3: `eph-2024-q3-3b6a7a15c4af`;
- Census target-year sample: `census-sample-2024-0839713eafea8d1b`;
- real semantic plane: `eph-cpv2010-semantic-plane-2024q3-v1`;
- Census commissioning: 469,172 persons / 141,863 households scored;
- predictive welfare: `research.household-welfare-predictive/v1` implemented on `encuestador-de-hogares` main;
- predictive poverty seam: merged on `indice-pobreza-UBA` main.

This is **research commissioning**, not official statistics. Transport caveats remain material and aggregate estimate uncertainty is not supplied by the current path.

## Current downstream edge

`indice-pobreza-UBA` PR #27 contains a validated detached 24-province + ARG predictive release producer, but it remains open. `argentina-poverty-atlas` PR #23 contains validated detached real-release ingest, but it also remains open. Therefore canonical `main` still stops before a merged real province release → public Atlas edge.

## Weight boundary

Keep these objects separate throughout the chain:

```text
EPH survey / expansion weight
!= Census selection_probability
!= optional donor-frame inverse-probability weight
!= Poverty analysis semantics
```

The Sep 11 `encuestador` experiments use no survey/design weights. The target-year sampler preserves design metadata without granting downstream permission to reinterpret `1/p` as the poverty estimand.
