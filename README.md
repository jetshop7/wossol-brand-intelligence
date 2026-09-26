# Wossol Brand Intelligence

This repository is the **Product / Competitive / Marketing / Brand Intelligence Source of Truth** for Wossol.

The repository is intentionally self-contained so an investigator can receive a short task such as `Audit Orders.` and recover the full operating method from committed project governance rather than conversation memory.

## Source-of-truth boundary

- `jetshop7/wossol-platform` = **Product Source of Truth**. Inspect it; do not replace current product truth with intelligence documents and do not modify it during intelligence audits.
- `jetshop7/wossol-brand-intelligence` = **Intelligence Source of Truth**. Store methodology, competitive intelligence, section audits, synthesis, and review history here.

## Mandatory agent entrypoint

For any section audit, start with:

1. `00-methodology/CODEX_OPERATING_PROTOCOL.md`
2. follow its Mandatory Startup Sequence;
3. apply the latest `00-methodology/MASTER_INSTRUCTIONS.md` completely.

The operating protocol defines **how Codex executes**.  
The master instructions define **how the intelligence audit is performed**.

A short command identifies the section. The repository supplies the process.

## Short-command contract

Examples:

- `Audit Home.`
- `Audit Orders.`
- `Continue Orders audit.`
- `Re-audit Confirmation.`

These commands do not require the user to repeat methodology, evidence rules, output structure, or completion criteria.

## Audit order

Home → Products → Inventory → Orders → Confirmation → Customers → Tracking/Delivery → Finance → Analytics/Decision Center → Market Center → Advertising → Integrations/Commerce Channels → Stores → Team → Sourcing/Network → External Shipping → Local Pickup

The order may change when dependencies justify it; record the reason.

## Repository structure

- `00-methodology/` — Codex operating protocol, audit methodology, changelog, retroactive queue
- `01-competitive/` — stable competitive reference
- `02-section-intelligence/` — canonical section audits
- `03-master-synthesis/` — evidence-backed cross-section strategic synthesis
- `04-review-history/` — authoritative Director review records and material corrections
- `05-director/` — ChatGPT Project Director governance, review protocol, decision standards, and project context

## Director entrypoint

A ChatGPT Project acting as the Wossol Intelligence Director should begin with:

1. `05-director/DIRECTOR_PROTOCOL.md`
2. `05-director/REVIEW_PROTOCOL.md`
3. `05-director/DECISION_STANDARDS.md`
4. `05-director/PROJECT_CONTEXT.md` when broader project context is needed.

The Director verifies Codex work against GitHub and Product Source evidence, writes authoritative reviews under `04-review-history/`, and controls the Quality Gate.

Codex does **not** use `05-director/` as its own operating instructions.

## Governance

Codex is the **Investigator / Extractor / Evidence Builder**.

Codex is not the final external reviewer. Completed section intelligence should be reviewable by a separate ChatGPT Project Director / Quality Controller using repository evidence.

Do not use master synthesis as a dumping ground and do not prematurely lock positioning, naming, tagline, visual identity, or final brand promise.

## Core rule

> **Do not merely catalogue what Wossol has. Discover what Wossol can legitimately own — and preserve the evidence that proves it.**
