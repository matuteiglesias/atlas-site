---
title: Engineering backlog
sidebar_position: 7
status: active
owners: [poverty-ecosystem-engineering]
---

# Engineering backlog

This page tracks **cross-repository architecture work that remains active after the Sep 11 real-data integration**. It is not a generic roadmap and does not replace producer-local issues/PRs.

The authoritative execution detail lives in producer repositories. This page records only integration-level consequences and gates.

## Active boundary work

### 1. Capture final real semantic-plane reproducibility on canonical aligner code

**Repository:** `matuteiglesias/eph-censo-aligner`

The real 2024-Q3 / CPV-2010 plane is scientifically commissioned and its final local support report passes. However, the producer evidence records one reproducibility drift: the successful materialization explicitly accepted valid large collective-dwelling `IX_TOT` values, while the earlier committed review policy retained an `IX_TOT max=40` constraint.

Target:

- capture the successful local implementation fix on canonical producer code;
- preserve the existing semantic decision;
- prove that regeneration of `eph-cpv2010-semantic-plane-2024q3-v1` no longer depends on uncommitted local behavior.

This is implementation/reproducibility repair, **not** a request to clip `IX_TOT` or reopen the semantic review.

### 2. Transport support and universe diagnostics

**Repository:** `matuteiglesias/encuestador-de-hogares`

The first full Census commissioning completed, but interpretation is materially limited by source-domain shift:

- about 28.8% of Census persons satisfy the predetermined weak-support rule;
- EPH-vs-Census domain classifier AUC is about 0.874;
- the current P1 artifact does not expose a governed private-versus-collective dwelling indicator.

Target: turn these limitations into bounded scientific diagnostics/sensitivity work. Do not “repair” them by clipping valid source values, dropping difficult rows without a declared estimand change, or inventing a dwelling-universe indicator.

### 3. Temporal transport / target-period latent state

**Repository:** `matuteiglesias/encuestador-de-hogares`  
**Semantic parent:** `matuteiglesias/eph-censo-aligner`

The commissioned P1-R plane explicitly defers temporal reconstruction. Some concepts—especially labor state—may be semantically comparable across EPH/Census while donor-vintage Census values are not observations of the 2024 welfare period.

Target: define and test a separate target-period-state mechanism only if the scientific gain justifies it. Preserve donor observations unchanged and version any inferred state/calibration separately.

The Sep 11 oracle evidence says true labor state contains downstream welfare signal, while the tested reconstruction captures essentially none of the oracle gain. That makes this a scientific question, not a license to hard-code quarter-level labor marginals.

### 4. Amount-model frontier after information frontier

**Repository:** `matuteiglesias/encuestador-de-hogares`

The current dominant baseline failure is positive-income magnitude/distributional compression. P1-R and P2 established that information matters, but amount error remains the largest reservoir.

Target: any next model-family/formulation experiment should be justified specifically against this failure and reuse the existing household-safe evidence framework. Avoid broad model sweeps without a predeclared hypothesis.

### 5. Aggregate uncertainty semantics

**Repositories:** `matuteiglesias/encuestador-de-hogares`, `matuteiglesias/indice-pobreza-UBA`

The current predictive welfare artifact provides a marginal household distribution that materially improves threshold prevalence calibration. It does not provide a defensible sampling/model/transport uncertainty distribution for province/national FGT estimates.

Target: decide whether and how uncertainty should propagate to aggregate Poverty releases. Until then preserve:

```text
uncertainty_status = not_supplied
```

Do not convert residual draws into confidence intervals by convention.

### 6. Merge/normalize the real province Poverty producer

**Repository:** `matuteiglesias/indice-pobreza-UBA`  
**PR:** `#27 — feat: expose predictive province release producer`

The branch has real local acceptance against the accepted predictive welfare release and Census frame: detached verification passes with 300 facts, 24 provinces + `ARG`, persons/households, poverty/indigence and FGT0/1/2.

Target: review/merge or supersede the PR without weakening:

