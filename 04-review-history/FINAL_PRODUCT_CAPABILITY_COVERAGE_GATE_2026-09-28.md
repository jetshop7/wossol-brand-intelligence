# Final Product Capability Coverage Gate — 2026-09-28

## Review metadata
- Scope: current Product capability coverage beyond the 21 accepted V1.2 migration sections
- Product source: `jetshop7/wossol-platform`, branch `dev/wossol-integration`
- Product HEAD checked: `fd2b03f1b51cbba7ebc841b2e0a019c0d0159b1b`
- Prior readiness basis: `04-review-history/SYNTHESIS_READINESS_GATE_2026-09-28.md`
- Reviewer: ChatGPT — Strategic Reviewer / Project Director
- Decision: **NEEDS TARGETED SOURCE VERIFICATION**
- Full re-audit of the existing 21 sections required: **No**
- Master Synthesis status: **PROVISIONAL pending coverage closure**

## Why this gate was reopened

RR-V12-001 through RR-V12-021 are genuinely accepted V1.2 migrations for the sections listed in the queue.

However, the prior coverage reconciliation was route/controller-family driven. A deeper reverse inventory of the current Product tree found material capabilities implemented:
- inside shared Merchant Shell components;
- inside broad Merchant Portal / Settings controllers;
- as backend-only evidence foundations;
- and as cross-domain configuration services.

Those capabilities are not necessarily represented as independent route/controller families and therefore could be missed even when every route/controller family is mapped.

This means:

**21/21 accepted sections ≠ proof that every material Product capability has been intelligence-audited.**

## Current Product delta after prior readiness

Prior readiness used Product `34cae67aaaa41c5967cad4b7145e67c84a8e0f34`.

Current GitHub Product HEAD is `fd2b03f1b51cbba7ebc841b2e0a019c0d0159b1b`, one commit later.

The delta is Shopify COD-only:
- checkout-session restart contract/testing;
- embedded COD Product Test-classification projection/badge;
- session-token cleanup/restart behavior;
- generated/runtime storefront consistency updates.

This does not establish a new Shopify capability family, but current-head Shopify truth is newer than the accepted V1.2 Shopify review and requires a narrow source-delta verification before final coverage closure.

## Confirmed material coverage gaps / under-extracted capabilities

### GAP-01 — Merchant Global Search

Current Product evidence:
- live `MerchantGlobalSearch` frontend in Merchant Shell;
- live `GET /merchant/search`;
- searches authorized Products/Variants, Orders, Customers, External Shipments, exact withdrawal references, Support tickets, Team and Stores;
- enforces Workspace, Store and section-access boundaries server-side;
- read-only, no provider calls, no persistence/business-state calculations;
- deterministic bounded ranking and deep links into owner workflows.

Current Intelligence search found no Global Search coverage.

Why material:
- directly reduces navigation/search/reconstruction effort across domains;
- is a cross-domain Merchant Shell capability;
- has important permission/scope semantics;
- provides Brand Evidence for reduced merchant work/context continuity;
- should not be silently absorbed into Home because it is independently interactive and cross-domain.

Required action:
**targeted V1.2 capability audit**, not a re-audit of all owner sections.

Recommended artifact:
`02-section-intelligence/MERCHANT_GLOBAL_SEARCH.md`

---

### GAP-02 — Merchant Growth Profile / acquisition-cohort foundation

Current Product evidence:
- live authenticated `GET/PATCH /merchant/growth-profile` endpoints;
- Merchant Owner/Admin can record Merchant-declared acquisition source/campaign/referral partner, experience, business models, sales channels, current/target markets, approximate monthly-order band, main Product categories and team-size band;
- versioned immutable history with supersession and AuditEvent evidence;
- no inferred behavior, cohort calculation, marketing automation or analytics projection;
- no approved frontend surface found in the current tree.

Current Intelligence search found no Growth Profile coverage.

Why material:
- strategically important current data foundation for future merchant segmentation/cohort/personalization;
- distinguishes declared merchant context from inferred Merchant Intelligence;
- can compound with Orders/Analytics/Market Center later;
- highly relevant to future brand/intelligence territory even though no merchant UI is currently established.

Required action:
**targeted foundation audit with strict current-vs-future split.**

Recommended artifact:
`02-section-intelligence/MERCHANT_GROWTH_PROFILE.md`

---

### GAP-03 — Merchant Settings / Delivery Pricing

Current Product evidence:
`/merchant/settings` is not only Store management. Current UI includes:
- Profile;
- Business;
- Account/contact;
- Security/password;
- Notifications preference;
- Stores;
- Store-scoped Delivery Pricing.

Delivery Pricing is a live merchant control with:
- concrete Store scope;
- destination/provider-zone-derived eligible rows;
- authoritative platform/provider fee evidence;
- merchant customer-facing Store price overrides;
- reset to inherited pricing;
- Product-level delivery-price overrides in the Delivery Pricing subsystem;
- Orders/Confirmation use persisted pricing snapshots rather than silently reading current Settings for historical Orders.

Stores audit references Delivery Pricing as a separate domain owner but does not audit its merchant-control behavior. Notifications covers the email preference boundary, and Stores covers Store lifecycle, but no accepted section fully owns the complete Settings/Delivery Pricing surface.

