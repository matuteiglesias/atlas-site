---
title: Contracts and release chain
sidebar_position: 3
status: current
owners: [poverty-ecosystem-engineering]
---

# Contracts and release chain

The ecosystem integrates through versioned artifacts, not sibling runtime imports or shared working directories. Producers own semantic transformation into an exported boundary; consumers verify identity, schema, lineage, QA and limitations before use.

## Contract envelope

A scientific handoff should expose the equivalent of:

```text
release/
├── manifest.json
├── data.parquet | data.csv
├── qa.json
├── limitations / warnings
└── checksums
```

A consumer must not infer artifact type, entity identity, period, monetary reference or status from filenames or directory location.

## Current research chain

```text
publicdata.eph-microdata@1
        |
        +-------------------------> research.eph-analysis-frame@1
        |                                  (`income-modeling-eph`)
        |
        v
research.eph-census-semantic-feature-plane@1
        (`eph-censo-aligner`)
        ^
        |
research.census-frame@1
research.census-target-year-sample/v2
        (`samplerCensoARG`)
        |
        +-------------------------------+
                                        |
                                        v
                             encuestador research run
                             + transport diagnostics
                                        |
                                        v
                   research.household-welfare-predictive/v1
                                        |
                  quarter-specific poverty-line/basket input
                                        |
                                        v
                           predictive FGT measurement
                                        |
                                        v
                         poverty-estimate-release/v2
                                        |
                                        v
                            argentina-poverty-atlas
```

The last two deployment edges are not fully canonical on default branches yet: the province/national Poverty producer is validated in open `indice-pobreza-UBA#27`, and detached real-release Atlas ingest is validated in open `argentina-poverty-atlas#23`.

## EPH source authority

`microdatos-EPH-INDEC` produces:

```text
publicdata.eph-microdata@1
```

The Sep 11 transport science pins the exact 2024-Q3 parent:

```text
eph-2024-q3-3b6a7a15c4af
```

The release preserves source custody and native household/person identity. Acquisition does not define targets, modeling cohorts, semantic alignment or monetary conversion.

## EPH-only analysis authority

`income-modeling-eph` now has an implemented and real-data-proven neutral boundary:

```text
research.eph-analysis-frame@1
```

It consumes exact EPH quarter releases, preserves `CODUSU + NRO_HOGAR + COMPONENTE`, preserves available survey-design fields without applying them, and rejects hidden target/rank/deflation semantics in the neutral plane.

It also implements:

```text
research.eph-income-study-cohort@1
```

which is a study-specific EPH artifact. Live cohort execution remains gated by an **approved** monetary-conversion parent; this does not block the separate `encuestador` transport study from using exact native EPH evidence under its own declared target semantics.

`income-modeling-eph` is therefore a parallel EPH-only scientific producer, not the Census scoring runtime.

## Census frame and target-year sample

`samplerCensoARG` owns the Census-side identity and selection boundary. The active contracts used by the real alignment are:

```text
research.census-frame@1
research.census-target-year-sample/v2
```

The target-year sample preserves:

- exact donor-frame identity;
- `frame_vintage=2010` distinct from target year;
- household as the selection unit;
- complete person membership for selected households;
- target person mass by department;
- explicit `selection_probability`;
- separately named design inverse-probability semantics;
- no generic populated `analysis_weight`/`sample_weight` that could be silently reused downstream.

The 2024 research integration uses exact sample:

```text
census-sample-2024-0839713eafea8d1b
```

The realized sample is a synthetic target-year-composition sample of donor Census units. It is not a 2024 Census.

## Semantic alignment

`eph-censo-aligner` owns the cross-source semantic plane, not statistical transport validity.

The real 2024-Q3 / CPV-2010 run materialized:

```text
eph-cpv2010-semantic-plane-2024q3-v1
```

with 47,564 EPH persons and 469,172 Census persons.

The Sep 11 reviewed P1-R plane contains 21 approved concepts; `H11` and `H16` are rejected, and temporal reconstruction is explicitly outside this commissioned plane. Large but valid `IX_TOT` values remain unclipped.

