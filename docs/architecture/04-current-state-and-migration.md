---
title: Current state and migration
sidebar_position: 5
status: current
owners: [poverty-ecosystem-engineering]
---

# Current state and migration

This page separates what is implemented/proven from what remains research-only, proposed or blocked. It is intentionally conservative: a clean software path does not convert research commissioning into official statistics.

## State matrix — 2026-09-11

| Component | Current state | What is now solid | Remaining gap |
| --- | --- | --- | --- |
| `microdatos-EPH-INDEC` | **current / real-data-proven** | deterministic official-source EPH release custody; exact quarter artifacts; native household/person source fields | ordinary future source drift / uncommon transport formats |
| `income-modeling-eph` | **current / real-data-proven EPH-only boundary** | `research.eph-analysis-frame@1` proven on exact 2026-Q1 EPH parent; grouped household splits; governed `research.eph-income-study-cohort@1` builder | live study cohort waits for an approved monetary-conversion parent; not on Census scoring runtime |
| `eph-censo-aligner` | **current / real-data-proven semantic plane** | exact 2024-Q3 EPH + CPV-2010 alignment; 21-field P1-R plane; executable support/schema gates | capture final local `IX_TOT` collective-dwelling fix in canonical policy before regeneration; temporal reconstruction remains separate |
| `samplerCensoARG` | **current / real-data-proven target-year sample authority** | governed CPV donor frame; 2024/2025 target-year semantics; household selection + complete membership; explicit probability/weight fields; v2 contract consumed by aligner | continue recurring integrity/real-input proof; downstream estimand semantics remain outside sampler |
| `encuestador-de-hogares` | **current / research-commissioned** | real EPH hurdle experiments; household-safe folds; P1-R/P2 frontier; nested predictive distribution; full Census scoring; `research.household-welfare-predictive/v1`; weekly real-data research pulse | material transport shift; private/collective universe ambiguity; future temporal reconstruction; no official-statistic claim |
| `IPC-Argentina` | **current / candidate release authority** | curated official-panel v2 candidate; exact source locks; durable immutable candidates; explicit approval/maturity gate | candidate != approved conversion; latest thin coverage can remain candidate while strict approved-mode consumers fail closed |
| `canastasINDEC` | **current / bounded research integration input** | exact IPC candidate consumption; six-region v2 basket candidate; quarter-parameterized poverty input slice including 2024-Q3 | broader scientific promotion/official threshold status remains distinct from the bounded research seam |
| `indice-pobreza-UBA` | **current / predictive seam merged; real province producer pending PR** | deterministic v2 contract + predictive-welfare FGT0/1/2 integration merged on main | PR #27 adds validated 24-province + ARG detached real producer; still open; aggregate uncertainty not supplied |
| `argentina-geography` | **approved / active** | exact geography authority; IGN 24-province release; stable zero-preserving province IDs; factual relations | threshold-area binding only as required by specific consumer evidence |
| `argentina-poverty-atlas` | **current main synthetic; real ingest validated pending PR** | production-quality fixture-first UX; exact geography parent; noindex synthetic demo | PR #23 real detached Poverty v2 ingest is validated but open; public main must not yet be described as real-data Atlas |
| `atlas-pobreza-docs` | **current / architecture authority** | architecture-first docs plus autonomous maintenance bundle | keep carry state synchronized with active producer transitions |

## The Sep 10–11 transition

The major change is that the central poverty path no longer stops at proposed interfaces. A real research chain now exists:

```text
EPH 2024-Q3
  -> real EPH/Census semantic plane
  -> exact CPV-2010 target-year sample
  -> household-safe EPH transport experiments
  -> full Census scoring
  -> predictive household welfare
  -> quarter-specific poverty input
  -> predictive FGT measurement
```

The source/sample/semantic/transport parts have crossed real data. The last province-release and public-Atlas integration edges remain validated on open PRs rather than canonical main.

## Completed proofs that should no longer appear as active TODOs

### Exact target-year Census sampling

The earlier architecture work around donor mass, target mass, household selection, complete membership, frame-vintage separation and explicit selection/weight semantics is now implemented in `samplerCensoARG` and consumed by the real alignment. Treat further sampler work as maintenance/scientific refinement, not as a blocker to beginning transport science.

### Real EPH/Census semantic plane

The former “approve one real vintage pair” blocker is closed enough for the Sep 11 study. `eph-censo-aligner` materialized `eph-cpv2010-semantic-plane-2024q3-v1` over 47,564 EPH persons and 469,172 Census persons. P1-R has 21 approved fields; `H11` and `H16` are rejected.

One implementation-drift caveat remains: the successful local materialization accepts valid large `IX_TOT` collective-dwelling values while the earlier committed policy still contained a narrower maximum. This is a reproducibility repair, not a reason to reopen the semantic review.

### Household-safe transport experimentation