- `research_estimate` status;
- `uncertainty_status=not_supplied`;
- exact ID/checksum/parent contracts;
- no model training or Census acquisition inside Poverty.

Until merged, this is validated pending integration.

### 7. Merge/normalize Atlas real-release ingest

**Repository:** `matuteiglesias/argentina-poverty-atlas`  
**PR:** `#23 — feat: ingest detached real poverty releases`

The branch is battle-tested against the accepted detached province release and rejects checksum corruption, missing/leading-zero province IDs, unsupported status, duplicate facts, fake uncertainty and geography mismatch.

Target: merge after the producer release boundary is canonical, then intentionally select a real release and remove the synthetic-search gate only when public research publication is actually intended.

Do not make the browser a fallback scientific aggregator.

### 8. Cross-period replication

**Repositories:** sampler / aligner / encuestador / canastas / Poverty

The first commissioned chain is centered on 2024-Q3 welfare evidence and a 2024 target-year Census sample. The next confidence gain should come from replication, not more infrastructure.

Target: choose one additional bounded period/year—2025 is already supported by sampler design—and repeat the exact evidence chain far enough to test whether the main conclusions are stable:

- amount compression;
- P1-R information gain;
- family ordering;
- predictive-distribution prevalence improvement;
- transport-support caveats.

Avoid expanding to many periods until the second run proves the machinery and conclusions are not one-period accidents.

### 9. Monetary approval boundary where required

**Repository:** `matuteiglesias/IPC-Argentina`

IPC v2 immutable candidate publication is live and candidate maturity/coverage is explicit. Thin latest-period coverage may remain a valid candidate while a strict approved-mode gate stays red.

Target: consumers that require an **approved** conversion (for example the live `income-modeling-eph` study cohort) must continue to fail closed until the producer provides that status. Do not weaken producer approval semantics merely to make downstream execution green.

## Recently completed architecture work

The following items should no longer be carried as primary blockers:

- **neutral EPH analysis frame** — implemented and real-data-proven in `income-modeling-eph`;
- **target-year Census household sampling contract** — implemented, v2 consumed by real alignment;
- **real EPH/CPV semantic plane** — materialized and used for the Sep 11 science;
- **household-safe OOF transport runtime** — implemented;
- **strong welfare/oracle diagnostics** — implemented;
- **P0/P1-R/P2 information-frontier experiments** — completed for the Sep 11 study;
- **nested predictive distribution experiment** — completed with clear threshold-prevalence improvement;
- **full Census P1 commissioning** — completed as research with material caveats;
- **predictive household welfare release builder** — merged on `encuestador` main;
- **quarter-parameterized 2024-Q3 basket input slice** — merged on `canastasINDEC` main;
- **predictive FGT seam** — merged on Poverty main.

Producer-local issues may remain open for follow-up, but the cross-repo architecture must not keep describing these capabilities as absent.

## Stable boundaries — avoid churn

### EPH acquisition

`microdatos-EPH-INDEC` remains a clean acquisition/custody producer. Do not expand it into modeling or semantic alignment.

### EPH-only research

`income-modeling-eph` remains useful and current, but outside the active Census scoring runtime. It owns neutral EPH analysis frames and EPH-only scientific studies, not transport deployment.

### Census sampling

`samplerCensoARG` owns sample identity and selection design. It does not define final Poverty analysis weights or contemporary socioeconomic state.

### Poverty v2

`indice-pobreza-UBA` should remain a thin scientific measurement/estimation authority. Do not absorb model fitting, sampler logic, geography processing or browser concerns into it.

### Public Atlas

The Atlas consumes detached releases and geometry. It may select/present capabilities but must not synthesize missing facts or uncertainty.

## Promotion discipline

Create/promote producer work from this backlog only when:

- the current producer state has been inspected;
- a concrete missing capability or scientific question can be named;
- acceptance evidence can be specified;
- the change reduces ambiguity or unlocks a real consumer;
- target architecture is not presented as implemented fact.

The objective is not to maximize repository activity. It is to keep the scientific chain easy to verify, continue and change without spreading stale assumptions downstream.
