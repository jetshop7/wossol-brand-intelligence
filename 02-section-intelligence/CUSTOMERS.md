# Customers — Section Intelligence

## 1. Audit Metadata

- **Audit date:** 2026-09-25.
- **Methodology:** `00-methodology/MASTER_INSTRUCTIONS.md` v1.1; current operating protocol.
- **Competitive reference:** `WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md` v1 (2026-09-09).
- **Intelligence source:** `jetshop7/wossol-brand-intelligence`, `main`, `b6217c0c3fea2e3b2d6e1a9c048c8ab81a6ae007`, clean and synchronized before inspection.
- **Product source:** `jetshop7/wossol-platform`, `dev/wossol-integration`, `e3912a967827bde06450d3510228e5a5ca9e78a7`; pre-existing uncommitted edits were present in three COD form extension files outside this audit's scope and were left untouched.
- **Evidence basis:** P1 code/schema, P2 tests (execution status stated below), P3 approved Customer UI and domain specifications, and P4 architecture material only when qualified. No production data or runtime was accessed.
- **Review status:** First Customers section audit; no Customers review record was present in the synchronized repository at audit time.

## 2. Audit Coverage Map

| Surface | Coverage | Evidence |
|---|---|---|
| Merchant Customer UI | list, filtering, search, profile, history, notes, block/unblock | EV-CUS-001–002 |
| Customer API and authorization | merchant scope, permission gates, profile and block routes | EV-CUS-003 |
| Profile and Orders relationship | profile upsert, history, test-order visibility, pre-confirmation deletion boundary | EV-CUS-004–005 |
| Reputation | merchant-local and Wossol aggregate derivation, labels, inputs and UI projection | EV-CUS-006–007 |
| Platform identity and acquisition | country-aware identity/link graph and immutable per-order acquisition observations | EV-CUS-008–009 |
| Consent | event ledger helper, state derivation, call-site search and missing integrations | EV-CUS-010 |
| Connected customer surfaces | order-create advisory and Confirmation read projection; separate communication surfaces | EV-CUS-011–012 |
| Contract and tests | Customer UI/domain specifications, focused backend tests, frontend/backend typechecks | EV-CUS-013–014 |

## 3. Executive Section Truth

Customers currently provides a private, merchant-scoped customer profile and Order history, editable merchant notes/profile fields, a manual merchant-specific block, bounded customer context during Order creation, and a separately labelled “Wossol Reputation” aggregate. A country-aware internal Platform Customer identity graph and immutable Order acquisition observations exist beneath those merchant surfaces. They do not expose a cross-merchant customer directory or identity-management capability to merchants.

The operational layer is real; the broader intelligence and consent loop is not. Reputation is a deterministic retrospective summary of delivery outcomes, not a validated prediction of an individual customer's future conduct. Acquisition ownership is historical attribution, not customer ownership, access control, transfer, or commission. A consent ledger helper exists, but no Customer-facing capture/mutation API, read projection, sender integration, or contact-decision enforcement was found. Confirmation's operator WhatsApp links are a separate workflow; this audit found no integration between them and the consent ledger.

Two material consistency risks remain: the P1 Platform Customer identity is country-aware while the Wossol reputation aggregate is keyed by normalized phone alone; and P1's current reputation labels/thresholds differ from the older P3 Final V1 Customer System formula. Neither is resolved by the existing tests or specifications.

## 4. Scope & Architecture Map

The private `MerchantCustomer` is scoped by Workspace, Merchant and normalized phone. It is the merchant's profile, notes, block state and own Order relationship. A separate internal `PlatformCustomer`/identity/link graph associates normalized phone identities using country and normalization version. `CustomerAcquisitionOwnership` records acquisition evidence per Order. `CustomerConsentEvent` is a scoped immutable evidence ledger. These are distinct entities and purposes; existence of a shared internal identity does not make merchant profiles, Orders, notes, ownership, or consent globally visible.

Customers owns merchant customer projections and customer-specific gates. Orders owns lifecycle decisions for an Order blocked due to customer state. Confirmation owns workforce handling. Messaging/Tracking own their communication surfaces. The consent ledger is not currently shown to authorize any of those surfaces.

