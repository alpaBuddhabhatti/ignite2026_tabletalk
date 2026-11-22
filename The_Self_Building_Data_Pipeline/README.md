---
title: The Self-Building Data Pipeline
description: A reference design for using AI to build and troubleshoot a governed Microsoft Fabric data pipeline
ms.date: 2026-11-20
ms.topic: overview
keywords:
  - Microsoft Fabric
  - data pipeline
  - AI-assisted engineering
---

## Overview

This repository contains design and operating guidance for a synthetic retail
data-pipeline demonstration using Microsoft Fabric and AI-assisted engineering.
It focuses on two questions: where can AI accelerate pipeline development, and
how can engineers safely diagnose and recover from pipeline failures?

The materials describe a proposed reference workflow. They do not prove that a
pipeline, Foundry agent, or Fabric integration has been deployed. No executable
pipeline implementation is included in this folder.

## Reference workflow

The proposed pipeline ingests retail data from SQL, an API, and files, then
validates and publishes curated results in a Fabric Lakehouse. AI may help draft
code, explain failures, and suggest tests. Contracts, deterministic checks,
access controls, and authorized people remain responsible for correctness and
promotion.

The example uses synthetic customer, product, order, and inventory data. A
disposable copy of an input introduces schema drift so the recovery process can
be tested without modifying baseline or production data.

## Documentation

* [Reference architecture and agent workflow](./ARCHITECTURE_AND_WORKFLOW.md)
  describes the proposed components, responsibilities, and boundaries.
* [Use case and data design](./USE_CASE_AND_DATA.md) defines sample sources,
  contracts, business rules, and expected-results fixtures.
* [Demo runbook](./DEMO_RUNBOOK.md) provides setup, execution, validation, and
  reset procedures.
* [Security and human approval](./SECURITY_AND_HUMAN_JUDGMENT.md) covers
  identity, data handling, failure recovery, and review requirements.
* [Design decisions](./DESIGN_DECISIONS.md) lists decisions to resolve before
  implementing or demonstrating the pipeline.
* [HVE Core for Fabric engineering](./HVE_CORE_FABRIC_GUIDE.md) maps an
  AI-assisted research, planning, implementation, and review workflow to this
  design.

## Operating principles

* Treat generated code and diagnoses as proposals until reviewed and tested.
* Define source contracts and expected results independently of generated code.
* Keep development, test, and production identities and destinations separate.
* Reconcile source, accepted, and quarantined records with deterministic checks.
* Require human approval for ambiguous business semantics and production
  changes.
* Record run identifiers, source batch identifiers, code changes, and test
  evidence.

## Before implementation

Confirm the Fabric workspace, capacity, connections, permissions, and AI
capabilities available in the target environment. Decide which source
connectors will be live and which will use local fixtures. Use only synthetic
or explicitly approved anonymized data, and keep the demonstration isolated
from production.

Do not describe proposed components as deployed features. Record implementation
status and validation evidence as the design is turned into a working pipeline.
