---
title: EncUESTADOR — transport functional contract
sidebar_position: 9
status: current
owners: [poverty-ecosystem-engineering]
---

# `encuestador-de-hogares`: transport functional contract

`encuestador-de-hogares` is the scientific transport instrument that relates exact EPH evidence to an exact Census-derived scoring population.

Its modern function is:

> Infer a declared target-period household welfare distribution for the exact households in one governed Census-derived sample, using target-period EPH evidence and an approved EPH/Census semantic information plane, while preserving household identity, temporal assumptions, transport diagnostics and lineage.

It owns statistical transport. It does not own source acquisition, Census sampling, cross-source semantic authority, poverty thresholds or FGT estimation.

## Current input boundary

The Sep 11 real-data study binds four concrete evidence families:

```text
1. exact EPH quarter evidence
2. approved real EPH/Census semantic feature plane
3. exact Census frame + target-year household sample
4. declared transport study / monetary semantics
```

Exact commissioned parents include:

```text
EPH:      eph-2024-q3-3b6a7a15c4af
Census:   census-sample-2024-0839713eafea8d1b
Plane:    eph-cpv2010-semantic-plane-2024q3-v1
```

The EPH source authority is `microdatos-EPH-INDEC`. `income-modeling-eph` is an EPH-only scientific sibling whose neutral-frame and feature contracts may inform bounded experiments, but its flagship/cohort is not the Census transport runtime.

## Training population and target semantics

The current EPH 2024-Q3 transport study distinguishes:

```text
positive income
true zero income
unavailable response (-9 / missing)
```

Unavailable supervision is not coerced to zero.

The terminal amount model is person-level; household welfare is constructed by aggregating exact household members. Strict observed household evaluation uses households with complete observed member income.

The study uses no EPH survey/design weights in fitting or reported Sep 11 evaluation. Future studies may choose differently only through an explicit study contract.

## Household-safe validation

All approved evaluation must preserve household grouping:

- members of one household cannot cross outer folds;
- learned intermediate representations consumed by a terminal model must be generated out-of-fold relative to the terminal holdout;
- full-fit models may be trained after selection for Census scoring, but evaluation evidence remains OOF/household-safe.

This is stronger than reusing one global OOF latent matrix when downstream folds can otherwise inherit information from held-out households.

## Architecture families

The transport study may compare:

```text
direct shared-information model
hurdle / two-part model
staged latent dependency graph
```

The direct model is a mandatory baseline. Historical RFC1→RFC4 stage count is evidence, not a required architecture.

The first modern lean labor-state cascade did not beat the direct Gamma baseline. Therefore extra stages must earn their place through final-welfare evidence rather than historical continuity.

## Information-plane discipline

The study currently distinguishes:

```text
P0    baseline shared/native information
P1-R  reviewed Census-compatible deployment plane
P2    richer EPH-only scientific information ceiling
```

P2 may improve EPH prediction without being deployable on Census.

The Census commissioning uses P1-R. This is a hard boundary:

```text
better EPH-only evidence != permission to fabricate Census features
```

## Current scientific diagnosis

The Sep 11 evidence closes the first information-frontier cycle:

- zero/positive presence is comparatively strong;
- positive-income amount is severely compressed;
- household aggregation inherits that compression;
- P1-R materially improves the information/ranking frontier;
- education is the strongest family in the bounded P1-R ablation, with labor/housing also useful;
- true labor state has welfare signal, but the tested reconstruction captures essentially none of the oracle gain;
- richer P2 adds scientific information but remains EPH-only;
- positive amount remains the dominant error reservoir.

These are findings for the exact 2024-Q3 design, not permanent model truths.

## Semantic equivalence != temporal equivalence

A Census concept can be semantically aligned while its donor-vintage value is stale for a later welfare period.

The commissioned P1-R run explicitly defers temporal reconstruction. Any later target-period-state mechanism must:

- preserve the original donor observation;
- create a separate inferred/latent state;
- identify exact target-period anchors if used;
- be evaluated as transport science;
- never rewrite Census source meaning in place.

## Census commissioning

Q8 scored the complete exact Census P1 matrix:

```text
469,172 persons
141,863 households
```

All identities reconciled technically.

The result is nevertheless classified:

```text
COMPLETE_WITH_MATERIAL_TRANSPORT_CAVEATS
```

because support/domain shift is substantial:

- weak-support Census-person fraction ≈ 0.288;
- EPH-vs-Census domain-classifier AUC ≈ 0.874;
- private/collective dwelling status is not available as a governed P1 indicator;
- temporal reconstruction is deferred.

The contract requires these limitations to travel with the downstream welfare artifact.

## Output boundary: point vs predictive welfare

A point welfare prediction remains useful internally, but the current downstream product is a distributional artifact:

```text
research.household-welfare-predictive/v1
```

The implemented representation is approximately:

```text
Y_h = max(0, location_h + R)
```

where:

- `location_h` is the household point prediction for the commissioned Census household;
- `R` is a governed empirical residual distribution calibrated from leakage-safe EPH OOF household evidence;
- support is floored at zero;
- the release records exact parents, scale and transport limitations.

## Why predictive welfare is now canonical for the research seam

The current point predictor is distributionally compressed. Q7 therefore tested low-tail prevalence under a nested household-safe empirical residual distribution.

For both the richer P2 scientific ceiling and deployable P1-R arm, probabilistic threshold estimation materially reduced prevalence error relative to hard thresholding, with favorable direction in all five outer folds.

This supports handing Poverty a welfare distribution rather than asking Poverty to invent uncertainty around a point model.

## What the predictive release does not own

It does not define:

- poverty or indigence lines;
- adult equivalence;
- FGT0/1/2;
- threshold-area geography;
- aggregate confidence intervals;
- official-statistic status.

In particular:

```text
household predictive distribution
!= aggregate poverty estimate uncertainty
```

The downstream current release path must keep `uncertainty_status=not_supplied` unless a separate method supplies it.

## Weight boundary

These remain distinct:

```text
EPH survey / expansion weight
!= Census selection probability
!= optional donor-frame inverse-probability quantity
!= Poverty analysis weight/estimand
```

`encuestador` preserves sampler lineage but does not reinterpret Census selection probability as model weight or final Poverty weight.

## Current operational maturity

The repository is no longer “runtime pending.” Main now contains:

- real EPH execution;
- direct/lean/hurdle science infrastructure;
- strong welfare diagnostics;
- information-frontier experiments;
- nested predictive-distribution evidence;
- full Census research commissioning;
- predictive welfare release builder;
- a weekly real-data research/reproducibility pulse.

The remaining frontier is scientific validity/replication, not first executable transport infrastructure.

## Boundary summary

```text
microdatos-EPH-INDEC
    what exact EPH evidence exists?

income-modeling-eph
    what EPH-only analysis/study evidence exists?

eph-censo-aligner
    what EPH/Census concepts are semantically comparable?

samplerCensoARG
    which exact Census donor households/persons are scored?

encuestador-de-hogares
    what target-period household welfare distribution can be inferred for those units?

indice-pobreza-UBA
    given welfare distribution + frame + lines + method, what poverty estimand follows?
```
