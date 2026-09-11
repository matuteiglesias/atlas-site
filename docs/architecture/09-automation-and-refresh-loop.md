---
title: Automation and refresh loop
sidebar_position: 10
status: current
owners: [poverty-ecosystem-engineering]
---

# Automation and refresh loop

The poverty ecosystem uses scheduled jobs as **liveness and convergence instruments**, not as substitutes for scientific judgment.

A workflow may discover, materialize, validate and package evidence. It may not silently convert a candidate into an approved scientific claim merely to keep CI green.

## Two DAGs

Keep these questions separate:

> Which exact artifacts and scientific authorities justify a result?

and:

> When an exact parent changes, which bounded executors should run so the ecosystem can converge again?

The first is the scientific DAG. The second is the automation DAG.

## Candidate production != promotion

The recurring pattern remains:

```text
discover exact source/parent
        |
        v
materialize candidate
        |
        v
validate identity/schema/QA
        |
        v
publish or retain immutable candidate evidence
        |
        v
consumer checks compatibility
        |
        v
scientific/promotion gate
```

A scheduled green candidate build says the executor and current evidence boundary are alive. It does not grant methodological approval.

## Current operating state — Sep 11

The scheduled layer has moved beyond the old fixture-only maturity skeleton.

### Root/source producers

- `microdatos-EPH-INDEC` keeps bounded official EPH discovery and deterministic candidate publication alive.
- `IPC-Argentina` publishes immutable monetary-conversion **candidates** and keeps strict approved-mode semantics separate; latest-period thin coverage can be advisory for candidate publication without weakening the approval gate.
- `canastasINDEC` keeps official basket-source / exact IPC-parent integration alive.
- `income-modeling-eph` keeps its EPH-only neutral-frame/study boundaries alive; live study execution can correctly remain blocked on an approved monetary conversion.

### Census / semantic layer

`samplerCensoARG` now runs a weekly **target-year/v2 contract pulse** rather than describing itself as fixture-only. The current schedule is Tuesday 11:17 UTC. It exercises:

- target-year design tests;
- donor-frame identity;
- complete household membership;
- weight-separation semantics;
- `frame-check`;
- `sample-v2-check`.

Hosted CI deliberately does not reconstruct the large private/local CPV-2010 sample. That is a data-portability boundary, not missing sampling architecture.

`eph-censo-aligner` follows Tuesday 11:47 UTC. Its semantic research pulse:

- keeps fixture/check/smoke health;
- verifies the frozen real 2024-Q3 review policy and `encuestador` handoff share exact parent/release identities;
- reports approved vs needs-judgment concepts;
- keeps stronger reviewer/methodological approval distinct from ordinary research-liveness success.

The large semantic-plane payload remains local until it has a portable governed distribution.

### Transport layer

`encuestador-de-hogares` now has two different recurring responsibilities:

1. cheap contract/release-surface liveness for the current predictive-welfare architecture;
2. bounded heavyweight **Real EPH 2024-Q3 acceptance every Wednesday at 09:17 UTC**, with concurrency protection.

The weekly layer now covers `research.household-welfare-predictive/v1` and the current science surface. Hosted CI intentionally does **not** silently recreate the large local Census commissioning artifacts.

This is the correct boundary: exact real EPH reproducibility is portable enough for scheduled proof; full Census scoring remains anchored to governed local/large parents until distribution changes.

### Poverty layer

`indice-pobreza-UBA` weekly maintenance now covers both:

- deterministic v2 release smoke/full unit suite;
- current predictive-welfare research seam liveness.

It verifies that the real-seam runner is callable and that frozen sampler/semantic/EPH identities remain inspectable. The current boundary is explicit:

- large Census/semantic/predictive-welfare artifacts remain locally anchored;
- aggregate uncertainty remains `not_supplied`.

A green weekly pulse is not a recomputed official poverty statistic.

### Atlas and docs

`argentina-poverty-atlas` keeps lint/typecheck/test/build health alive. Canonical main remains synthetic/noindex until a real detached Poverty release is intentionally integrated.

`atlas-pobreza-docs` maintains build/deployment verification and now also carries `docs/maintenance/carry_state.yaml` so documentation refreshes can start from exact previously-inspected producer refs.

## Large local data is an explicit automation boundary

Several real scientific seams are proven locally but should not be reconstructed opportunistically in generic hosted CI:

- CPV-2010 raw/local donor frame;
- full target-year Census sample payload;
- full real semantic plane materialization;
- full Census scoring products;
- predictive welfare release payload derived from those large parents.

Do not interpret this as architecture incompleteness. Portable contracts and reproducible identities can be tested remotely while large/data-restricted execution remains bounded to an authorized environment.

If a future portable immutable distribution becomes available, consumers may add exact remote acceptance without changing scientific authority.

## Root-trigger independence

Independent source changes should not invalidate unrelated branches of the graph.

Examples:

- new EPH quarter does not require a new Census sample if sampling parents are unchanged;
- new IPC observation does not change semantic EPH/Census alignment;
- new geography release does not retrain the welfare model by itself;
- a new target-year demographic parent changes sampler composition, not EPH source semantics;
- a new welfare model should not rewrite Poverty methodology.

Preserving these independences prevents unnecessary downstream churn.

## Durable release discovery

The live `ecosystem-release-discovery/v1` proof remains the correct cross-repository distribution pattern for portable artifacts:

```text
producer publishes immutable candidate
        |
        +--> optional direct notification
        |
        +--> scheduled consumer polling as correctness fallback
```

The contract is shared; the runtime implementation remains repository-local while the interface is young.

Do not build a central release bus or estate DAG service merely because multiple consumers implement the protocol.

## Consumer locks

Consequential consumers compare:

```text
exact parent currently pinned
        vs
new eligible immutable candidate
```

A newer source may cause a new downstream **candidate**. It must not silently replace an approved parent lock or scientific result.

This is particularly important for:

- EPH source revisions;
- semantic-plane changes;
- monetary conversion status;
- target-year Census sample identity;
- transport model/release changes;
- poverty lines/method revisions.

## Failure classification

A red run should reveal a bounded reason. Useful classes include:

```text
source_unavailable_or_ambiguous
source_schema_changed
source_lock_failed
candidate_build_failed
parent_not_portable_in_hosted_ci
semantic_review_incomplete
identity_or_join_gate_failed
monetary_approval_missing
transport_support_or_domain_shift_failed
poverty_parent_missing_or_incompatible
aggregate_uncertainty_not_supplied
public_consumer_contract_drift
```

Do not patch a red scientific gate into green by weakening its meaning.

## Mature maintenance definition

The ecosystem is operationally mature when:

1. root authorities have bounded discovery/validation paths;
2. portable outputs have immutable identities and parent locks;
3. consumers can detect newer eligible parents without sibling imports;
4. scheduled jobs are idempotent when evidence is unchanged;
5. parent changes rebuild candidates rather than silently approving results;
6. scientific/semantic failures remain visible;
7. large/local data boundaries are explicit rather than accidentally reconstructed;
8. Poverty can consume exact welfare/frame/line/method parents without model imports;
9. Atlas can consume a governed Poverty release without browser science;
10. the docs carry enough inspected-state metadata for the next maintainer/agent to resume without rediscovery.

The target is not “all Actions green.” It is that green and red each have a precise, bounded meaning.