## 5. Current Capability Inventory

| Capability | Status | Current behavior |
|---|---|---|
| Scoped merchant Customer list/detail | LIVE | Authenticated active Merchant membership and explicit active Workspace; profile and Orders remain merchant/workspace-scoped |
| Search and filters | LIVE | Server pagination; name/phone search; own status, Wossol reputation and blocked-state filters |
| Merchant profile and notes | LIVE | Merchant display name/region/city overrides, note, and latest Order-observed fallbacks with provenance precedence |
| Customer block/unblock | LIVE | Manual merchant-scoped gate with reason required for blocking; does not rewrite historic Orders or global reputation |
| Customer Order history | LIVE | Own linked Orders with status/date/pagination context; Test records are identified; merchant-deleted pre-confirmation Orders are excluded |
| Order-create customer context | LIVE, advisory | Bounded signal codes for own block, reputation and prior history; does not return raw history or prevent all creation paths by itself |
| Wossol Reputation | LIVE, projection contract risk | Cross-merchant delivery-outcome aggregate and categorical label; aggregate query key is normalized phone, not country-aware Platform Customer identity |
| Internal Platform Customer identity graph | LIVE, internal | Country-aware normalized phone identity and merchant link created during Order linkage; not a merchant-facing global profile/directory |
| Identity merge/split/correction and historic reconciliation | NOT IMPLEMENTED | Schema lineage groundwork only; no observed endpoint/workflow or historic backfill |
| Acquisition ownership observations | LIVE, evidence only | Append-only/versioned per-Order observation; no merchant read surface, transfer semantics or commission engine |
| Customer consent | FOUNDATION ONLY | Immutable scoped ledger helper/state derivation; no observed producer/API/UI, Customer projection, sender or enforcement integration |
| Customer campaigns, segmentation and automated outreach | NOT ESTABLISHED | No Customer-owned campaign/segment/contact-decision capability established in inspected scope |

## 6. Workflow & Lifecycle

1. An authenticated merchant actor selects an explicit active Workspace; membership and permissions are checked against the active Merchant/Workspace context.
2. A new Order links or creates the scoped `MerchantCustomer`, updates observed name/location fields, and may link it to an internal country-aware `PlatformCustomer` in the same transaction.
3. Order creation may produce an advisory customer-intelligence projection. A blocked customer is handled by the Orders-owned lifecycle gate; the Customers block action itself does not reopen or cancel historic Orders.
4. Merchants browse own Customer profiles and linked history, edit allowed profile/note fields, or manually block/unblock with audit evidence.
5. Platform reputation is recalculated from eligible historical delivery outcomes. Its current keying differs from the country-aware identity key and must not be treated as proven same-person aggregation across countries.
6. Acquisition evidence is recorded at Order linkage. Consent state can be derived from ledger events, but this audit found no workflow connecting that state to message eligibility or sending.

## 7. Value Recipient Map

| Recipient | Current value |
|---|---|
| Merchant operator | Reusable private customer context, own Order history, notes and manual future-Order block |
| Merchant owner/manager | Scoped access control, audited edits/blocking and customer-history summaries |
| Order operator | Bounded advisory signals and a separate Orders-owned resolution path for blocked Orders |
| Wossol product/operations | Internal identity and acquisition evidence foundations for possible future cross-domain intelligence |
| Customer/consumer | No direct Customer product surface or demonstrated consent-centered engagement benefit established here |

## 8. Control & Merchant Agency

The merchant controls their private profile annotations and a merchant-specific future Order block, subject to permission checks. They do not control or inspect a platform-wide customer profile, other merchants' relationship history, identity merges, acquisition transfer, or a global reputation calculation. The block is not a platform-wide ban and does not itself decide the lifecycle of already-blocked Orders. Current agency is useful operational control with categorical advisory context, not autonomous or validated customer decision intelligence.

## 9. Transparency & Trust

