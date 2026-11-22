---
title: Reference Architecture and Agent Workflow
description: Proposed architecture for a governed Fabric retail pipeline and AI-assisted engineering workflow
ms.date: 2026-11-20
ms.topic: concept
keywords:
  - Microsoft Fabric
  - Microsoft Foundry
  - Lakehouse
  - data pipeline
---

## Status and scope

This is a conceptual reference architecture for a retail pipeline demonstration.
It is not deployment documentation and does not establish that the components
or connections below are implemented or enabled in a particular tenant.
Confirm the target environment before selecting services or promising a live
integration.

## Data flow

```text
SQL orders ------\
Inventory API ----+-> Fabric ingestion and transformation
Product files ---/        |
                           v
                  Raw and standardized data
                           |
                           v
                 Validated curated Lakehouse
                           |
                           v
                  Fabric Data Agent (optional)
                           |
                           v
          Foundry decision workflow (optional)
                           |
                           v
              Human review and authorization
```

The pipeline owns deterministic data movement and transformation. If included,
a Fabric Data Agent provides a question-and-answer interface over selected
Lakehouse tables. A Foundry agent may coordinate evidence retrieval and draft
a decision brief. Neither agent is the system of record, and neither replaces
the pipeline's contracts or quality gates.

## Component responsibilities

| Component | Responsibility | Boundary |
|---|---|---|
| Source systems and fixtures | Provide SQL, API, and file records | Use synthetic or approved anonymized data |
| Fabric pipeline and notebooks | Ingest, standardize, validate, and publish data | Keep transformations deterministic and replayable |
| Lakehouse | Store raw and curated data | Publish only data that passes the agreed quality gates |
| Fabric Data Agent, if used | Answer questions over selected Lakehouse tables | Do not treat generated answers as a replacement for source data |
| Foundry agent, if used | Coordinate approved tools and summarize retrieved evidence | Do not infer unsupported business facts or execute unapproved writes |
| User interface, if used | Present questions and results | Do not grant the interface implicit data-write authority |
| Engineering workflow | Version changes, test them, and review diffs | Require approval before promotion |

These are proposed responsibilities. Verify each product's current capabilities,
permissions, and availability in the target environment.

## Engineering change flow

1. Record the requirement, source contract, expected result, and constraints in
   version control.
2. Research the existing artifacts and collect concrete run, schema, and test
   evidence.
3. Plan the smallest change, its test gates, rollback, and required approvals.
4. Generate or edit code only within the approved development scope.
5. Review the diff for business semantics, identity, data access, and side
   effects.
6. Run targeted tests, relevant regression tests, replay checks, and output
   reconciliation.
7. Promote through the team's normal pull-request and release process.

AI may assist with research, code drafts, failure analysis, and test ideas.
Deterministic execution and tests establish data behavior; authorized
engineering and data owners approve ambiguous mappings and production changes.

## Identity and trust boundaries

Keep the developer's interactive identity separate from the identity used by
Fabric connections at runtime. Grant each identity only the access needed for
its role. Do not copy secrets into prompts, notebooks, logs, or source control.

Treat source values, files, API responses, logs, and model-generated content as
untrusted input. A value from a source must never be treated as an instruction
to an agent. Review generated code before execution, especially changes to
destinations, permissions, retries, or write behavior.

## Failure and recovery

The recommended demonstration failure is a renamed field in a separate,
disposable fixture. The declared contract remains unchanged so the failing
validation provides evidence of the mismatch. The recovery process should:

1. Identify the failing run and pipeline activity.
2. Compare the observed schema with the approved contract.
3. Ask the data owner whether the new field has the same business meaning.
4. Propose a narrowly scoped mapping, quarantine, or contract update.
5. Test the change against the golden fixture and regression suite.
6. Verify counts, totals, lineage, replay behavior, and rollback before
   promotion.

Do not infer semantic equivalence from similar field names alone.

## Readiness evidence

Before calling an implementation ready for wider use, capture:

* Versioned source contracts and expected-results fixtures.
* Successful baseline and controlled-failure run identifiers.
* Accepted and quarantined counts with reconciliation results.
* Targeted and regression test results, including replay/idempotency checks.
* Identity, permission, and destination review.
* Monitoring, ownership, rollback, and promotion procedures.
