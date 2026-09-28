# Customers V1.2 Migration Review — 2026-09-28

## Review metadata
- Section: Customers
- Reviewed intelligence commit: `0f56b528011c9ee1ab21c47e7dc23e492bb694cc`
- Product evidence commit: `34cae67aaaa41c5967cad4b7145e67c84a8e0f34`
- Prior authoritative review: `04-review-history/CUSTOMERS_REVIEW_2026-09-25.md`
- Methodology: V1.2
- Reviewer: ChatGPT — Strategic Reviewer / Quality Gate
- Decision: **ACCEPT WITH OPEN PRODUCT ISSUES**
- Full re-audit required: No
- Retroactive queue: RR-V12-010 completes V1.2 Quality Gate

## Prior accepted truth preserved

The migration preserves the accepted distinction between:
1. private Merchant Customer relationship/profile;
2. internal country-aware Platform Customer identity;
3. cross-merchant Wossol Reputation projection;
4. per-Order acquisition evidence;
5. consent evidence foundation.

Customers remains a scoped customer-continuity and retrospective evidence system.

It is not a merchant-visible global customer graph, predictive customer intelligence system, consent-managed communication system, fraud engine, or customer-ownership network.

## Product truth delta

No material Customers-owned source change invalidates the prior audit.

The V1.2 value comes primarily from deeper connected-domain inspection:
- Order provenance and source context;
- canonical customer linkage;
- immutable per-Order acquisition observation;
- Confirmation/Tracking outcome recomputation;
- explicit Test Order population boundaries.

The migration correctly treats this as incremental evidence enrichment rather than a restart.

## Merchant job removed / reduced

The principal merchant job reduced is reconstructing repeat-customer context from isolated Orders.

Current Customers can keep together:
- merchant-scoped customer identity/context;
- own Order history;
- merchant notes/profile overrides;
- manual merchant-specific block state;
- bounded historical outcome/reputation context;
- links back to Order-owned resolution.

This reduces repeated lookup/context reconstruction.

It does not establish replacement of:
- a full CRM;
- campaign software;
- customer-support inbox;
- messaging platform;
- spreadsheet-based segmentation;
- general customer intelligence tooling.

No measured time or outcome saving is established.

## Provenance-to-outcome trace

The strongest verified connected chain is:

**Commerce / Manual / supported Messaging source → canonical Order attribution/customer facts → MerchantCustomer + country-aware PlatformCustomer link → per-Order CustomerAcquisitionOwnership evidence → Confirmation / shipment outcome → Customer evidence recomputation → merchant/customer context.**

This chain is strategically meaningful because source evidence and later outcome evidence can remain associated with the customer relationship without making the source itself the customer identity.

Canonical customer identity remains based on Workspace country plus normalized phone.

Messaging participant IDs, external account IDs, Ad IDs or checkout/session evidence do not become canonical Customer identity merely because they are connected to an Order.

## Acquisition evidence boundary

Director source verification confirms that `CustomerAcquisitionOwnership` is an immutable/idempotent Order-scoped observation.

It derives from persisted Order attribution when available, otherwise records explicit UNKNOWN.

It is not:
- legal/economic customer ownership;
- contact permission;
- consent;
- transfer rights;
- commission entitlement;
- complete campaign attribution;
- causal acquisition proof.

This distinction is correctly preserved.

## Test Order population boundary

Director verification confirms a valuable and explicit Customers-specific distinction.

Customer History uses `customerHistoricalOrderWhere`, which excludes qualifying merchant-deleted pre-confirmation Orders but does not exclude Test Orders. The history projection exposes `isTestRecord`.

Customer commercial/reputation evidence uses `customerCommercialEvidenceOrderWhere`, which explicitly adds:

`isTestRecord: false`.

Therefore:
- Test Orders remain visible as historical operational evidence;
- Test Orders do not contribute to Customers commercial/reputation projection.

This is good truth preservation: test activity is not erased, but it is separated from the commercial reputation calculation.

It must not be generalized to Analytics overall. Other Analytics populations have separately verified Test Order leakage/open policy issues.

## Outcome and intelligence depth

Customers connects historical Order/Confirmation/Delivery evidence into deterministic retrospective counts and labels.

That reaches:
**connected data → calculation → descriptive decision context.**

It does not establish:
- validated prediction of future customer behavior;
- fraud prediction;
- causal interpretation;
- personalized recommendation;
- autonomous action;
- measured intervention outcome;
- learning loop.

The merchant can use the context when deciding what to do, but the system does not yet perform validated Customer Decision Intelligence.

## Control

Current merchant control includes:
- private profile/note editing;
- manual merchant-specific block/unblock;
- scoped customer/history access;
- deliberate Order resolution through the Orders owner domain.

The block is not a global ban.

The merchant does not control:
- global identity merge/split;
- cross-merchant reputation formula;
- other merchants' histories;
- platform identity correction;
- acquisition transfer;
- consent through an established Customers workflow.

## Consent boundary

The consent helper remains a foundation only.

Director verification confirms typed append/current-state behavior and an explicit UNKNOWN state when no event exists.

The migration correctly does not convert this into communication authorization.

No established application integration proves:
- merchant/customer capture UX;
- revocation UX;
- Customer API/projection;
- Confirmation sender gating;
- Messaging sender gating;
- campaign/contact enforcement.

Possession of phone/history/acquisition evidence must not be interpreted as consent.

## Open identity/reputation contradiction

The prior material issue remains.

Canonical Platform Customer identity includes country and normalization semantics.