- “Your Customer Status” and “Wossol Reputation” are presented as distinct concepts; the former is the merchant's relationship while the latter is a cross-merchant aggregate.
- Customer list/detail queries are scoped to the authorized Merchant and Workspace; the internal Platform Customer ID and canonical raw identity are not projected as merchant profile controls.
- Block/unblock is an explicit, audited merchant action; blocking requires a reason and is not represented as changing global reputation.
- Order-create context uses bounded codes rather than exposing raw Order lists, private notes, or normalized phone values in the advisory result.
- The consent ledger's UNKNOWN state is not affirmative consent. No evidence established that Customer consent currently gates Confirmation or Messaging communication actions.
- A label or aggregate count is not proof of identity-match correctness, predictive accuracy, delivery quality, or consent to contact.

## 10. Merchant Value Extraction

The most immediate value is continuity: a merchant can recognize repeat relationships, see their own history, preserve useful private notes, and make a deliberate future-order block decision without reconstructing the relationship from separate records. The Order-create advisory can make relevant prior outcomes visible before submission. This can reduce avoidable rework, but outcome improvement or risk-prediction accuracy was not measured.

## 11. Feature Clusters

1. **Private relationship workspace:** scoped profile, own Orders, notes, provenance and merchant-controlled block.
2. **Evidence-bounded order-time context:** reputation/history signals presented as advisory rather than a raw cross-merchant record dump.
3. **Internal identity and acquisition spine:** country-aware Platform Customer links plus per-Order acquisition evidence, not merchant network access.
4. **Trust groundwork without activation:** consent event integrity exists separately from absent capture, projection and contact enforcement.

## 12. Merchant Journey / Old Way vs Wossol Way

| Stage | Common risk | Current Wossol behavior |
|---|---|---|
| Repeat purchase intake | Repeat customer context is scattered across Orders | Scoped Customer profile and linked merchant Order history |
| Pre-submit check | Relevant past outcomes are not visible at decision time | Bounded advisory history/reputation codes can inform Order entry |
| Relationship maintenance | Private context is lost or mixed with system data | Merchant note/profile override is separated from Order-observed values |
| Customer safety | One bad interaction causes uncontrolled blanket exclusion | Merchant can explicitly block future Orders in their own scope; historic Order handling remains separate |
| Network learning | Shared identity is mistaken for shared record access | Internal identity link exists, while merchant projection remains scoped |
| Customer contact | A connection or phone number is mistaken for permission | Consent evidence foundation is separate; enforcement in communication surfaces is not established |

## 13. Hidden / Non-Obvious Advantages

- Merchant relationship facts and platform identity are deliberately separate models, preventing shared identity from automatically becoming shared profile access.
- Country participates in canonical identity matching, preventing a local phone string alone from defining the internal person key.
- The acquisition record attaches to an Order and preserves historical source evidence; it is not a mutable customer-owner field.
- Customer block is a forward-looking merchant policy action; resolving a previously blocked Order stays in Orders' lifecycle boundary.
- The consent helper preserves event scope, purpose, channel and policy evidence with idempotency checks, but that foundation must not be mistaken for a working consent-based messaging product.

## 14. Data & Intelligence Assets

The system stores merchant-scoped normalized phone identity, observed and overridden customer attributes, notes, links to eligible Orders, merchant status/reputation projections, country-aware internal platform identity, acquisition ownership observations, and consent events. These are potentially valuable longitudinal assets when their identity, access, scope, provenance and lawful-use boundaries are maintained.

Current Wossol Reputation is a deterministic rules projection over historical outcomes, not a learned model or demonstrated predictor. Policy currently labels fewer than three eligible observations `LIMITED_HISTORY`; at six or more, four or more cancellations and a ratio of at least 60% yields `HIGH_RISK`; at least two and 40% yields `RISKY`; six total plus five delivered and 80% yields `RELIABLE`; three delivered and 67% yields `GOOD_HISTORY`; otherwise `LIMITED_HISTORY`. These rules communicate categorical evidence only. They have no supplied calibration study, probability, confidence interval, or measured intervention lift.

## 15. Cross-Section Compound Advantages

