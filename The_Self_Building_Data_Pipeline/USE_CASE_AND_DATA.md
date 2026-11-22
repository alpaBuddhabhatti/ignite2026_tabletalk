---
title: Retail Use Case and Data Design
description: Synthetic retail sources, contracts, quality checks, and expected results for the Fabric pipeline
ms.date: 2026-11-20
ms.topic: concept
keywords:
  - Microsoft Fabric
  - retail data
  - data contract
  - data quality
---

## Business scenario

A retailer wants a dependable daily view for merchandising and store operations. Information about customers, products, orders, and inventory is split across a SQL system, an API, and files. Analysts currently reconcile the sources manually and may not notice stale, duplicated, or inconsistent data until a report looks wrong.

The demonstration uses a repeatable pipeline design that lands source data,
standardizes it, validates it, and publishes a small analytics-ready result.
Keep the scenario intentionally small enough to test and explain.

## Source map

Use fabricated data with stable IDs and a small, known expected result. The exact source-to-domain mapping is a proposal; confirm the environment can reach each source before promising a live connection.

| Source | Example data | Example fields | Demo concern |
|---|---|---|---|
| SQL database | Customers, products, orders, order lines | `customer_id`, `product_id`, `order_id`, `quantity`, `unit_price`, `order_ts` | Incremental extraction, keys, nulls, duplicate order lines |
| REST API | Current inventory by SKU and store | `sku`, `store_id`, `on_hand`, `updated_at` | Authentication, pagination, throttling, timeouts, response schema |
| File drop | Supplier inventory adjustments or daily stock CSV | `sku`, `store_id`, `adjustment_qty`, `effective_date` | Late or repeated files, malformed rows, schema drift |

If available connectors or tenant policy do not support the live API path, use a saved, representative API response as a file fixture and explicitly disclose the substitution. Do not imply the demo exercised a live connector if it did not.

## Pipeline stages

1. Define the source contract by recording expected columns, types, key
   definitions, update semantics, and business meaning.
2. Land a replayable source copy with ingestion time, source name, batch/run ID,
   and schema version.
3. Standardize types, names, timestamps, and units. Preserve source identifiers
   and reject or quarantine invalid rows.
4. Conform the data by joining order lines to product/customer dimensions and
   inventory to store/SKU keys.
5. Run deterministic quality assertions and reconcile source, accepted, and
   quarantined counts.
6. Publish a curated daily store/SKU view for ordered quantity, sales amount,
   available inventory, and data-freshness status.

Names such as Bronze/Silver/Gold can be used if they match the chosen Fabric implementation, but the stage boundaries and data contracts matter more than labels.

## Business rules to make explicit

Treat these as examples, not implied truths. Agree on them before generating code:

- An order line must have an order ID, product ID, positive quantity, valid timestamp, and a defined price.
- Each order-line identifier must be unique within its source system.
- Product and customer references must resolve to known records or be reported for review.
- Inventory cannot be negative unless the business explicitly uses negative values to represent backorders.
- Money uses a documented currency and decimal precision; do not silently infer currency from a symbol.
- A repeated source file or rerun must not double-count the same order lines.
- Late-arriving updates must have an explicit correction/window policy.

## Expected results fixture

Before the demo, prepare a tiny golden dataset and expected output independent of the AI-generated code. Include at least:

- Two or three stores, a few products, customers, and orders.
- One repeated order line, one unresolved product, one missing optional attribute, and one valid inventory update.
- Explicit expected source count, accepted count, rejected/quarantined count, and curated totals.
- Stable row keys and a run identifier so before/after results can be compared.

Define expected answers before running the demonstration. Keep the fixture
read-only and use it to independently verify the generated transformation.

## Safe failure fixture

Keep a baseline input and a separate, versioned failure copy. For example, rename `unit_price` to `price_each` in one copy of the order-line input while leaving the declared contract unchanged. The resulting failure should be deterministic and reversible.

The intended learning is not "make the error disappear." It is to:

1. Identify the failing source and pipeline run.
2. Compare the observed schema with the declared contract.
3. Ask AI to explain the evidence and propose options.
4. Decide whether to map the new field, reject/quarantine the source, or update the contract.
5. Review the change and prove it with fixture tests before publishing.

## Useful validation outputs

- Pipeline/run ID, status, duration, and linked source batch.
- Source rows, accepted rows, rejected rows, and reconciliation result.
- Quality-check results with clear pass/fail reasons.
- Curated table with freshness and lineage context.
- Proposed code change and targeted test output after repair.

Avoid adding an LLM-generated business conclusion to the pipeline output unless
the implementation includes a separately governed AI stage. Keep pipeline
facts deterministic.