`encuestador-de-hogares` now has the science infrastructure that used to be target architecture:

- grouped household folds;
- direct HGB/hurdle baselines;
- nested cross-fitting for learned intermediate features;
- strong person/household diagnostics;
- oracle/error-reservoir diagnostics;
- exact real EPH execution and run evidence.

The first lean cascade did **not** outperform the direct baseline materially; this prevented the architecture from ossifying around historical RFC stage count.

### Information frontier

The Sep 11 bounded experiments establish:

- baseline P0 is strongly compressed in positive-income magnitude;
- P1-R materially improves information/rank/dispersion under the paired design;
- grouped ablation ranks education as the clearest information family, followed by labor/housing and then demographics; composition is largely redundant in that experiment;
- true labor state contains welfare signal, but the tested reconstruction captures essentially none of the oracle gain;
- a richer EPH-only P2 frontier contains additional information, but P2 is an information ceiling, not a Census deployment plane;
- positive-income amount remains the dominant error reservoir.

These are exact-study findings, not permanent claims about future model classes.

### Predictive distribution evidence

The nested empirical-residual distribution experiment is complete. Relative to hard thresholding of point welfare, mean absolute prevalence error fell materially for both scientific P2 and deployable P1-R arms; all five outer folds favored the probabilistic approach in both arms.

This supports a predictive-welfare handoff for threshold estimation. It does **not** supply aggregate poverty confidence intervals.

### Census research commissioning

All 469,172 persons / 141,863 households in the exact Census P1 scoring frame were scored without identity failure using the frozen P1-R deployment plane plus EPH-only residual calibration.

Commissioning status is explicitly:

```text
COMPLETE_WITH_MATERIAL_TRANSPORT_CAVEATS
```

not “validated population truth”. Material caveats include approximately 28.8% weak-support Census persons, domain-classifier AUC around 0.874, and no governed private-versus-collective dwelling indicator in the P1 artifact.

### Predictive welfare artifact

`encuestador-de-hogares` main now implements:

```text
research.household-welfare-predictive/v1
```

The artifact carries household locations, a governed empirical residual distribution, floor-at-zero support policy, monetary scale, lineage and transport limitations. Poverty can integrate thresholds without understanding model internals.

### Predictive Poverty seam

`indice-pobreza-UBA` main now integrates the predictive household welfare distribution against poverty/indigence lines and computes FGT0/1/2 while preserving the deterministic path.

This closes the conceptual handoff between predictive welfare and Poverty. It does not yet make the real province/national producer canonical mainline code.

## Current frontier

The main scientific bottleneck is no longer “build the pipeline.” The current frontier is narrower:

1. **transport validity and support** — quantify/understand weak-support populations and source-universe differences rather than clipping them away;
2. **temporal transport** — decide whether/how donor-vintage states such as labor status are reconstructed for a target welfare period;
3. **distribution/aggregate uncertainty** — the household predictive distribution is useful for threshold prevalence, but aggregate Poverty uncertainty remains `not_supplied`;
4. **cross-period replication** — repeat the bounded study on another target period/year rather than overfitting conclusions to 2024-Q3;
5. **release integration** — merge a governed province/national real Poverty producer, then merge the Atlas real-release adapter without browser-side science.

## Edges that remain pending integration

### Poverty province/national producer

`indice-pobreza-UBA#27` is open. Its branch has passed real local acceptance against the accepted predictive welfare release and Census frame and writes a detached v2 release with 300 facts across 24 provinces + `ARG`. Because it has not merged, document it as **validated pending integration**, not current main capability.

### Atlas real-release ingest

`argentina-poverty-atlas#23` is open and mergeable. Its branch strictly verifies detached v2 release status/checksums/schema/24 province IDs and keeps synthetic fixture mode as fallback. Canonical Atlas `main` remains synthetic/noindex until this edge merges and an active real release is intentionally selected.

## Important non-blockers

Do not reopen these as generic infrastructure programs merely because future refinement exists:

- target-year sampler architecture;
- basic EPH/Census semantic alignment machinery;
- household-safe folds;
- direct-vs-cascade experiment infrastructure;
- predictive-distribution primitive;
- detached Poverty v2 consumer contract.

New work should be tied to a named scientific question, exact source drift, or concrete consumer need.

## Open scientific decisions

Still visible by design:

- acceptable transport evidence under temporal/structural shift;
- whether and how target-period latent state is reconstructed from donor-vintage Census observables;
- private/collective dwelling universe treatment;
- whether a different conditional amount formulation materially reduces compression beyond the current information frontier;
- how predictive welfare uncertainty should propagate to aggregate poverty uncertainty;
- which additional period/year is the next replication target;
- what exact release/promotion gates are required before public research publication.

## Status discipline

A merged contract is implemented. A real-data run is real-data-proven. A commissioned research chain is still research. An open PR remains pending integration. None of these states implies official poverty statistics.