- **Customers × Orders:** Order history and bounded prior-outcome context improve continuity; Orders retains lifecycle authority for a blocked Order.
- **Customers × Confirmation:** authorized Customer context is projected to Confirmation in bounded form; operator contact tools remain Confirmation-owned and are not shown tied to Customer consent.
- **Customers × Tracking:** delivered/cancelled-with-shipment evidence feeds reputation; provider shipment state does not become a merchant Customer control.
- **Customers × Commerce/attribution:** trusted Order source can feed acquisition observations, but ownership evidence is neither customer ownership nor revenue attribution proof.
- **Customers × consent:** the event schema could support auditable choices, but absent integration means it currently does not establish contact eligibility.

## 16. Competitive Analysis

The competitive master treats customer records, repeat-order history and basic customer management as category capabilities. Its suggestion that a customer network reputation could be whitespace is a hypothesis, not verified competitor absence. This audit does not establish competitor feature parity or absence. Wossol's potentially distinctive combination is the separation of private merchant relationships from internal country-aware identity and cross-merchant aggregate reputation, but the identity/reputation key mismatch is a material trust constraint and no validated customer-outcome advantage has been demonstrated.

## 17. Marketing Translation

Safe present-tense territory: “Keep your customer history, notes and order context together in your workspace.” For reputation, qualify it as a Wossol delivery-history summary and avoid implying verified identity matching across regions or prediction of an individual. Do not claim customer consent management, compliant campaigns, global identity resolution, fraud prevention, future delivery guarantees, or customer lifetime value intelligence.

## 18. Surprise / Delight Potential

The satisfying moment is seeing a repeat customer's own history and a bounded outcome summary before the merchant creates another Order. Surprise is only earned if the merchant can understand what is private, what is an aggregate, how much evidence supports a label, and why the same person is being matched. Without those explanations and identity consistency, an aggregate can surprise in the wrong way.

## 19. Category Reframe

Customers is not yet a CRM or customer engagement platform. It is a scoped merchant relationship register connected to Wossol's order and delivery evidence, with internal identity/acquisition/consent foundations that have not matured into merchant-facing network intelligence or consent-driven outreach.

## 20. Brand Implications

The credible principle is “useful shared intelligence without exposing another merchant's customer relationship.” That principle is supported by distinct merchant and platform models and scoped projections, but must be matched by identity-key consistency and actual consent enforcement before extending claims into network reputation or outreach. This is a product truth, not a final brand-positioning decision.

## 21. Weaknesses, Risks & Constraints

- Reputation aggregates by normalized phone only, while canonical Platform Customer identity includes country; potential cross-country collision/contamination is source-level, with actual data/runtime impact unverified.
- Cross-merchant aggregate counts are exposed without underlying Orders; privacy risk depends on aggregation, population and context. No specific disclosure incident was established.
- Current reputation names and thresholds conflict with the older P3 Final V1 Customer System contract; labels may not mean what the approved contract says.
- Rules are not calibrated prediction; labels such as `HIGH_RISK` may invite over-reliance despite no outcome validation.
- Platform identity lineage has no operational merge/split/correction or historic reconciliation path.
- Consent is foundation-only; no evidence that it gates Confirmation's operator WhatsApp links or other communication surfaces.
- Acquisition ownership is per-Order historical evidence without a merchant surface or ownership-transfer/commission semantics.
- No production-scale, match-quality, fairness, retention, merchant adoption, or customer-outcome evidence was inspected.

## 22. Future Potential (Not Current Truth)

Potential directions include identity-key-aligned and explainable reputation, safe minimum-evidence thresholds, identity correction workflows, merchant-visible acquisition evidence where authorized, and consent capture/read/revocation workflows integrated with each contact surface. Any outreach or cross-merchant feature would require explicit product, privacy and legal review, defined consent semantics, data minimization and verification. These are proposals, not capabilities established by this audit.

## 23. Claim Safety Matrix

