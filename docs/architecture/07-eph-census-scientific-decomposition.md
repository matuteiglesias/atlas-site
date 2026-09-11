---
title: EPH, Census sampling and transport science
sidebar_position: 8
status: current
owners: [poverty-ecosystem-engineering]
---

# EPH, Census sampling and transport science

The current architecture separates four scientific responsibilities that historically lived closer together:

1. exact EPH source custody;
2. EPH-only scientific analysis;
3. EPH↔Census semantic alignment and Census sample identity;
4. statistical transport from EPH evidence to an exact Census scoring population.

That separation is now exercised on real data rather than only described as target architecture.

## Current decomposition

```text
microdatos-EPH-INDEC
 exact EPH quarter release
        |
        +------------------------------+
        |                              |
        v                              v
income-modeling-eph             eph-censo-aligner
EPH-only neutral frame          semantic comparability
+ EPH studies                         ^
                                      |
                         samplerCensoARG
                   exact Census frame/sample
                                      |
                                      v
                          aligned transport plane
                                      |
                                      v
                         encuestador-de-hogares
                    household-safe transport science
                                      |
                                      v
                     predictive household welfare
                                      |
                                      v
                              Poverty v2
```

Cross-repository arrows are artifact contracts, not sibling imports.

## `income-modeling-eph`: current role

This repository remains a first-class EPH-only research instrument.

It now implements and has real-data proof for:

```text
research.eph-analysis-frame@1
```

The neutral frame preserves native EPH household/person identity and available survey-design fields while excluding model targets, Census-shaped aliases, target-derived ranks and hidden monetary rebasing.

It also implements:

```text
research.eph-income-study-cohort@1
```

for its own positive-income EPH study. That live study path is currently gated by an **approved** monetary-conversion release.

This repository is **not** on the active Census-scoring runtime. Its feature contract can nevertheless be used as evidence to define a bounded EPH-only information ceiling for transport experiments, provided the transport study keeps its own cohort, target and folds.

## `samplerCensoARG`: who is scored

The sampler owns the Census population/sample boundary.

The current real path uses:

```text
research.census-frame@1
research.census-target-year-sample/v2
```

The target-year sample keeps:

- CPV-2010 donor identity;
- household selection as the primary unit;
- every person in selected households;
- target-year person mass by department;
- explicit selection probability and separate design-weight semantics;
- no implicit poverty-region identity or model-fitting weight.

For Sep 11 science, the exact 2024 sample is:

```text
census-sample-2024-0839713eafea8d1b
```

It is a synthetic target-year-composition sample of Census-2010 donor units, not contemporaneous Census microdata.

## `eph-censo-aligner`: what is semantically comparable

The first real feature plane is:

```text
eph-cpv2010-semantic-plane-2024q3-v1
```

It materialized over:

- 47,564 EPH persons;
- 469,172 Census persons.

The reviewed P1-R plane contains 21 approved concepts. `H11` and `H16` are rejected. Large valid `IX_TOT` values are not clipped to EPH training support.

The aligner answers source semantics, recodes, support/schema gates and semantic comparability. It does not answer whether a 2010 donor state is valid for 2024 welfare interpretation.

## `encuestador-de-hogares`: transport science

The transport study owns:

- EPH training population and target semantics;
- household-aware fold policy;
- direct vs staged/hurdle architecture comparisons;
- deployable/shared vs EPH-only information-plane experiments;
- support/domain-shift diagnostics;
- exact Census scoring;
- person→household welfare construction;
- predictive welfare distribution used downstream.

The Sep 11 study uses no EPH survey/design weights in fitting or reported evaluation. This is an explicit study decision, not a generic preprocessing default.

## Income supervision and household integrity

For exact EPH 2024-Q3:

- 47,564 persons total;
- 41,821 terminal-eligible zero-or-positive income observations;
- 25,209 positive;
- 16,612 true zero;
- 5,688 `P47T == -9` unavailable responses;
- 55 true missing;
- 16,650 households;
- 12,568 complete observed-income households used for strict household evaluation.

`-9` and missing are unavailable supervision, not zero.

Folds are grouped by household so members cannot leak across train/test partitions. Learned intermediate features used by downstream models are generated out-of-fold under the outer household split.

## The information frontiers

The current science distinguishes three frontiers:

### P0 — baseline

A small baseline of native/shared features. It establishes the initial failure mode and is not presented as the best possible model.

### P1-R — reviewed deployable plane

The 21-field real EPH/Census semantic plane. This is the current Census-deployable information surface used for Q8 commissioning.

