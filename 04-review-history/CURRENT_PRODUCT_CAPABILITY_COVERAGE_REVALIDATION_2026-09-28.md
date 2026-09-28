# Current Product Capability Coverage Revalidation — 2026-09-28

## Purpose

Revalidate whether the accepted V1.2 section set covers the **current Product capability universe**, not merely whether RR-V12-001 through RR-V12-021 were completed.

This check was triggered after Master Synthesis initialization.

## Current Product snapshot

- Repository: `jetshop7/wossol-platform`
- Branch: `dev/wossol-integration`
- Current GitHub HEAD checked: `fd2b03f1b51cbba7ebc841b2e0a019c0d0159b1b`
- Current frontend page routes: **117**
- Current backend controller files: **52**

RR-V12-001 through RR-V12-021 remain valid accepted migrations for the sections they cover.

## Key finding

**The 21/21 V1.2 queue is complete, but the queue itself is not sufficient evidence that every meaningful current Product capability has been separately audited.**

The prior route/backend reconciliation successfully mapped route/controller families to owner domains or cross-cutting controls, but several current capabilities were treated too generically as supporting/cross-cutting infrastructure and were not given enough Product-to-Brand intelligence treatment.

Therefore Master Synthesis should be treated as **provisional pending targeted capability coverage closure**, not discarded.

## Material capability gaps / under-audited surfaces

### 1. Merchant Settings / Account & Store Delivery Pricing

Current merchant UI/API includes:
- personal profile/display name;
- private avatar;
- Merchant business display name;
- phone / WhatsApp contact data;
- email change with session invalidation;
- password change with session invalidation;
- Email notification preference;
- Store management;
- Store-scoped delivery pricing overrides and reset behavior.

Existing V1.2 audits cover pieces of this through Stores, Notifications, Team/Auth and commerce/delivery pricing seams, but no accepted audit was found that evaluates the complete merchant Settings surface, especially:
- account/security consequences;
- delivery-pricing authority and downstream commercial effects;
- work reduction/control/provenance value;
- cross-domain boundaries.

**Required intervention:** targeted capability audit / supplement, not full project re-audit.

### 2. Merchant Global Search

Current Merchant Shell includes an active Global Search UI backed by a read-only cross-domain projection over authorized:
- Products / Variants;
- Orders;
- Customers;
- External Shipping;
- Withdrawals/Finance;
- Support Tickets;
- Team members;
- Stores.

It preserves Workspace, Store and section-access boundaries and routes directly to owner workflows.

No accepted section audit was found that explicitly analyzes Global Search as a cross-domain navigation/context-continuity capability.

This is potentially material to **Reduced Merchant Work / navigation effort / Context Continuity**.

**Required intervention:** targeted source verification and value extraction; likely supplement under Home / Merchant Portal unless evidence justifies a standalone section.

### 3. Merchant Growth Profile

Current backend exposes a versioned, audited Merchant-owned `growth-profile` contract containing merchant-declared:
- acquisition source/campaign/referral partner;
- experience level;
- business models;
- primary sales channels;
- current and target market country codes;
- approximate monthly Order band;
- main Product categories;
- team-size band.

Current source explicitly states that it performs no cohort calculation, marketing automation, inferred attribution or Analytics projection.

No accepted intelligence artifact was found covering this capability.

No current dedicated frontend page/file was identified in the targeted source check, so it may be **Partial / backend foundation** rather than a surfaced merchant workflow.

Strategically, this matters because it could later support personalization/segmentation/market guidance, but must not be upgraded into current intelligence.

**Required intervention:** targeted source verification and current/partial/future classification.

### 4. Admin System Settings

Current Admin UI/API provides Workspace-scoped, versioned, audited configuration for:
- IANA timezone;
- active working weekdays;
- opening/closing times;
- change reason and history.

The UI explicitly says this is the Workspace standard operating schedule and does not stop the platform/providers outside those hours.

No accepted section audit was found that explicitly owns this control surface.

