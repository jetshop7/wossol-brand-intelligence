# Customers Review — 2026-09-25

## Review metadata
- Section: Customers
- Reviewed intelligence commit: `3ae088e9e615da10c1a0f5bf8ba81d80a7470414`
- Product evidence commit: `e3912a967827bde06450d3510228e5a5ca9e78a7`
- Reviewer: ChatGPT — Strategic Reviewer / Quality Gate
- Decision: **ACCEPT WITH OPEN PRODUCT ISSUES**
- Full re-audit required: No

## What passes

The audit correctly distinguishes three materially different layers: the private Merchant Customer relationship/profile, the internal country-aware Platform Customer identity graph, and the cross-merchant aggregate Wossol Reputation projection. It does not convert internal identity infrastructure into a merchant-visible global customer graph.

The strongest strategic conclusion is appropriately bounded: current value is customer continuity and scoped operational context, while network intelligence, predictive customer intelligence, consent-managed outreach, and customer ownership/commission semantics are not established.

Targeted Product verification confirms that the canonical Platform Customer phone identity includes country, normalization version, and normalized value, while the audit records the current reputation projection/read key as normalized-phone-only. The resulting identity/reputation mismatch is correctly treated as a material Product trust/contract risk rather than proof of actual production collision.

The current P1 reputation policy is deterministic and materially differs from the Final V1 contract's label/threshold language. The audit correctly preserves both rather than using P1 to silently supersede the approved contract.

The consent ledger is correctly classified as evidence foundation only. Its append/read helper establishes scoped immutable consent evidence and an UNKNOWN state when no event exists; it does not by itself establish capture UX, sender gating, communication authorization, or compliance. The audit appropriately avoids claiming that absence of inspected call sites proves absence across every deployed system.

Evidence and verification discipline pass: focused test execution, typechecks, source inspection, and production limitations are separated rather than blended.

## Product evidence challenge

### Identity and reputation
P1 canonical identity explicitly requires Workspace country plus normalized phone and normalization version. The Customer Final V1 clarification also recognizes the later Platform Customer identity graph while retaining a separate Platform Reputation projection. The audit correctly identifies that the current reputation-key behavior must be reconciled with that country-aware identity model before stronger cross-merchant identity/reputation claims are safe.

### Reputation contract
The Final V1 document contains historical status semantics plus later clarification, while P1 currently uses `LIMITED_HISTORY`, `GOOD_HISTORY`, `RELIABLE`, `RISKY`, and `HIGH_RISK` with concrete thresholds. The audit correctly treats the mismatch as unresolved semantic authority rather than describing current thresholds as the approved Final V1 contract.

### Consent
P1 consent code is a ledger/state primitive. The approved clarification explicitly says it is not operational communication history or Merchant/User permission. The audit's conclusion that consent storage must not be marketed as consent-managed communication is therefore sound.

## Open product issues retained

1. **Country-aware identity vs phone-only reputation key.** Product authority must align reputation with canonical identity or explicitly constrain/version the reputation identity domain, including migration/recomputation implications.
2. **Reputation policy contract drift.** Product authority must reconcile/version P1 labels and thresholds against the approved Final V1 semantics.
3. **Consent enforcement gap.** Consent evidence exists, but contact-path capture/read/revocation/gating behavior is not established for Confirmation/Messaging. No compliance or consent-managed-outreach claim is safe from this evidence alone.
4. **Identity correction governance.** No operational merge/split/correction and historical reconciliation workflow is established for the internal identity graph.

These are Product issues and do not invalidate the Customers intelligence audit.

## Claim / strategic safety

Safe present territory is merchant-scoped customer continuity, own Order history/context, notes, manual merchant-specific blocking, and a qualified retrospective Wossol Reputation summary.

Do not claim predictive customer risk, globally correct identity resolution, fraud prevention, consent-managed messaging, compliant campaigns, customer ownership, commission entitlement, or proven outcome improvement.

## Methodology impact

No methodology change is required.

## Retroactive impact

No prior accepted section requires correction. The consent-enforcement distinction should be carried into later Tracking/Delivery, Advertising, Integrations/Commerce Channels, and any Messaging-related synthesis. The identity/reputation issue should remain visible wherever cross-merchant intelligence is discussed.

## Acceptance

**Customers passes the Director Quality Gate and is accepted with the open Product issues above.**

No Codex correction or re-audit is required.

The next section in the canonical sequence is **Tracking/Delivery**.