### P2 — EPH-only scientific ceiling

A bounded richer set of valid EPH observables drawn from the EPH-only feature contract. It excludes income targets/components, identifiers, survey weights, target-derived fields and geography ranks.

P2 asks whether missing predictive information exists inside EPH. It does not authorize scoring those fields on Census.

## What the Sep 11 experiment established

### Q1 — dominant baseline failure

The direct hurdle-Gamma baseline is much stronger on zero/positive presence than on positive-income amount.

Selected baseline evidence:

- presence balanced accuracy about 0.872;
- conditional-positive R² about 0.232;
- positive-amount dispersion ratio about 0.465;
- household R² about 0.288;
- household dispersion about 0.543;
- household Spearman about 0.618.

The bottom tail is overpredicted and top tail materially underpredicted. The first lean/staged cascade is essentially tied/slightly worse than the direct baseline, so historical cascade complexity is not architecture law.

### Q2 — real Census-compatible information matters

The paired P0 vs P1-R experiment is complete under the frozen household-fold design. P1-R materially improves information/ranking and amount dispersion, with the favorable direction stable across folds.

The exact producer result files remain the authority for metrics. The architecture-level consequence is that the semantic alignment work is scientifically useful rather than merely operational plumbing.

### Q3 — information-family ordering

The bounded P1-R family ablation closes with the qualitative ordering:

```text
education > labor/housing > demographics
composition approximately redundant in this experiment
```

This is a result of this exact design, not a permanent global feature-ranking claim.

### Q4 — labor signal vs reconstruction

True labor state contains downstream welfare signal. However, the tested deployable reconstruction captures essentially none of the oracle gain.

That says the issue is not simply whether labor matters; it is whether target-period labor structure can be reconstructed from the information actually available at deployment.

### Q5 — P2 information ceiling

The matched richer EPH-only frontier adds scientific signal beyond the baseline and supports the conclusion that some useful information is not currently transportable through the Census plane.

P2 remains EPH-only research evidence. Census commissioning does not use the P2-only fields.

### Q6 — amount remains the main error reservoir

Oracle/error-reservoir diagnostics show that replacing only presence errors gives modest gains relative to the much larger ceiling obtained when positive amount is treated as known for diagnostic purposes.

The upper-bound diagnostic is non-deployable and non-causal; its purpose is localization of error, not a performance claim.

## Predictive distributions — Q7

The mean frontier remains compressed, so the study tested whether a leakage-safe empirical residual distribution can recover low-tail prevalence better than thresholding point predictions.

For both the P2 scientific ceiling and deployable P1-R arm, the probabilistic method reduced mean absolute prevalence error materially relative to hard thresholding, and all five outer folds moved in the favorable direction.

The evidence index classifies both arms as `CLEAR SUCCESS`.

This is why the downstream handoff is now a predictive household-welfare distribution rather than only a point amount.

## Census commissioning — Q8

The selected deployment plane is P1-R, not P2.

The full exact Census scoring frame was commissioned:

```text
469,172 persons
141,863 households
```

with no technical identity failure.

But the commissioning result is explicitly:

```text
COMPLETE_WITH_MATERIAL_TRANSPORT_CAVEATS
```

not scientific equivalence between EPH and Census.

Material caveats include:

- weak-support person fraction about 0.288;
- EPH/Census domain-classifier AUC about 0.874;
- no governed private-vs-collective dwelling indicator in the P1 artifact;
- temporal reconstruction deferred.

## Predictive household-welfare boundary

`encuestador-de-hogares` main now implements:

```text
research.household-welfare-predictive/v1
```

with representation approximately:

```text
Y_h = max(0, location_h + R)
```

where `R` comes from governed EPH OOF residual evidence.

The artifact is intentionally downstream-friendly: Poverty needs welfare semantics, distribution representation, exact parents, monetary scale and limitations—not classifier internals.

## What not to infer

The current evidence does **not** establish that:

- CPV-2010 donor states are observed 2024 states;
- P2 fields are available on Census;
- weak-support Census units should be clipped or dropped;
- survey weights should be introduced merely because EPH contains them;
- the predictive residual distribution is complete aggregate uncertainty;
- a successful Census scoring run is an official poverty estimate.

## Next scientific layer

The highest-value remaining questions are now:

1. support/domain-shift sensitivity and the private/collective universe;
2. target-period latent-state reconstruction where justified;
3. conditional-amount formulations after the information frontier is fixed;
4. aggregate uncertainty semantics;
5. cross-period replication.

Generic transport infrastructure is no longer the primary blocker.
