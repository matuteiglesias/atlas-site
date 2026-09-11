---
title: Documentation maintenance — start here
status: current
owners: [poverty-ecosystem-engineering]
---

# Documentation maintenance — start here

This is the entrypoint for an autonomous engineering agent updating the poverty ecosystem docs.

## What this repository owns

`atlas-pobreza-docs` is authoritative for cross-repository engineering architecture: responsibility boundaries, artifact handoffs, shared status vocabulary, identities/clocks that must remain distinct, and the integration-level interpretation of migration state.

It is not authoritative for a producer repository's implementation details, scientific method, concrete release values, QA, or official-source status.

## Reading order

1. `AGENTS.md`
2. `README.md`
3. `SYSTEM.yaml`
4. `docs/architecture/00-authority-and-principles.md`
5. the architecture pages affected by the change
6. `docs/maintenance/carry_state.yaml`
7. exact evidence in affected producer repositories

Use `docs/architecture/05-working-reference-policy.md` before promoting claims from historical or working-reference material.

## When to refresh

Pull a documentation refresh when a real producer or consumer event changes at least one of:

- repository authority or responsibility;
- an artifact/release contract crossing a repository boundary;
- identity, clock, weighting, temporal or monetary semantics;
- component maturity or real-data proof;
- a scientific conclusion that affects integration design;
- an active architecture backlog item;
- a downstream consumer capability.

Do not run a complete estate audit for every edit. Start from `carry_state.yaml`, inspect the changed producer evidence, then expand only where dependencies changed.

## Minimum workflow

1. Identify the upstream change and exact producer evidence.
2. Classify the claim: architecture, implemented contract, real-data proof, research result, limitation, or unresolved decision.
3. Build a small cross-repo impact map.
4. Edit only affected authoritative docs.
5. Reconcile stale backlog/status claims.
6. Update carry state and refresh handoff.
7. Build and verify the site.

## Acceptance

A refresh is complete when another engineer can tell:

- what is implemented versus proposed;
- which producer evidence supports the claim;
- which exact boundary changed;
- what remains research-only or unresolved;
- where the next refresh should start.

The goal is architectural memory that can be continued without oral context, not maximal documentation volume.