A concept may be semantically comparable without being a valid target-period state variable. That later temporal/transport judgment belongs to `encuestador-de-hogares`.

## Survey-to-Census inference

`encuestador-de-hogares` is the active transport instrument. Its current mainline science now includes:

- household-group-safe folds;
- direct and staged/lean baselines;
- hurdle income models with explicit zero/positive/nonresponse semantics;
- strong person/household diagnostics and oracle comparisons;
- paired information-plane experiments;
- nested empirical-residual predictive distributions;
- full Census research commissioning;
- a governed predictive welfare release builder.

The downstream artifact implemented on main is:

```text
research.household-welfare-predictive/v1
```

Current representation:

```text
Y_h = max(0, location_h + R)
```

where `location_h` is the commissioned household point-welfare location and `R` is an empirical residual distribution calibrated from leakage-safe EPH OOF evidence. The release records exact lineage, monetary scale, support policy and transport caveats.

The welfare artifact does **not** own poverty lines, adult equivalence or FGT mathematics. Changing the threshold should not require refitting the welfare model.

### Point welfare vs predictive welfare

A deterministic/point welfare value and a predictive welfare distribution are different interfaces. The current research path promotes the predictive distribution because low-tail prevalence was materially better calibrated than thresholding the compressed point prediction.

This does not mean the residual ECDF provides complete aggregate-estimate uncertainty. Poverty must declare uncertainty separately.

## Monetary references

`IPC-Argentina` publishes immutable `research.argentina-monetary-conversion/v1` **candidate** releases under a curated official-panel method and exposes exact coverage/maturity metadata. Scheduled candidate publication may remain green under thin latest-period coverage while the explicit approval gate remains stricter.

Therefore:

- candidate publication != approved conversion;
- analytical price product != official IPC authority;
- consumers requiring an approved conversion must continue to fail closed when approval is absent.

## Poverty-line / basket input

`canastasINDEC` consumes governed IPC candidate lineage and source basket data. Main now includes a period-parameterized bounded slicer over the existing v2 basket candidate, allowing a complete quarter such as 2024-Q3 to be selected for the research seam without changing acquisition or basket science.

The basket/line producer does not define Census geography. Threshold-area interpretation remains a separate boundary.

## Poverty measurement

`indice-pobreza-UBA` main preserves the deterministic v2 semantics and now also contains a predictive-welfare measurement path.

For the first predictive representation, Poverty integrates the marginal household welfare distribution against poverty/indigence thresholds to obtain exact empirical:

```text
FGT0
FGT1
FGT2
```

while keeping deterministic poverty semantics available in parallel.

It produces/validates the contract:

```text
poverty-estimate-release/v2
```

A release declares capabilities, exact IDs, parents, geography level, status and uncertainty state. It is a scientific artifact, not a browser data shape.

Open PR `indice-pobreza-UBA#27` adds the bounded local real producer from predictive welfare + Census frame + six regional lines to a detached release with 24 provinces + `ARG`, persons/households, poverty/indigence and FGT0/1/2. Real acceptance produced 300 facts, but because the PR remains open this is validated pending integration, not current `main` capability.

Its intended status is explicitly:

```text
research_estimate
uncertainty_status = not_supplied
```

## Geography and Atlas

`argentina-geography` supplies exact geography products independently of poverty values. The Atlas already has an exact IGN 24-province parent with zero-preserving province IDs.

Canonical Atlas `main` remains a synthetic, `noindex` demonstration surface. Open PR `argentina-poverty-atlas#23` validates strict ingest of a detached real `poverty-estimate-release/v2`, including checksums, status, 300-fact schema and 24 province IDs, without recomputation or invented uncertainty.

Until that PR merges, the public-product contract is proven on a branch but not canonical runtime behavior.

## Cross-repository rule

Prefer:

```text
consumer -> artifact contract -> immutable release
```

over:

```text
consumer -> sibling checkout -> internal function
```

Small duplicated validators are acceptable while contracts are young. Extract shared runtime only after repeated stable semantics demonstrate a real maintenance burden.
