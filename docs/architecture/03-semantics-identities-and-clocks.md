---
title: Semantics, identities, and clocks
sidebar_position: 4
status: current
owners: [poverty-ecosystem-engineering]
---

# Semantics, identities, and clocks

The hardest failures in this ecosystem are usually semantic compression errors: two distinct concepts are given one field, clock, identifier or status and are later treated as interchangeable. The Sep 2026 real-data integration sharpened several distinctions that must remain explicit.

## Identity is artifact-scoped

Every release declares entity level and identifier namespace.

Examples:

- EPH household: `CODUSU + NRO_HOGAR`;
- EPH person: `CODUSU + NRO_HOGAR + COMPONENTE`;
- Census sample household/person IDs from the exact sampler release;
- geography IDs tied to an exact Geography Release;
- household IDs preserved from Census sample through welfare inference into Poverty.

No scientific join may reconstruct identity from row order, approximate names or fuzzy matching.

The Sep 11 Census commissioning reconciled all 469,172 Census persons into 141,863 households without unmatched or duplicate scoring identities. That is technical/identity acceptance, not proof of transport validity.

## Geography identity != threshold area

A geography ID answers which governed territorial unit a row belongs to. A poverty-threshold area answers which CBA/CBT regime applies.

They can be linked by a governed binding, but they are different objects. Poverty-region vocabulary must not be smuggled into Census sample identity.

## EPH/Census semantic plane

The real alignment now has an exact materialized plane:

```text
eph-cpv2010-semantic-plane-2024q3-v1
```

The active P1-R subset contains 21 approved concepts. `H11` and `H16` are rejected. `P1-S` remains the narrow stable subset used for stricter temporal interpretations.

Semantic alignment answers whether a concept is comparable enough to enter the reviewed plane. It does **not** answer whether a donor-vintage Census value is a valid observation of a later welfare period.

### Semantic comparability != temporal validity

For example, a labor-status concept can be semantically harmonized while still being a 2010 donor-vintage state. Treating it as observed 2024 labor state would require a separate transport-time assumption or reconstruction mechanism.

The Sep 11 commissioning deliberately deferred temporal reconstruction rather than silently overwriting donor observations.

## Information plane != scientific information ceiling

The current transport science uses distinct feature frontiers:

```text
P0    baseline transport information
P1-R  reviewed Census-compatible plane
P2    richer EPH-observable research ceiling
```

A P2 variable may be scientifically valid and predictive inside EPH while being unavailable or unjustified on Census. Therefore:

```text
P2 improvement
!= permission to deploy P2 on Census
```

The final Sep 11 adjudication is that P1-R materially improves the transport frontier; richer P2 adds additional scientific information in the matched experiments; the deployable Census commissioning remains on frozen P1-R.

## Income target semantics

For the commissioned EPH 2024-Q3 transport study:

- valid supervision distinguishes true zero, positive income and unavailable response;
- `P47T == -9` and true missing are unavailable supervision, not zero income;
- person-level income is modeled before household aggregation;
- household evaluation requires complete observed member income when comparing against observed household totals;
- no EPH survey/design weights are used in fitting or the reported Sep 11 evaluation.

The main baseline failure is positive-income magnitude/distributional compression, not simply zero/positive classification. That is an experiment-specific scientific result, not a universal property of every future model.

## Survey weights != Census sampling != Poverty estimand

Keep these objects separate:

```text
EPH survey / expansion weight
        !=
Census selection_probability
        !=
optional donor-frame inverse-probability weight
        !=
Poverty analysis semantics
```

`samplerCensoARG` owns how donor households enter the target-year sample. `encuestador-de-hogares` owns how EPH survey design is or is not used in its transport study. `indice-pobreza-UBA` owns the final estimand/analysis semantics it accepts.

A historical field named `sample_weight` is never sufficient authority to infer any of these meanings.

## Target-year sampling semantics

The sampler separates:

```text
D[d]   exact donor-frame person mass in department d
T[d,y] exact target-year person mass for department d
c      global sampling intensity
```

with the target-year household inclusion design based on:

```text
p[d,y] = c * T[d,y] / D[d]
```

under its governed bound policy.

The selection unit is the household and every member is retained; the target mass is person mass. Target-year sampling changes the department mixture, not every within-department demographic or socioeconomic state.

Thus:

```text
frame_vintage = 2010
sampling_target_year = 2024
```

does not mean the selected records are observed 2024 Census records.

## Separate clocks

A real poverty research run may need all of:

```yaml
eph_training_period: 2024-Q3
census_frame_vintage: 2010
sampling_target_period: 2024
welfare_period: 2024-Q3
monetary_reference_period: <declared by welfare/basket parents>
poverty_line_period: 2024-Q3
geography_vintage: <exact geography release>
```

Never collapse these into one generic `year`.

A stable Census sample ID reused across welfare periods would describe repeated synthetic scoring snapshots, not observed longitudinal households.

## Point welfare != predictive welfare distribution

The current predictive household-welfare release represents approximately:

```text
Y_h = max(0, location_h + R)
```

where `location_h` is a point welfare prediction and `R` is an empirical residual distribution calibrated from household-safe EPH OOF evidence.

This distinction matters because the baseline conditional mean is compressed. In Sep 11 Q7 evidence, probabilistic low-tail prevalence substantially outperformed hard thresholding for both the deployable P1-R and richer P2 arms.

A probability distribution over household welfare therefore carries more threshold information than a single predicted amount.

But:

```text
predictive household welfare distribution
!= fully propagated uncertainty of aggregate poverty estimates
```

The current Poverty release path explicitly retains `uncertainty_status=not_supplied` unless a separate aggregate uncertainty method is provided.

## Poverty status != official statistics

Preserve at least these states:

```text
implemented contract
fixture-proven
real-data-proven
research-only / commissioned
scientific release readiness
public publication
official statistic
```

The Sep 11 end-to-end chain reaches real-data research commissioning. It does not reach official-statistic status.

## Support validity != value validity

A Census value can be valid according to source semantics while lying outside common EPH support. The real commissioning intentionally retained large valid `IX_TOT` values rather than clipping them to training support.

Current transport diagnostics report material shift, including roughly 28.8% of Census persons flagged weak-support under the predetermined rule and EPH-vs-Census domain-classifier AUC around 0.874.

These are limitations to interpret, not reasons to rewrite valid source observations.

## Private/collective dwelling universe remains visible

The commissioned P1 artifact lacks a governed private-versus-collective dwelling indicator. This creates a real universe ambiguity and must remain a limitation rather than being patched by guessing from household size or clipping extreme values.
