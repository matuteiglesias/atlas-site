---
title: Evidence and precedence
status: current
owners: [poverty-ecosystem-engineering]
---

# Evidence and precedence

Documentation status must follow evidence strength. This page governs how an agent translates producer evidence into cross-repository claims.

## Precedence

For a concrete artifact or implementation claim:

1. exact producer release/contract and producer-local acceptance evidence;
2. merged producer implementation and current producer `SYSTEM.yaml`/canonical docs;
3. deterministic fixture or CI proof;
4. open producer PR;
5. issue/design proposal;
6. historical notebook, work packet, retrieved note or stale ecosystem page.

This documentation repository remains authoritative for the *integration architecture*, but it may not silently override stronger producer evidence about what actually exists.

## Evidence classes

### Implemented contract

Use when a producer has merged the contract/behavior on its canonical branch. This proves the interface exists, not that it has been exercised on consequential real data.

### Real-data-proven

Use when the implemented contract has completed an exact real-data execution with identifiable parents, outputs and acceptance/QA evidence.

This does not automatically imply official, production-approved or scientifically final.

### Fixture-proven

Use when deterministic/synthetic fixtures prove the code and boundary but no exact real parent has crossed it yet.

### Research-only

Use for real empirical/scientific results that are deliberately non-official, experimental, commissioning evidence, or not yet approved for consequential publication.

### Proposed

Use for target architecture, issues, unmerged PRs or explicit designs that have not become canonical producer behavior.

### Legacy

Use for superseded code/artifacts that remain useful for archaeology, regression evidence or semantic clues.

### Blocked

Use only when a named prerequisite is missing or a fail-closed gate is intentionally red. Name the blocker.

## Important non-implications

- A merged implementation does not imply scientific approval.
- A successful real-data research run does not make a result official.
- An open PR is not implemented state.
- A green docs build does not validate science.
- A model with better EPH evidence is not automatically deployable on Census.
- Semantic comparability does not imply temporal equivalence.
- Predictive household welfare uncertainty does not automatically supply aggregate poverty-estimate uncertainty.

## Recording disagreement

If producer evidence conflicts with architecture documentation, do not hide the divergence. Record:

- the exact current producer behavior;
- the accepted/target architecture if still relevant;
- whether the mismatch is intentional, transitional or unresolved.

Architecture can lead implementation. It cannot pretend implementation has already followed.
