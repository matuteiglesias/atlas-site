---
title: Update matrix
status: current
owners: [poverty-ecosystem-engineering]
---

# Update matrix

This matrix is a routing aid, not a requirement to touch every listed page.

| Upstream event | Likely docs surface |
| --- | --- |
| repository ownership/boundary changes | `00-authority-and-principles`, `01-system-map`, `SYSTEM.yaml` |
| new/changed artifact contract or release handoff | `02-contracts-and-release-chain`; possibly `01-system-map` |
| identity, clock, weighting, temporal or monetary semantics | `03-semantics-identities-and-clocks` |
| component maturity / real-data proof / migration state | `04-current-state-and-migration`, `SYSTEM.yaml` |
| working-reference promotion/supersession | `05-working-reference-policy` plus the affected page |
| completed/superseded architecture work | `06-engineering-backlog` |
| EPH/Census sampling/alignment/transport science | `07-eph-census-scientific-decomposition` |
| encuestador transport or welfare output semantics | `08-encuestador-functional-contract` |
| scheduled refresh/automation behavior | `09-automation-and-refresh-loop` |
| cross-repository release discovery protocol | `10-release-discovery-contract` / `11-release-discovery-live-proof` |
| public front-door meaning materially changes | `README.md`, `docs/index.md` |

## Cross-cutting triggers

Always inspect `04-current-state-and-migration` and `06-engineering-backlog` when a major milestone closes several previously-blocking steps. These are the pages most likely to accumulate stale WIP.

Inspect `03-semantics-identities-and-clocks` whenever a new successful execution could tempt readers to collapse distinct concepts such as:

- EPH survey weights vs Census selection probability vs Poverty analysis semantics;
- Census donor vintage vs sampling target period vs welfare period;
- semantic comparability vs target-period validity;
- point welfare vs predictive welfare distribution;
- predictive unit-level uncertainty vs aggregate estimate uncertainty.

## Non-rule

Do not edit a page merely because it appears in this table. If the producer change does not alter the claim that page makes, leave it alone.
