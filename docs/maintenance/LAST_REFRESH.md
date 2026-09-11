---
title: Last documentation refresh
status: current
owners: [poverty-ecosystem-engineering]
---

# Last documentation refresh — 2026-09-11

## Scope

Refreshed the authoritative poverty ecosystem architecture after the Sep 10–11 real-data transport/poverty sprint and established a reusable autonomous docs-maintenance bundle.

## Producer repositories inspected

- `microdatos-EPH-INDEC`
- `income-modeling-eph`
- `samplerCensoARG`
- `eph-censo-aligner`
- `encuestador-de-hogares`
- `IPC-Argentina`
- `canastasINDEC`
- `indice-pobreza-UBA`
- `argentina-geography`
- `argentina-poverty-atlas`

Exact inspected refs and pending PR heads are recorded in `carry_state.yaml`.

## Major state transitions recorded

1. `income-modeling-eph` now has a real-data-proven neutral EPH analysis-frame boundary; it remains EPH-only and outside the active Census scoring runtime.
2. `samplerCensoARG` target-year/v2 sampling semantics are implemented and consumed by the real alignment, rather than being a future blocker.
3. `eph-censo-aligner` materialized a real 2024-Q3 EPH / CPV-2010 feature plane with a 21-field P1-R deployment surface.
4. `encuestador-de-hogares` moved from runtime-pending design to real research commissioning: household-safe experiments, P1-R/P2 information frontier, nested predictive distribution, full Census scoring, and `research.household-welfare-predictive/v1` are on main.
5. The main scientific diagnosis is now explicit: positive-income amount/distributional compression dominates the baseline error; Census-compatible information helps; richer EPH-only information provides an additional ceiling; labor state contains signal but current reconstruction captures little of the oracle gain.
6. Q7 supports predictive threshold estimation over hard thresholding of compressed point welfare.
7. Q8 scored all 469,172 Census persons / 141,863 households but remains research-only with material transport caveats.
8. `canastasINDEC` can expose a bounded 2024-Q3 poverty-input slice.
9. `indice-pobreza-UBA` main now contains the predictive-welfare FGT seam.
10. Weekly sampler/semantic/encuestador/Poverty pulses were updated to match the current architecture rather than obsolete fixture-only gates.

## Claims deliberately not promoted

- The Sep 11 chain is **not** official poverty statistics.
- P2 EPH-only features are **not** authorized Census deployment features.
- The predictive household residual distribution is **not** complete aggregate-estimate uncertainty.
- `uncertainty_status=not_supplied` remains the truthful aggregate state.
- `indice-pobreza-UBA#27` is validated but still open; the real 24-province + ARG producer is not described as canonical mainline capability.
- `argentina-poverty-atlas#23` is validated but still open; canonical Atlas main remains a synthetic/noindex demo.
- Temporal reconstruction of donor-vintage state is not silently assumed.
- Weak-support / source-universe problems are not repaired by clipping or row deletion.

## Main unresolved questions

- transport support/domain shift and the private/collective dwelling universe;
- target-period latent-state reconstruction;
- amount-model formulation after the information frontier;
- aggregate uncertainty propagation;
- cross-period/year replication;
- integration/promotion of the real Poverty and Atlas edges.

## Documentation surfaces changed

- root `AGENTS.md`
- `README.md`
- `SYSTEM.yaml`
- `docs/index.md`
- `docs/architecture/01-system-map.md`
- `docs/architecture/02-contracts-and-release-chain.md`
- `docs/architecture/03-semantics-identities-and-clocks.md`
- `docs/architecture/04-current-state-and-migration.md`
- `docs/architecture/06-engineering-backlog.md`
- `docs/architecture/07-eph-census-scientific-decomposition.md`
- `docs/architecture/08-encuestador-functional-contract.md`
- `docs/architecture/09-automation-and-refresh-loop.md`
- `docs/maintenance/*`

## Verification

Final verification should run from the refresh branch:

```bash
npm ci
npm run build
python scripts/verify_deployment_config.py
```

The result of those commands is recorded in the PR closeout; a green site build validates documentation mechanics, not scientific truth.