Current Wossol Reputation storage/read behavior remains normalized-phone keyed.

That mismatch prevents a strong claim that cross-merchant reputation is operating on the same canonical identity boundary.

The audit correctly preserves the issue rather than claiming globally correct identity resolution.

## Reputation contract drift

The current executable deterministic reputation policy remains different from the approved Final V1 semantics.

Executable truth establishes current behavior but does not silently supersede the approved contract.

The audit correctly preserves both until Product authority reconciles/version-controls the contract.

## Identity correction governance

No operational merge/split/correction and historical reconciliation workflow is established strongly enough to support a robust identity-resolution claim.

Schema lineage/foundation is not the same as merchant/platform correction capability.

This remains an important prerequisite before stronger network/identity claims.

## Cross-domain compound value

The current compound value is stronger than a standalone customer list:

**source-aware Order → stable customer relationship → historical outcome accumulation → bounded context at later merchant decisions.**

That can reduce merchant context reconstruction and preserve useful commercial history.

The potentially stronger future asset is accumulated identity + source + outcome evidence across time.

Today this is a foundation, not a demonstrated network effect or learning system.

## Claims strengthened / weakened / unchanged

**Strengthened:** customer continuity is better understood as cross-domain provenance-to-outcome continuity, not only profile/history storage.

**Strengthened:** Test activity has a defensible Customers-specific truth boundary: visible in history, excluded from commercial/reputation calculation.

**Strengthened:** per-Order acquisition observation is useful provenance infrastructure.

**Unchanged:** Wossol Reputation is deterministic retrospective evidence, not prediction.

**Unchanged:** country-aware identity vs phone-only reputation key remains unresolved.

**Unchanged:** reputation-policy contract drift remains unresolved.

**Unchanged:** consent is evidence foundation only.

**Unchanged:** no identity merge/split/correction workflow is established.

**Unchanged:** no competitive superiority/network-effect claim is supported.

## Verification assessment

Recorded verification passes:
- Customers backend specs: **37/37**;
- Customers frontend specs: **13/13**;
- backend typecheck: passed;
- frontend typecheck: passed.

Director source challenge independently verified:
- history vs commercial/reputation Test Order filters;
- country-aware Platform Customer linkage;
- acquisition-observation semantics;
- consent helper/current-state boundary;
- Order-owned customer linkage.

No production dataset, live contact provider, live identity-quality measurement, live consent enforcement, browser session or predictive-validation evidence was established.

The local fresh-fetch permission limitation does not invalidate source evidence at the reviewed Product commit.

## Open Product issues

1. Align/version Wossol Reputation identity with country-aware canonical Platform Customer identity or explicitly constrain the reputation namespace.
2. Reconcile current executable reputation labels/thresholds with the approved Final V1 contract.
3. Implement/verify identity merge/split/correction and historical reconciliation governance before stronger identity claims.
4. Establish consent capture/read/revocation/enforcement integrations before consent-managed communication claims.
5. Define privacy/minimum-evidence/explanation thresholds for cross-merchant reputation exposure.
6. Validate reputation/calibration/fairness against real outcomes before predictive language.
7. Preserve Test Order exclusion in Customers commercial/reputation evidence while keeping test history explicitly labelled.
8. Do not infer the Customers Test exclusion policy for Analytics or other projections.
9. Verify production identity/data-quality/recomputation freshness.
10. Competitive distinctiveness remains unverified.

## Claim / marketing safety

Safe current framing:

**Wossol keeps a merchant's customer history, notes and Order context together and can add bounded retrospective delivery-history context while preserving Test activity separately from Customer commercial/reputation evidence.**

A stronger system-safe interpretation is:

**Customer context can stay connected from an Order's source and customer identity through later delivery outcomes, reducing the need to reconstruct repeat-customer history manually.**

Do not claim:
- predictive customer risk;
- fraud prevention;
- globally correct customer identity;
- customer ownership;
- acquisition causality;
- consent-managed outreach;
- compliant campaigns;
- customer lifetime value;
- autonomous customer decisions;
- proven outcome improvement;
- learning intelligence;
- demonstrated network effect.

## Strategic / brand implication

Customers strengthens evidence for:
- Context Continuity;
- Reduced Merchant Work;
- Connected Commercial Truth;
- Operational Control;
- accumulating historical evidence.

Its most defensible value is continuity with boundaries: the merchant can retain useful relationship and outcome context without Wossol pretending that identity, reputation, acquisition, consent and prediction are the same thing.

That pattern supports the broader working hypothesis around connected commercial truth, but does not yet justify a customer-intelligence/network hero claim.

## Methodology impact

No methodology change required.

V1.2 successfully exposes the important distinctions between:
- history and commercial population;
- source provenance and identity;
- identity and reputation;
- reputation and prediction;
- acquisition evidence and ownership;
- consent evidence and communication authorization;
- descriptive context and Decision/Learning Intelligence.

## Retroactive impact

RR-V12-010 has completed its V1.2 Quality Gate.

The Customers-specific Test Order exclusion should remain explicit and must not be used to erase the Analytics Test Order issue.

Consent boundaries remain relevant to Confirmation, Messaging and later synthesis.

Identity/reputation key inconsistency remains relevant to any future cross-merchant intelligence/network narrative.

No previously accepted section requires correction from this migration.

## Final decision

**ACCEPT WITH OPEN PRODUCT ISSUES.**

Customers is V1.2-complete for intelligence purposes. No Codex correction or re-audit is required before proceeding to Confirmation.