| Claim | Status | Boundary |
|---|---|---|
| “See your customer’s profile and linked Order history in your workspace.” | GREEN | Scoped merchant projection supported by P1 |
| “Add private notes and manually block future Orders in your merchant scope.” | GREEN | Explain that historic Orders are not rewritten |
| “View a Wossol Reputation delivery-history summary.” | YELLOW | Label is a rules-based aggregate; identity key mismatch and formula-contract drift unresolved |
| “Wossol identifies the same customer across countries.” | RED | Country-aware identity exists, but reputation uses normalized-phone-only key; matching correctness not validated |
| “Predicts who will cancel or fail delivery.” | RED | No calibration or predictive validation |
| “Consent-managed messaging or compliant marketing campaigns.” | RED | Ledger helper exists without capture/projection/sender/enforcement integration |
| “Acquisition ownership proves who owns a customer or deserves commission.” | RED | Per-Order observation only; no transfer or commission contract |

## 24. Commercial Magnitude

**FOUNDATIONAL, with a potentially differentiating evidence spine.** Customer continuity affects repeat-order entry, confirmation and delivery experience. The merchant profile and history deliver concrete workflow value now. Cross-merchant reputation and the identity/consent foundations could support more, but material commercial impact, prediction quality and adoption are not proven.

## 25. Strategic Classification

| Area | Classification |
|---|---|
| Merchant customer list, profile, notes and history | TABLE STAKES / useful operating baseline |
| Scoped block plus bounded order-time context | OPERATIONAL STRENGTH |
| Internal country-aware identity and immutable acquisition evidence | FOUNDATIONAL ASSET |
| Wossol Reputation aggregate | POTENTIAL DIFFERENTIATOR / trust and contract risks unresolved |
| Consent-based customer engagement | NOT ESTABLISHED / future whitespace |
| Validated predictive customer intelligence | NOT ESTABLISHED |

## 26. Action Register

| Priority | Action | Rationale |
|---|---|---|
| MUST FIX / CLARIFY | Align Wossol Reputation's key to canonical country-aware Platform Customer identity, or explicitly constrain the contract and queries; assess data migration and historical recomputation. | Avoid a potential same-local-number cross-country aggregate collision. |
| MUST CLARIFY | Reconcile P1 reputation labels/thresholds with `Customer System (Final V1).md`; version the policy and update the authoritative contract or implementation. | Current semantics contradict the documented V1 formula. |
| MUST MATCH | Explain what evidence the merchant sees and avoid predictive language; consider safe minimum counts and identity caveats. | Prevent low-volume aggregates and categorical labels from implying certainty. |
| MUST FIX before outreach claims | Define consent capture/read/revocation and integrate it with each messaging/Confirmation contact action before claiming consent-managed communications. | A ledger helper alone does not enforce customer choices. |
| WORTH ADOPTING | Add operational identity correction/reconciliation governance before merchant-facing global identity features. | Schema lineage alone cannot safely correct mistaken links. |
| POTENTIAL MOAT | Preserve scoped provenance and immutable source/outcome evidence while validating identity quality and merchant benefit. | Differentiation depends on trusted evidence, not merely accumulated records. |

## 27. Evidence Register

**EV-CUS-001 — Merchant Customer list UI.** **Type:** P1. **Path:** `apps/frontend/src/app/merchant/customers/page.tsx`. **Observed:** paginated search/filter list, own status and Wossol Reputation facets, block state and navigation. **Confidence:** High.

**EV-CUS-002 — Merchant Customer detail UI.** **Type:** P1. **Path:** `apps/frontend/src/app/merchant/customers/[customerId]/page.tsx`. **Observed:** scoped profile/history, editable merchant fields/note, explicit block confirmation/reason and link to separately handled blocked Orders. **Confidence:** High.

**EV-CUS-003 — Customer authorization/API.** **Type:** P1. **Paths:** `apps/backend/src/modules/customers/customer-access.service.ts`, `customers.controller.ts`, permission catalog. **Observed:** authenticated active Merchant and explicit active Workspace checks; route-level `customers.view/edit/note/block` or `orders.create` permissions; ambiguous scope fails closed. **Confidence:** High.

**EV-CUS-004 — Customer service and projections.** **Type:** P1. **Path:** `apps/backend/src/modules/customers/customers.service.ts`. **Observed:** profile/list/detail/history, observed-field precedence, search normalization, merchant block, advisory create-order projection and authorized Confirmation projection. **Confidence:** High.

