---
title: Demo Runbook
description: Prepare, execute, validate, and reset the synthetic retail pipeline demonstration
ms.date: 2026-11-20
ms.topic: how-to
keywords:
  - Microsoft Fabric
  - data pipeline
  - runbook
---

## Purpose and scope

Use this runbook to exercise the build, failure, diagnosis, and verification
workflow in an isolated development workspace. It assumes synthetic data and a
disposable failure fixture. It is not a production deployment procedure.

## Prerequisites

* Confirm the tenant, Fabric capacity, workspace, operator account, and
  required assets.
* Verify that planned Fabric and AI features are enabled, licensed, and
  available to the relevant identities.
* Confirm source connectivity, network paths, gateway availability where
  required, and API limits.
* Separate the operator identity from the identity that executes pipeline
  connections.
* Use only synthetic or explicitly approved anonymized records.
* Keep code, data, and generated artifacts in a dedicated development
  workspace.

## Prepare the baseline

1. Create small, version-controlled fixtures for SQL, API, and file inputs.
2. Define source contracts and expected results independently of AI-generated
   code.
3. Build or stage a known-good end-to-end pipeline.
4. Record baseline row counts, curated totals, validation results, run ID, and
   expected output.
5. Create a separate, disposable input copy with one controlled schema change.
6. Verify that the changed fixture fails the intended check without changing
   baseline output.
7. Prepare a known-good correction and regression test in case a proposed AI
   repair is unsuitable.
8. Record the prompts, inputs, and steps needed to reproduce the run.

## Execute the workflow

### 1. Define the acceptance criteria

Record the business question, source domains, output table, and measurable
acceptance criteria. Identify the data as synthetic and the workspace as
development.

### 2. Review the data contract

Check keys, types, currency, update behavior, and handling of invalid records
for representative order and inventory rows.

### 3. Generate one bounded change

Ask AI for one pipeline or notebook step. Specify the source fixture,
development destination, metadata to preserve, transformations, quality
assertions, and accepted/quarantined data paths. Do not use a broad request
without contracts and testable acceptance criteria.

### 4. Review the generated change

Inspect source and destination paths, authentication, types, null behavior,
joins, duplicate handling, idempotency, retries, logging, and quarantine
behavior. Keep credentials out of prompts and code.

### 5. Run baseline quality checks

Execute the baseline and compare it with the independently prepared expected
results. Record the passing rules and accepted/rejected row counts.

### 6. Test a reusable pattern

If the design includes a source-agnostic helper, parameterized step, or reusable
contract, verify that source-specific assumptions remain explicit.

### 7. Inject the controlled failure

Run the isolated schema-drift fixture once. Capture the run ID, error,
observed schema, and failed contract or test. Do not modify the baseline,
shared files, or production artifacts.

### 8. Request an evidence-based diagnosis

Use a bounded prompt such as:

```text
Use the failing run output, declared order-line contract, and observed fixture
schema. Identify the exact mismatch, cite the evidence, explain what you cannot
infer, and propose the smallest reversible fix. Do not execute it or change
the source.
```

Review the proposed diff and reject changes that are unsupported by the
contract or data-owner decision.

### 9. Apply and verify an approved repair

Apply a repair only when its business meaning is confirmed. Run targeted and
regression tests, reconcile counts and totals, compare with the golden fixture,
and verify replay and rollback behavior.

## Acceptance evidence

* Baseline and failure run identifiers.
* Observed-versus-expected schema and contract-check results.
* Accepted and quarantined counts with reconciliation.
* Targeted and regression test results.
* Curated output totals, freshness, and lineage checks.
* Identity, permission, and destination review.
* Verified replay behavior and documented rollback.

## Reset and cleanup

1. Stop or disable any demonstration schedules or triggers.
2. Restore the baseline fixture using the documented non-destructive reset.
3. Confirm that shared and production assets were not modified.
4. Remove temporary credentials or access granted solely for the run.
5. Retain approved prompts, source contracts, code diffs, and test evidence
   according to organizational policy.

## Fallback

Prefer local fixtures over unreliable external dependencies. If a staged result
or recording is used, label it as such. Do not present recorded or substituted
data as a live connector result.
