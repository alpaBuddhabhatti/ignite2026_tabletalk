---
title: Security, Failure Handling, and Engineering Judgment
description: Security controls and human review requirements for an AI-assisted Fabric pipeline
ms.date: 2026-11-20
ms.topic: concept
keywords:
  - Microsoft Fabric
  - security
  - human approval
  - data pipeline
---

## Security controls

Treat AI-generated code as untrusted until reviewed and tested. Keep the
demonstration in an isolated development workspace with synthetic or approved
anonymized data.

| Area | Demo control | Production question |
|---|---|---|
| Identity | Use the supported Fabric connection/connection identity for the chosen source. Grant only the access needed to read the demo inputs and write to the demo destination. | Which identity executes each pipeline step? Does it have access only to the required data and operations? |
| Secrets | Store credentials in supported secret/connection facilities such as Key Vault-backed connections where available. Never paste secrets into prompts, notebooks, logs, or source control. | How are credentials rotated, scoped, audited, and revoked? |
| Data minimization | Use fabricated customer IDs and records. Do not place personal, payment, or confidential data in prompts. | What classification, masking, retention, residency, and consent obligations apply? |
| Workspace boundary | Use a separate development workspace and destination. Avoid production write access during the demonstration. | How are development, test, and production separated and promotion approved? |
| Network | Validate private connectivity, gateway, firewall, and API allow-list behavior before the demo. Avoid weakening controls just to make a live connector work. | Are source and destination network paths approved, monitored, and resilient? |
| AI input | Treat API responses, file contents, and text fields as untrusted data. A data value is not an instruction to the assistant. | How is prompt injection through source data prevented from causing unauthorized actions? |
| Generated code | Review the diff, dependencies, query scope, destinations, and side effects before execution. | Is code scanned, peer-reviewed, versioned, tested, and promoted through the normal release process? |
| Auditability | Keep run IDs, source batch IDs, prompt/version notes, code diffs, and quality results. Redact secrets and personal data. | Can an operator reconstruct who ran what, with which code and data, and why? |
| Output | Use deterministic validations to gate publication; label AI suggestions as proposals. | Who owns acceptance thresholds, exception handling, lineage, and data-quality SLAs? |

Use the organization's approved tenant controls and Fabric workspace roles. Do
not assume that a successful developer test means the pipeline's runtime
identity or another operator has the same access.

## Failure-injection plan

Choose one primary failure for a controlled demonstration. Schema drift is
recommended because it is visible, bounded, and easy to reset. Keep the
remaining cases as alternatives rather than introducing unpredictable
failures.

| Failure | Safe setup | Evidence to inspect | Recovery to test |
|---|---|---|---|
| Schema drift (recommended) | In a separate fixture, rename a declared column such as `unit_price` to `price_each`. | Failing pipeline step, run ID, observed versus expected schema, failed contract assertion. | Decide whether to map the alias, quarantine the source, or version the contract. Test output against the golden fixture. |
| Malformed value | Add a non-numeric quantity or invalid timestamp to a fixture copy. | Rejected row, validation reason, accepted/rejected count reconciliation. | Quarantine or reject according to an explicit policy; never silently coerce to a misleading value. |
| Duplicate/replayed batch | Re-submit the same file or source batch in the dev workspace. | Run/batch ID, duplicate key check, before/after curated count. | Demonstrate idempotent upsert/deduplication and prove the replay did not double-count. |
| API throttle or timeout | Use a controlled mock response or known test endpoint that returns a transient failure. | Retry attempt count, status code, request correlation ID, elapsed time. | Bounded exponential backoff, timeout, and clear terminal failure; avoid infinite retries. |
| Credential/access failure | Use a separate test identity with missing read access, or inspect a captured, sanitized failure. | Identity used, denied operation, source, permission scope. | Request narrowly scoped access; do not switch to a broad administrator identity. |
| Invalid business rule | Add an unresolved product key or an inventory value that violates the agreed rule. | Failed relationship/domain check and affected row count. | Route to quarantine/review or apply an approved rule; do not guess missing mappings. |

## AI recovery protocol

For each repair, require AI to:

1. Name the failing run and stage.
2. Quote or summarize the relevant error/schema/test evidence.
3. Separate observed facts from hypotheses.
4. Propose the smallest scoped change and identify its assumptions.
5. State risks, side effects, and rollback.
6. Avoid executing or publishing a change without explicit human approval.

The engineer then:

1. Reviews the source contract and generated diff.
2. Verifies the proposed business mapping with the data owner.
3. Runs targeted tests and the full relevant regression suite.
4. Checks row counts, rejected records, totals, freshness, and lineage.
5. Confirms idempotency and rollback behavior.
6. Approves promotion through the normal change-control process.

## What AI can help with

- Drafting ingestion and transformation boilerplate from explicit contracts.
- Explaining a failure based on actual logs and observed schemas.
- Suggesting test cases for known rules and edge cases.
- Producing documentation or a reusable template.
- Summarizing a code diff and highlighting assumptions for review.

## What still needs engineering and data-owner judgment

- Defining what an order, sale, customer, valid price, and current inventory mean.
- Choosing keys, currency, time zone, cutoff window, and late-arrival semantics.
- Deciding whether a bad record is rejected, quarantined, corrected, or legally retained.
- Designing idempotency, watermarking, retries, backfill, and schema-version policy.
- Evaluating access, privacy, retention, residency, threat model, and downstream impact.
- Setting quality thresholds, SLAs, alert owners, support rota, and recovery objectives.
- Reviewing generated code and dependencies and approving production writes.

## Demonstration boundaries

- The AI assists with code and diagnosis; deterministic pipeline logic and tests establish correctness.
- Do not describe a notebook or pipeline as production-ready solely because one sample run succeeded.
- Do not call a generated repair root cause unless evidence supports it.
- Do not expose secrets or use real personal data for convenience.
- Do not run destructive fixes, uncontrolled retries, or production writes.
- Clearly distinguish live results from pre-recorded or staged fallback material.