This may be supporting operating infrastructure rather than a Brand hero capability, but it is real Product control and should be classified.

**Required intervention:** targeted source verification; assign owner section or create a small supporting-control supplement.

### 5. Workspace Payment Configuration

Current Admin API exposes Workspace payment configuration including:
- POS-card operational allowance;
- electronic-payment operational allowance;
- merchant/customer processing-charge allocation;
- versioned/audited mutation;
- Order-level effective payment-method eligibility derived from Workspace configuration plus immutable sold-line Product payment-policy snapshots.

This is materially connected to **Products → Orders → Finance / commerce policy**.

No accepted section audit was found that explicitly evaluates this current payment-policy/configuration chain.

**Required intervention:** targeted audit / supplement because this can materially affect commercial control and future payment claims.

## Capabilities requiring explicit coverage confirmation, not necessarily new audits

### Product Connection Health
Current Product endpoint combines permission-aware Shopify and Advertising mapping/health projections for a ProductStore link.

Likely owner coverage:
- Products;
- Integrations / Shopify;
- Advertising.

Need explicit source-to-audit mapping so it is not silently lost.

### Admin Merchant Management
Current Admin capability includes Merchant account/workspace/store/status/password/identity-document/fee-profile controls.

Likely cross-cutting ownership:
- Team/Auth;
- Stores/Workspace;
- Finance fee configuration;
- platform administration.

Need verification that no unique merchant-lifecycle value/claim was missed.

### Admin Employees
Current Admin employee/workspace assignment and credential lifecycle is likely covered materially by Team, but this should be explicitly confirmed against the Team audit.

### System Override
Already classified as a privileged cross-domain recovery/control surface spanning provider capability, Tracking resync and Product mapping correction.

No standalone marketing capability should be inferred; verify owner-domain coverage only.

### Data Quality / Platform Analytics
Likely correctly owned by Analytics / Decision Center; verify no unreviewed merchant-facing capability.

### External Integration / Accurate Mayar
Likely supporting provider health/integration evidence under Integrations / Tracking; verify exact owner mapping.

## Current Shopify delta after accepted V1.2 snapshot

Product HEAD advanced from the synthesis-readiness Product reference `34cae67...` to `fd2b03f...`.

The delta modifies Shopify COD configuration/session/runtime behavior, including:
- surfacing canonical Test Product classification as a read-only badge in embedded Shopify Product management;
- replacing a generic classification-change failure with bounded `CHECKOUT_RESTART_REQUIRED`;
- clearing stale checkout-session tokens on classification restart and finalized no-Upsell flows;
- related storefront/runtime tests and presentation changes.

This does not create a new section, but it is newer than the accepted Shopify/Products source snapshots.

**Required intervention:** targeted source-delta verification against Shopify COD + Products (and Integrations only if the evidence changes its conclusions). Do not require a from-scratch re-audit.

## Coverage decision

**Decision: SYNTHESIS COVERAGE HOLD — TARGETED CORRECTION/VERIFICATION REQUIRED BEFORE FINAL MASTER SYNTHESIS RELIANCE.**

This does **not** revoke the 21 accepted V1.2 section reviews.

It means:
- the existing 21 migrations remain authoritative for their domains;
- Master Synthesis V1.0 remains useful provisional work;
- capability-universe coverage must be closed for the current Product before treating synthesis as final evidence foundation.

## Smallest next intervention

Run one Codex **Current Product Capability Coverage Closure** pass that:

1. starts from current Product HEAD;
2. inventories all merchant/admin/worker/public route families, controllers and material service-only capabilities;
3. maps each to an accepted V1.2 owner artifact;
4. specifically audits/verifies the five under-covered areas above;
5. verifies the listed cross-cutting controls;
6. performs targeted Shopify source-delta verification;
7. creates only the minimum new supplement/audit artifacts required;
8. updates the route/backend/capability reconciliation;
9. does not rewrite accepted audits from scratch;
10. does not start or modify final Brand Strategy.

After Codex pushes the closure record, Director performs one final Coverage Quality Gate.
