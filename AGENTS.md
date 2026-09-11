# AGENTS.md

This repository is the cross-repository engineering documentation authority for the Argentina poverty ecosystem.

Before editing current-state or architecture claims, read `docs/maintenance/00_START_HERE.md` and the relevant files under `docs/architecture/`.

## Authority rules

- Producer repositories own their own science, implementation, concrete releases, QA and producer-local contracts.
- This repository owns the cross-repository architecture, boundaries, integration vocabulary and migration/status interpretation.
- Exact producer evidence outranks stale ecosystem documentation about concrete implementation state.
- Target architecture must never be presented as already implemented.
- Historical notes, `CODEX_*`, `WORK_PACKET_*`, notebooks and working-reference pages are evidence, not automatic authority.

## Maintenance rules

- Inspect affected producer repositories before changing a current-state claim.
- Preserve meaningful state distinctions such as `current`, `real-data-proven`, `fixture-proven`, `research-only`, `proposed`, `legacy` and `blocked`.
- Do not bulk-rewrite working-reference material. Update authoritative architecture first and promote older pages only when they become relevant and source-backed.
- Do not modify producer repositories from a docs-maintenance task.
- Do not invent release IDs, metrics, dates, maturity states or scientific conclusions.
- If producer evidence conflicts, document the conflict rather than choosing the convenient version.
- Prefer one coherent docs PR over many tiny maintenance PRs.

## Required closeout

Before concluding a docs refresh:

1. update `docs/maintenance/carry_state.yaml`;
2. update `docs/maintenance/LAST_REFRESH.md`;
3. run the repository's build/deployment verification commands;
4. state which claims were deliberately not promoted.

A green documentation build proves the site renders. It does not prove scientific validity.