**EV-CUS-005 — Merchant Customer and Order linkage.** **Type:** P1. **Paths:** `apps/backend/src/modules/customers/customers.service.ts`, `apps/backend/src/modules/orders/orders.service.ts`, `apps/backend/prisma/schema.prisma`. **Observed:** Customer is keyed by Workspace/Merchant/normalized phone; linked history is scoped; Test Orders are marked; Customer block is distinct from historic Order resolution. **Confidence:** High.

**EV-CUS-006 — Reputation policy and recalculation.** **Type:** P1. **Paths:** `apps/backend/src/modules/customers/customer-reputation-policy.ts`, `customers.service.ts`. **Observed:** deterministic categorical thresholds and separate merchant/platform projections; test/deleted-before-confirmation exclusions for eligible outcomes. **Confidence:** High for source behavior; no predictive validity claim.

**EV-CUS-007 — Reputation schema and reads.** **Type:** P1. **Paths:** `apps/backend/prisma/schema.prisma`, `customers.service.ts`. **Observed:** `PlatformCustomerReputation` is unique by normalized phone; merchant list/detail/order-time reads join using normalized phone. **Confidence:** High; actual cross-country collision incidence not verified.

**EV-CUS-008 — Platform identity.** **Type:** P1/P2. **Paths:** `apps/backend/src/modules/customers/platform-customer-identity.ts`, specs, schema; `docs/wossol-system-design/01-system-design/core-systems/PLATFORM_CUSTOMER_IDENTITY_P0_02.md`. **Observed:** identity tuple includes country, normalized value and normalization version; order linking creates internal platform links; tests cover country separation and deterministic linkage. No merge/split/correction endpoint or historic backfill found. **Confidence:** High for inspected implementation/contracts.

**EV-CUS-009 — Acquisition ownership.** **Type:** P1/P2. **Paths:** `customer-acquisition-ownership.ts`, `orders.service.ts`, `CUSTOMER_ACQUISITION_OWNERSHIP_P0_03.md`. **Observed:** append-only/versioned per-Order evidence with trust rules; no merchant read surface, ownership transfer or commission semantics. Related unit specs were included in the passing focused test run. **Confidence:** High.

**EV-CUS-010 — Consent ledger and integration search.** **Type:** P1/P2. **Paths:** `customer-consent-ledger.ts`, its specs, `CUSTOMER_CONSENT_LEDGER_P0_04.md`; repository search for helper call sites. **Observed:** scoped immutable event append/state helper and idempotency validation; helper references outside its own source/specs were not found. No Customer API/UI, projection or sender/enforcement integration established. **Confidence:** High for searched scope, not proof of absence in all deployed systems.

**EV-CUS-011 — Order-time and Confirmation customer context.** **Type:** P1. **Paths:** `customers.service.ts`, `orders/create/page.tsx`, Confirmation customer projection. **Observed:** bounded advisory customer signals and authorized aggregate/context projection; does not establish raw cross-merchant Order access. **Confidence:** High.

**EV-CUS-012 — Separate contact surfaces.** **Type:** P1. **Paths:** Confirmation worker order detail and WhatsApp phone helper; Messaging module surfaces. **Observed:** operator-facing contact paths exist outside Customers; no inspected call site connects these paths to Customer consent state. This is not a claim that no other consent control exists anywhere in the product. **Confidence:** Medium-high.

**EV-CUS-013 — Customer contracts.** **Type:** P3. **Paths:** `docs/ui/merchant/MERCHANT_CUSTOMERS_UI_SPEC.md`, `docs/wossol-system-design/01-system-design/core-systems/Customer System (Final V1).md`, P0-02/03/04 records. **Observed:** approved privacy boundary and architecture intent; older Final V1 reputation names/formula conflict with the current P1 policy and are preserved as an unresolved contract contradiction. **Confidence:** High for intent, not executable behavior.

