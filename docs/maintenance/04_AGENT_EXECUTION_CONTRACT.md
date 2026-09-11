---
title: Agent execution contract
status: current
owners: [poverty-ecosystem-engineering]
---

# Agent execution contract

Autonomous agents may perform ordinary documentation maintenance without asking the user to restate repository state.

## Allowed autonomous work

- inspect current producer repositories and exact public/accessible evidence;
- reconcile current-state and migration pages;
- update cross-repository contract/boundary documentation;
- retire or supersede stale backlog items;
- refresh machine/human carry state;
- fix navigation or wording necessary to keep the authoritative surface coherent;
- open one documentation PR with verification evidence.

## Boundaries

- Do not modify producer repositories from a docs-maintenance task.
- Do not create new scientific behavior, release schemas or producer authority in this repo.
- Do not invent release IDs, metrics, maturity states, dates or conclusions.
- Do not silently turn unresolved scientific questions into software conventions.
- Do not infer that an open PR is implemented.
- Do not infer that a real-data research run is official or production-approved.
- Do not erase useful historical evidence merely because it is no longer current; mark supersession or leave it in the subordinate reference layer.
- Do not introduce a new shared framework/registry/service simply because several repos repeat a contract.

## Evidence discipline

When a current-state claim changes, retain enough source detail that the next maintainer can recover why:

- producer repository;
- revision, PR, release or artifact identity when material;
- the exact claim supported;
- limitations that remain.

Avoid copying entire manifests or implementation logs into this repo.

## Conflict handling

If producer evidence conflicts:

1. identify the conflicting sources;
2. prefer current canonical producer evidence for concrete behavior;
3. preserve the target architecture separately if still intentional;
4. describe the mismatch as migration debt or an unresolved decision.

Do not pick whichever source makes the documentation cleaner.

## Editorial discipline

Prefer a small number of authoritative pages with strong boundaries over proliferating new topical notes. Historical work packets are not the normal place for current conceptual truth.

The objective is to reduce future rediscovery and rework, not to maximize documentation changes.
