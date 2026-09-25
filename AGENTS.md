# Wossol Brand Intelligence — Codex Agent Instructions

This repository is the **Intelligence Source of Truth** for the Wossol Product → Brand Intelligence Extraction System.

## Mandatory entrypoint

For every section audit, re-audit, continuation, reviewer-correction, or related intelligence task:

1. **Synchronize this Intelligence repository from its configured GitHub remote before interpreting the task.** GitHub is the shared Intelligence Source of Truth. If the working tree is clean, fetch and fast-forward the current branch from its upstream (normally `origin/main`). If local intelligence changes exist, preserve them safely and do not overwrite, reset, or discard them; reconcile or report the blocker before proceeding.
2. Read `00-methodology/CODEX_OPERATING_PROTOCOL.md` first.
3. Follow its Mandatory Startup Sequence completely.
4. Apply the latest `00-methodology/MASTER_INSTRUCTIONS.md`.
5. Treat repository instructions as the durable operating memory; do not rely on conversation memory to reconstruct the process.

## Repository boundary

- `jetshop7/wossol-platform` = **Product Source of Truth**.
- `jetshop7/wossol-brand-intelligence` = **Intelligence Source of Truth**.

During Product → Brand Intelligence audits:

- Inspect/read `wossol-platform` deeply enough to establish product truth.
- **Do not modify `wossol-platform`.**
- Write audit, methodology, competitive-intelligence, synthesis, and review artifacts only to `wossol-brand-intelligence`, according to the operating protocol.
- GitHub is the **shared canonical Intelligence Source of Truth**; the local clone is a working copy/cache, not an independent authority.
- Completed audit/review-correction work must be committed and pushed to GitHub when authentication/permissions are available.
- Do not copy application code into this repository.

## Short-command contract

A short instruction such as:

`Audit Orders.`

is sufficient.

It means: execute the complete current Product → Brand Intelligence audit workflow for that section as defined by `00-methodology/CODEX_OPERATING_PROTOCOL.md` and the latest methodology, continue until the applicable Definition of Done is satisfied or blockers are explicitly documented, commit and push the intelligence-repository changes to the configured GitHub remote when access is available, and return the standard handoff report.

The user should not need to repeat methodology, evidence rules, file destinations, audit stages, or completion criteria.

## Governance

Codex acts as the **Investigator / Extractor / Evidence Builder**.

Codex is not the final external reviewer.

Keep findings evidence-backed, traceable, challengeable, and explicit about uncertainty.

Never convert:
- documentation intent into current product truth;
- visibility into control;
- stored data into intelligence;
- analytics into decision intelligence;
- AI presence into meaningful intelligence;
- competitor silence into competitor absence;
- future architecture into a current marketing claim.

Do not prematurely decide final positioning, naming, tagline, logo, colors, visual identity, or brand promise.

## Source-state discipline

For each audit, record the actual product source state required by the operating protocol, including repository, branch, commit SHA, date, and relevant local/uncommitted-state information when available.

If the product source cannot be accessed or its state cannot be verified, do not invent it. Record the blocker and its consequences.

## Methodology evolution

Methodology changes are allowed only through the controlled evolution process defined in `MASTER_INSTRUCTIONS.md` and `CODEX_OPERATING_PROTOCOL.md`.

Never silently alter the audit methodology.

When a valid methodology change affects previous audits, update the changelog and retroactive-review queue as required.

## Final rule

A task prompt tells you **what to audit**.

This repository tells you **how to audit it**.

> **Do not merely catalogue what Wossol has. Discover what Wossol can legitimately own — and preserve the evidence that proves it.**