**EV-CUS-014 — Focused tests and typechecks.** **Type:** P2. **Paths:** Customer identity, reputation policy, acquisition ownership, consent ledger, Customer service specs. **Observed:** backend command completed with 37 passing, 0 failing. Frontend and backend typechecks passed. No claim that Customer UI test specs were executed; no frontend UI test script was identified in the package manifest. **Confidence:** High for recorded local verification, not production behavior.

## 28. Contradictions & Uncertainty

1. **CONTRADICTION-CUS-001 — Country-aware identity vs phone-only Wossol Reputation.** **Source A (P1/P3):** Platform identity includes country and specs establish distinct Platform Customers for identical local numbers across countries. **Source B (P1):** Platform reputation is unique by normalized phone and reads/recalculation aggregate on that value alone. **Nature:** the reputation projection key does not match the canonical identity contract. **Working conclusion:** potential cross-country collision/contamination is a material source-level risk; no real collision, affected merchant, or production impact was established. **Required resolution:** align the key and read/recompute paths or formally constrain the reputation identity domain; assess migration/history and add cross-country projection tests.
2. **CONTRADICTION-CUS-002 — P3 Final V1 label/formula vs current P1 policy.** **Source A (P3):** Final V1 defines older labels and different thresholds (including Loyal/Good/Risky/Very Risky semantics). **Source B (P1):** current enum/policy uses `RELIABLE`, `GOOD_HISTORY`, `LIMITED_HISTORY`, `RISKY`, `HIGH_RISK` with the thresholds recorded in Section 14. **Nature:** names and thresholds are not equivalent. **Working conclusion:** runtime source supports the P1 behavior; the approved customer-system contract remains unreconciled, so semantic authority is uncertain. **Required resolution:** product authority decides intended policy, then version/align P1 and P3 and validate merchant-facing language.
3. **CONTRADICTION-CUS-003 — Consent foundation vs communication workflows.** **Source A (P1/P3):** consent event helper and scoped ledger contract exist. **Source B (P1):** Confirmation operator WhatsApp actions and separate Messaging surfaces exist; no consent-ledger call sites were found in inspected scope. **Nature:** stored consent evidence does not establish gating/enforcement at contact. **Working conclusion:** do not claim consent-managed outreach; whether separate operational policies apply is unverified. **Required resolution:** map contact paths and define consent enforcement before any related product/compliance claim.
4. **UNCERTAINTY-CUS-004 — Aggregate privacy and reliability.** Cross-merchant delivery evidence appears in categorical aggregate projections. Source inspection does not establish population thresholds, runtime match quality, fairness, or disclosure incidents. No predictive validity is claimed.

## 29. Open Questions

1. Will Wossol Reputation use canonical Platform Customer identity (including country) or an explicitly constrained phone namespace, and how will historic aggregate records be corrected?
2. Which reputation policy is authoritative: current P1 thresholds or P3 Final V1? Who approves the change and its user-facing meaning?
3. What minimum evidence, explanation and privacy threshold should apply before a cross-merchant aggregate is shown?
4. Which Confirmation/Messaging contact actions must read consent, and what are the capture, revocation, retention and failure-safe semantics?
5. What identity correction and historical reconciliation process is required before any merchant-facing platform identity capability?
6. Are reputation labels associated with measured merchant outcomes or merely deterministic descriptions of past delivery evidence?

## 30. Methodology Learnings

No reusable methodology change identified. Existing evidence hierarchy and contradiction handling were sufficient to distinguish operational customer context from network access, rules-based reputation from prediction, and consent evidence storage from contact authorization.

## 31. Retroactive Review Impact

No prior section audit or methodology change was identified as requiring retroactive review. The unresolved identity/reputation and consent integration risks should be considered by reviewers of related Customers, Orders, Confirmation and Messaging claims, but do not by themselves establish a retroactive source correction in those records.

## 32. Canonical Takeaway

Wossol Customers is currently a scoped merchant relationship register connected to Orders, plus a cross-merchant delivery-history aggregate and internal identity/acquisition/consent foundations. Its strongest defensible value is continuity with controlled boundaries. The aggregate reputation's country-insensitive key and conflict with the approved Final V1 formula must be resolved before stronger network-intelligence claims; consent storage must not be presented as consent-managed communication.
