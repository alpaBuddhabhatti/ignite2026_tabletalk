# HVE Core for Fabric pipeline engineering.
#HVE Core is Microsoft’s Hypervelocity Engineering framework that adds production‑ready agents, reusable prompts, coding instructions, and workflow skills to GitHub Copilot, available as a VS Code extension or GitHub repository.

## Purpose

This guide records how to install HVE Core for GitHub Copilot and adapt its
Research → Plan → Implement → Review workflow to Microsoft Fabric pipeline work.
HVE Core is an engineering workflow library; it is not a Fabric pipeline
runtime or a replacement for Fabric controls.

The operating principle is:

> AI carries the rules; humans keep the judgment.

## Installation

### Preferred route: HVE Core plugin in VS Code

In VS Code:

1. Start a new Copilot-harness chat.
2. Open the Command Palette.
3. Run **Chat: Open Customizations**.
4. Select **Browse** → **Configure Marketplaces** → **Add Marketplace**.
5. Enter `microsoft/hve-core`.
6. Return to **Browse**, choose the HVE Core marketplace, and install
   `hve-core`.
7. Approve the trust prompt.
8. Run **Developer: Reload Window**.

### Verification

Open a new Copilot chat and verify:

- The chat uses the Copilot harness.
- The agent picker contains **RPI Agent**.
- `/` completion exposes HVE Core skills such as
  `hve-core:rpi-research`.

Use one installation route at a time. Installing the plugin and the VS Code
extension together can duplicate agents and skills.

### Fallback route: VS Code extension

Install `ise-hve-essentials.hve-core` from the VS Code Marketplace only if the
plugin workflow cannot be used. The HVE Core project notes that the extension's
instructions may not reach Copilot sessions and it does not include canvases,
so the plugin remains preferred.

## Installation used for this workspace

HVE Core was installed through the VS Code extension route:

```text
ise-hve-essentials.hve-core@3.2.2
```

Run **Developer: Reload Window**, then verify that **RPI Agent** is available in
a new Copilot-harness chat. Copilot CLI was not available in this environment,
so its plugin installation route was not used.

## Guardrails for Fabric work

- Never give an agent broad production workspace write access.
- Use development/test workspaces for generated changes.
- Keep source and destination access in Fabric connections.
- Do not paste credentials, tokens, or unnecessary personal data into prompts.
- Use an explicit data contract and deterministic tests.
- Separate the presenter's Copilot/user identity from the identity that executes
  the Fabric pipeline.
- Require a reviewed pull request before promotion.
- Preserve run IDs, source batch IDs, diff evidence, tests, and rollback notes.

## Map HVE roles to Fabric engineering

The base HVE install does not contain a collection of Fabric-specific runtime
agents. Reuse or adapt its patterns:

| HVE/HVE-style role | Fabric use |
|---|---|
| RPI Agent | Research, plan, implement, and review a bounded Fabric change |
| Research | Inspect contracts, pipeline definitions, logs, schemas, and prior work |
| Plan | Define scope, test gates, rollback, and explicit human decisions |
| Implement | Update a Fabric artifact, notebook, script, test, or documentation |
| Review | Reconcile the diff against contract, safety, and regression evidence |
| Security review | Examine identity, permissions, secrets, destinations, and side effects |
| Architecture review | Record trade-offs, dependencies, and deployment boundaries |
| Documentation | Keep runbooks, prompts, and operational context current |

## Sample prompts

### Research: Understand a Fabric failure

```text
Use HVE RPI research for a Fabric pipeline investigation.

Goal: Understand why the retail order ingestion failed in development.
Read the supplied run output, expected data contract, observed schema, and
existing transformation code.

Produce:
1. Facts from the run and artifacts.
2. The exact expected-versus-observed mismatch.
3. Hypotheses and confidence.
4. What cannot be inferred.
5. Security or data-sensitivity concerns.

Do not change files or suggest production access.
```

### Plan: Create a safe schema-drift repair

```text
Use HVE RPI planning for the confirmed Fabric schema-drift finding.

Constraints:
- The development fixture changed `unit_price` to `price_each`.
- Do not assume the fields have the same business meaning.
- The proposed change must be scoped to this supplier/source.
- Include targeted tests, regression tests, idempotency checks,
  rollback, and required data-owner approval.
- Do not execute or deploy the change.
```

### Implement: Generate a bounded patch

```text
Use HVE RPI implementation for the approved plan.

Modify only the development notebook transformation and its tests:
- Add the approved supplier-specific mapping.
- Preserve accepted/quarantined row reconciliation.
- Keep secrets and connection values out of source code.
- Add no production writes or deployment steps.

After editing, show the exact files changed and the evidence for each change.
```

### Review: Check a proposed pipeline change

```text
Use HVE RPI review on the proposed Fabric pipeline patch.

Verify:
- Scope matches the approved plan.
- The contract and business meaning are preserved.
- Tests prove expected totals, counts, and quarantine behavior.
- A replay does not duplicate rows.
- Identity, connections, secrets, and destination are safe.
- Rollback is clear.
- No production asset is changed by the patch.

Classify each finding and require explicit human approval for ambiguous
business semantics.
```

### Generate a Fabric pipeline design

```text
Research and plan a Microsoft Fabric retail ingestion pipeline.

Inputs:
- Orders from SQL
- Inventory from an API
- Product and supplier files

Requirements:
- Bronze, Silver, and curated Lakehouse layers
- Explicit source contracts
- Parameterized connections
- Accepted and quarantined data paths
- Idempotent replay
- Quality checks for keys, nulls, freshness, counts, and totals
- Least-privilege execution identities
- Observable run and source batch IDs
- Development-only design

Compare portal-Copilot, notebook/Foundry, pipeline-agent, and
source-controlled GitHub Copilot approaches. Recommend an implementation
with trade-offs. Do not create cloud resources.
```

### Ask for an architecture diagram

```text
Create a Mermaid architecture diagram for the approved Fabric pipeline plan.
Show:
- SQL, API, and file sources
- Fabric pipeline and notebooks
- Bronze, Silver, and curated Lakehouse
- Optional Foundry diagnostic agent
- GitHub Copilot/HVE engineering workflow
- Separate human-development identity and Fabric runtime identity
- Review and promotion gates

Label AI proposals separately from deterministic data movement and human
approval. Do not imply a live integration that has not been implemented.
```

## Suggested table-talk use

Use HVE as a 5-minute section of the discussion:

1. Explain why several agents are useful: narrower roles make assumptions and
   evidence easier to review.
2. Contrast a transient portal answer with a source-controlled RPI workflow.
3. Show one sample prompt from this guide.
4. Ask which role the audience would add first: contract, quality, diagnosis,
   security, or review.
5. Emphasize that automation proposes; deterministic gates and humans approve.

## First practical exercise

After installation, start a Copilot chat, select **RPI Agent**, and run:

```text
Research the current repository's planned retail Fabric pipeline. Identify
existing requirements, contracts, testing guidance, and security constraints.
Do not change files. Tell me which evidence is confirmed and which decisions
are still open.
```

That exercise prepares a grounded plan before any Fabric code is generated.
