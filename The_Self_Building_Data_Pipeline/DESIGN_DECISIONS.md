---
title: Pipeline Design Decisions
description: Decisions and evidence required before implementing the retail Fabric pipeline
ms.date: 2026-11-20
ms.topic: reference
keywords:
  - Microsoft Fabric
  - data contracts
  - data quality
  - pipeline design
---

## Purpose

Resolve these decisions with the relevant data, platform, security, and
operations owners before implementation. The suggested defaults are starting
points, not approved requirements.

## Decisions to resolve

| Area | Suggested starting point | Evidence or owner required |
|---|---|---|
| Source connectivity | Use development connections and small, synthetic fixtures first | Confirm connector support, network path, authentication, and source ownership |
| Source contracts | Version expected columns, types, keys, and update semantics | Source owner approves schema and business definitions |
| Schema drift | Fail validation and quarantine the affected input until meaning is confirmed | Data owner confirms aliases or approves a contract version |
| Invalid records | Preserve rejected rows with a reason; do not silently coerce values | Data owner defines reject, quarantine, and correction policies |
| Duplicate and replay behavior | Use stable source keys and make reruns idempotent | Agree on deduplication keys, update rules, and backfill behavior |
| Incremental processing | Track source batch/run identifiers and define a watermark policy | Source owner confirms change tracking and late-arrival semantics |
| Quality gates | Check keys, nulls, domains, freshness, counts, and business totals | Analytics owner sets thresholds and expected-result fixtures |
| AI assistance | Allow bounded code and diagnosis proposals, not unreviewed production writes | Security and engineering owners approve tools, data sent, and review gates |
| Runtime identity | Use a least-privilege Fabric connection identity | Platform owner verifies permissions for each source and destination |
| Promotion and rollback | Promote reviewed changes through the normal release process | Operations owner defines rollback, monitoring, and incident ownership |

## Minimum acceptance evidence

Before promoting a pipeline change, require:

* A reviewed contract and independently prepared expected result.
* Passing targeted and regression tests.
* Reconciled source, accepted, and quarantined record counts.
* Verified totals, freshness, and lineage for the curated output.
* Evidence that retries and replay do not double-count records.
* Reviewed identity, permissions, destinations, and side effects.
* An owner-approved rollback and promotion procedure.

## Status tracking

Record each decision with its owner, resolution, date, and supporting evidence
in the repository's normal issue or pull-request workflow. Do not treat an
example value or a generated suggestion in these documents as an approved
business rule.