Why material:
- real merchant commercial-control capability;
- affects checkout/customer pricing and Order evidence;
- reduces external/manual pricing configuration;
- directly supports Operational Control / Merchant Agency;
- should not be reduced to a minor Store setting.

Required action:
**new incremental V1.2 section audit** of Merchant Settings with Delivery Pricing as the material commercial subdomain, while reusing accepted Stores/Notifications/Product/Orders evidence where appropriate.

Recommended artifact:
`02-section-intelligence/MERCHANT_SETTINGS_DELIVERY_PRICING.md`

---

### GAP-04 — Canonical Geography / Order geography provenance

Current Product evidence:
- `CanonicalGeographyService` is invoked from canonical Order creation;
- immutable/versioned `OrderGeographyInterpretation` evidence can retain raw region/city/zone and provider destination references plus canonical matches;
- deterministic provider-ID or exact-alias matching only;
- explicit MATCHED/PARTIAL/UNMATCHED semantics;
- no fuzzy inference, geocoding or AI matching;
- missing undeployed geography tables are designed not to reject an otherwise valid Order;
- current reference catalogue/mappings are intentionally not seeded;
- no current geography UI or Analytics redesign.

Orders audit acknowledges “Delivery Pricing/Geography” as an owner boundary but does not extract this provenance system.

Why material:
- important provenance foundation for future geography-safe Analytics/Market Intelligence;
- prevents provider/display-name drift from becoming analytical identity;
- provides explicit unknown/partial evidence rather than fabricated confidence;
- materially relevant to Connected Commercial Truth and future intelligence.

Required action:
**targeted foundation audit**, likely cross-linked to Orders / Analytics / Market Center rather than promoted as current user-facing intelligence.

Recommended artifact:
`02-section-intelligence/CANONICAL_GEOGRAPHY_ORDER_PROVENANCE.md`

---

## Under-extracted but already partially covered — targeted verification, not new full section

### GAP-05 — Workspace Payment Configuration / Product Payment Policy downstream contract

This is **not a complete omission**.

Accepted Products already records:
- mandatory COD;
- paired optional electronic methods;
- versioned payment policy;
- payment-policy snapshots.

Accepted Confirmation records payment eligibility/selection.

However, the current Product also contains a distinct Workspace-level admin configuration that:
- gates POS Card / E-payment operational eligibility;
- stores merchant/customer processing-fee allocation;
- versions/audits changes;
- resolves Order eligibility against immutable sold-line snapshots;
- is consumed by merchant pre-confirmed Orders and Confirmation;
- does not itself activate a payment provider, create payment records or prove electronic payment execution.

Required action:
**targeted source verification and supplements** to Products / Orders / Confirmation if current accepted artifacts do not preserve the complete Workspace-gate + sold-line-snapshot authority boundary.

Do not create a standalone payments marketing claim from this foundation.

---

### GAP-06 — Current Shopify COD HEAD delta

Current Product HEAD `fd2b03f...` is newer than the accepted Shopify V1.2 evidence.

Delta is bounded to:
- fail-closed checkout restart semantics when Product Test classification changes;
- explicit `CHECKOUT_RESTART_REQUIRED` response handling;
- clearing stale persisted session token;
- embedded Product read-only Test badge/projection;
- related storefront/runtime tests.

Required action:
**targeted source-delta verification** against the accepted Shopify COD supplement. No full Shopify re-audit is justified unless the delta reveals a broader contract change.

---

## Capabilities currently classified as cross-cutting infrastructure rather than missing standalone sections

The deeper tree inventory also contains:
- Auth/session;
- permissions/access scope/roles;
- Workspace/member scope;
- Audit;
- Domain Events;
- secure credentials;
- outcome reasons;
- Product connection health;
- Data Quality;
- system override;
- system settings;
- provider sync coordination;
- admin employee/merchant/fee support.

These should remain mapped to owner-domain audits or infrastructure unless targeted verification reveals an independent merchant/strategic capability not already represented.

Do not create audits merely because a module exists.

## Coverage consequence

The earlier statement:

**Product-surface coverage: READY FOR SYNTHESIS**

is now superseded.

Current state:

**Route/controller-family coverage: RECONCILED.**

**Material capability coverage: NOT YET CLOSED.**

**Master Synthesis V1.0: useful but PROVISIONAL.**

The existing Master Synthesis should be preserved, not discarded. After the targeted gaps above are audited/reviewed, reconcile only the affected Product Advantage / Marketing Asset / Brand Strategic Evidence conclusions.

## Required sequence

1. Merchant Settings / Delivery Pricing — current merchant-facing commercial control.
2. Merchant Global Search — current cross-domain friction reduction.
3. Merchant Growth Profile — current backend evidence foundation / future cohort value.
4. Canonical Geography / Order Provenance — current backend provenance foundation.
5. Payment Configuration targeted verification/supplement.
6. Shopify current-HEAD targeted delta verification.
7. Final module/service coverage reconciliation against all accepted artifacts.
8. Re-run Synthesis Readiness Gate.
9. Patch Master Synthesis only where the newly accepted evidence changes it.

## Final decision

**NEEDS TARGETED SOURCE VERIFICATION.**

Do not restart the 21 accepted V1.2 migrations.

Do not proceed to final positioning/brand lock.

Use the smallest targeted audits necessary to close the newly discovered capability gaps.
