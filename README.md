# Wossol Brand Intelligence

This repository is the Product / Competitive / Marketing / Brand Intelligence Source of Truth for Wossol.

## Source-of-truth boundary

- `jetshop7/wossol-platform` = Product Source of Truth. Audit it; do not replace current product truth with intelligence documents.
- `jetshop7/wossol-brand-intelligence` = Intelligence Source of Truth. Store methodology, competitive intelligence, section audits, synthesis, and review history here.

## Operating sequence

1. Read `00-methodology/MASTER_INSTRUCTIONS.md`.
2. Read `00-methodology/METHODOLOGY_CHANGELOG.md`.
3. Read `01-competitive/WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md`.
4. Audit the current Wossol product repository and record branch, commit SHA, date, and local-change status.
5. Write section intelligence to `02-section-intelligence/`.
6. Update synthesis only when evidence is mature enough.
7. Record methodology changes and retroactive impacts instead of silently changing prior conclusions.

## Audit order

Home -> Products -> Inventory -> Orders -> Confirmation -> Customers -> Tracking/Delivery -> Finance -> Analytics/Decision Center -> Market Center -> Advertising -> Integrations/Commerce Channels -> Stores -> Team -> Sourcing/Network

## Repository structure

- `00-methodology/` - audit operating standard, changelog, retroactive queue
- `01-competitive/` - stable competitive reference
- `02-section-intelligence/` - canonical section audits
- `03-master-synthesis/` - cross-section strategic synthesis
- `04-review-history/` - review records and material corrections

## Core rule

Do not merely catalogue what Wossol has. Discover what Wossol can legitimately own - and preserve the evidence that proves it.
