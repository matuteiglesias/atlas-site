---
title: Refresh workflow
status: current
owners: [poverty-ecosystem-engineering]
---

# Refresh workflow

Use this loop for ordinary autonomous maintenance.

## 1. Read carry state

Start from `carry_state.yaml`. It is a navigation checkpoint, not a substitute for producer evidence.

## 2. Discover producer changes

Inspect the affected producer's current canonical branch, recent merged PRs, current contracts/system metadata, and any exact release/run evidence relevant to the claim.

Expand to adjacent producers only when a boundary, parent, consumer, identity or clock changed.

## 3. Build an impact map

For each material change record:

- producer and exact revision/evidence;
- previous documented state;
- new supported state;
- downstream boundary affected;
- architecture pages likely affected;
- claims that remain deliberately unpromoted.

## 4. Classify every claim

Use one of these classes:

- architecture;
- implemented contract;
- real-data proof;
- research result;
- limitation;
- unresolved scientific decision.

Do not compress these into one generic `done` state.

## 5. Edit authoritative pages first

Prefer `docs/architecture/` plus `SYSTEM.yaml`, `README.md` and `docs/index.md` where needed.

Do not bulk-rewrite working-reference pages. Promote or repair them only when an active decision/consumer makes them relevant and the evidence passes the working-reference promotion test.

## 6. Reconcile WIP

When a former backlog item is now merged/proven, remove it from active WIP or mark it superseded. Do not keep stale TODOs for continuity.

Conversely, do not close a scientific question merely because the software path now executes.

## 7. Refresh repository state

Update:

- `SYSTEM.yaml` verification date/status if justified;
- `docs/maintenance/carry_state.yaml` with inspected refs and current triggers;
- `docs/maintenance/LAST_REFRESH.md` with the human handoff.

## 8. Verify

Run the repository's canonical checks, normally:

```bash
npm ci
npm run build
python scripts/verify_deployment_config.py
```

Also check YAML syntax, touched Markdown links/front matter, and sidebar references.

## 9. Hand off one coherent change

Prefer one reviewable documentation PR with:

- producers inspected;
- state transitions;
- stale claims/backlog retired;
- claims deliberately not promoted;
- remaining scientific questions;
- verification evidence.

## Boundedness rule

Do not turn every refresh into a full estate census. The maintenance loop is pull-based: current consumer/science changes determine what must be re-inspected.
