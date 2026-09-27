# Local Pickup — Section Intelligence

## 1. Audit Metadata

- **Audit date:** 2026-09-27 (incremental V1.2 migration re-audit; retains the prior accepted review and open product issues).
- **Methodology:** `00-methodology/MASTER_INSTRUCTIONS.md` v1.2; `CODEX_OPERATING_PROTOCOL.md` v1.1.
- **Competitive reference:** `01-competitive/WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md` v1.
- **Intelligence source:** `jetshop7/wossol-brand-intelligence`, `main`, synchronized clean to `cadfb6170ff1983acd3ec5ff206403940a3d6e07` before task interpretation.
- **Product source:** `jetshop7/wossol-platform`, branch `dev/wossol-integration`, commit `23fd26572fb82ff86b74539e40eb0e1181bb07f3`; tracking ref reported equal at inspection (Product remote not freshly fetched). One uncommitted change was present in `apps/backend/src/modules/orders/orders-messaging-attribution.postgres.integration.spec.ts`; it is unrelated, was not read as evidence, and was not modified. Relevant Local Pickup UI/API/service/schema/design files had no committed changes between Product commits `de58ec2e4a89a4bc9b751ea669b053e63e8d31a8` and this HEAD; intervening committed changes were Analytics/Orders/integration documentation only.
- **Evidence limitation:** local source and automated tests establish implemented code paths, not deployed configuration, durable production operations, actual physical collection, provider data quality, or realized economics.

## 2. Audit Coverage Map

| Surface | State | Coverage | Evidence |
|---|---|---|---|
| Merchant request UX | INSPECTED | options, quote, create, list, detail, edit/cancel bounds, labels | EV-LP-001–002 |
| APIs, permissions and scope | INSPECTED | merchant/admin routes, Workspace/Merchant/Store/Variant boundaries | EV-LP-002–003 |
| Operational lifecycle | INSPECTED | create, manual pickup request, evidence-led receipt, cancellation, alerts | EV-LP-004–005 |
| Inventory evidence boundary | INSPECTED | movement matching, expected-vs-received reconciliation, scheduler | EV-LP-006 |
| Finance effects | INSPECTED | fee and optional goods-value debit gates and ownership | EV-LP-007 |
| Persistence and data model | INSPECTED | lifecycle enum, request/carton/expected/receiving/timeline/alert schema | EV-LP-008 |
| Specs/tests | PARTIALLY INSPECTED | focused service, reconciliation, permission, UI source specs; local focused run | EV-LP-009 |
| Product intent/code consistency | INSPECTED | current system design, merchant UI spec, execution-control notes vs executable flow | EV-LP-010 |
| Competition and production | PARTIALLY INSPECTED | stable competitive baseline and repository evidence only; no live provider or competitor operation validation | §16, §24 |
| V1.2 value synthesis and fresh verification | INSPECTED | job/friction/context/provenance/economic/decision chain, connected domains, focused current backend/UI regression tests | EV-LP-011–014 |

## 3. Executive Section Truth

Local Pickup is an implemented, Store-scoped inbound declaration and follow-up workflow for already-selected Merchant Products/Variants. Merchants declare carton contents, pickup contact/location, and optionally whole-pickup goods value; Wossol operations manually records that collection was requested from a provider. The code does not contact a courier or supplier. Inventory's persisted positive provider movement is the only observed completion trigger: movement references must match the Local Pickup code and expected Variants must all have receiving lines. A short/over quantity is retained and alerted, but does not prevent completion once each expected Variant has evidence. Finance posts the snapshotted service fee (if enabled) and optional goods-purchase debit in that completion transaction.

This is useful inbound operational structure and evidence linkage, not sourcing/procurement, supplier management, provider dispatch, warehouse receiving, or actual stock mutation. Two design statements materially exceed current code: the design's `by_courier` / `supplier_direct_delivery` modes have no persisted mode or branching in the model, and its allowance for an operator-verified `inventory_updated` transition has no matching route—the implementation requires provider-movement evidence. Those statements remain intent/code contradictions; do not resolve them as established “by design” behavior. The implementation also does not prove a human physically collected the goods or that an Inventory movement was caused by this request beyond its matching reference and Variant.

## 4. Scope & Architecture Map

Local Pickup owns the request, pickup code, contact/location snapshot, cartons, declared Variant quantities, expected inbound rows, labels/version history, pickup-requested event, receiving-line projection, timeline and internal alerts. Merchant Products/Variants and Store authorization provide identity and scope; canonical delivery destinations and Fee Profile rules provide the quote. An internal operator records manual provider coordination. Inventory owns provider movement evidence and actual stock; Local Pickup consumes positive movements whose shipment/manifest reference equals its code and whose Variant is expected. Finance owns ledger posting. This is separate from External Shipping's international AIR/SEA shipment, proof, warehouse receipt and General Pickup workflow, and from customer-facing Orders/Tracking delivery.

## 5. Current Capability Inventory

| Capability | Status | Current behavior |
|---|---|---|
| Merchant create options | LIVE | Requires a concrete authorized Store; projects active canonical delivery destinations, eligible active Store-linked Variants with `accurate_mayar` mapping, and active Fee Profile assignment state. |
| Quote/create | LIVE | Quotes destination override or governorate tariff; snapshots fee/currency/charging choice and optional goods value. Create immediately creates `CREATED`; no draft. |
| Cartons and labels | LIVE | Numbered cartons with Product/Variant/positive whole quantity; A4/thermal labels; Created edits can replace cartons and version labels. |
| Merchant lifecycle controls | LIVE | Created-only edit/cancel; merchant-safe detail exposes declared/received/difference and sanitized timeline. |
| Provider request | MANUAL OPERATION | Authorized operator marks pickup requested; no courier/supplier API dispatch was found. |
| Receiving | LIVE, evidence-gated | Positive Inventory provider movements matched by pickup code and expected Variant populate receiving lines; all expected Variants must have evidence to complete. |
| Difference handling | LIVE | Exact quantities complete cleanly; short/over quantities are retained, mismatch-alerted, then status still completes. No evidence means no completion. |
| Delay alert | LIVE | Configurable threshold, default 24 hours after pickup request, with deduplication. |
| Finance | LIVE CODE PATH | On completion, optional enabled service charge and separately optional whole-pickup goods-purchase debit; transaction-bound, Finance-owned methods. |
| Pickup modes / supplier workflow | NOT FOUND IN P1 | No mode field, supplier identity, supplier choice, purchase order or mode-specific behavior in inspected model/service/controller. |
| Stock mutation / physical verification | NOT OWNED / NOT ESTABLISHED | Local Pickup neither mutates stock nor verifies physical collection; provider movement remains the accepted evidence source. |

## 6. Workflow & Lifecycle

1. Merchant chooses a concrete authorized Store, canonical pickup destination, contact/phone, and Products/Variants in one or more cartons. Variants must belong to the Merchant/Workspace and Store and have a Workspace `accurate_mayar` mapping. A destination/Fee Profile quote is recalculated server-side.
2. Creation snapshots the declared request, expected inbound quantities, quote and charging state, optional goods-purchase value, labels, merchant-safe timeline and audit evidence. The four persisted states are `CREATED`, `IN_PROGRESS`, `INVENTORY_UPDATED`, and `CANCELLED`.
3. While `CREATED`, the Merchant can edit or cancel; cancel deactivates expected rows. An authorized internal operator's “Mark Pickup Requested” action records manual provider coordination and transitions to `IN_PROGRESS`; it does not call a provider.
4. Inventory persists provider movement evidence then emits a best-effort process-local signal. Local Pickup reconciles promptly and on a periodic fallback. It looks for positive movement deltas with provider shipment or warehouse manifest code exactly equal to pickup code, then aggregates matching expected Variants into receiving lines.
5. Completion waits until each expected Variant appears in receiving lines. Quantity mismatch creates an internal alert but the evidence quantity is preserved and the pickup still becomes `INVENTORY_UPDATED`; an absent Variant leaves it `IN_PROGRESS`. Expected rows deactivate on completion.
6. In the same serializable completion transaction, Finance posts the configured pickup fee and optional goods-value charge, timeline and audit evidence. Delay alerts are evaluated independently. Alert resolution requires a nonempty handling note.

## 7. Value Recipient Map

| Recipient | Current value |
|---|---|
| Merchant / stock operator | Structured local inbound declaration, printable carton identity, status and declared-vs-received quantity visibility. |
| Wossol operations | Store-scoped work queue, manual request capture, delay/mismatch alerts and auditable resolution. |
| Inventory | Expected inbound tied to catalog Variants and later provider movement, without Local Pickup owning stock. |
| Finance | Event-gated, source-linked fee/goods debit rather than charging at request time. |
| Supplier / provider | No direct portal, dispatch request, supplier record or service-level control established. |
| Customer | No direct customer order tracking or last-mile delivery value established. |

## 8. Control & Merchant Agency

The Merchant chooses the Store, destination/contact and declared Products/Variants/quantities; before pickup request, they may correct or cancel the declaration. After transition to `IN_PROGRESS`, edits/cancellation are rejected. Wossol operators control the manual “pickup requested” record and alert resolution, but the inspected interface does not provide a human override to inventory completion. Inventory/provider integration controls accepted movement evidence, and Finance controls ledger effects. This creates a narrow controlled lifecycle, not self-service provider dispatch or source selection.

## 9. Transparency & Trust

Strengths include scoped authorization, canonical location/fee snapshots, merchant-safe timeline wording, no internal alert/provider error leakage in Merchant projection, current label versions, declared/received/difference rows, transaction-bound completion, audit records, idempotent timeline key, and deduplicated alerts. The displayed status means matching provider movement was accepted—not independent proof of physical collection. Reference equality and Variant identity bind evidence operationally but cannot rule out provider reference reuse, erroneous upstream data, or unrelated movement carrying that reference. When mismatch exists, status completion and Finance effects proceed; users must not equate `INVENTORY_UPDATED` with exact quantity fulfillment.

## 10. Merchant Value Extraction

**Merchant job removed/reduced:** Local Pickup structures the inbound declaration and later comparison for already-selected Products/Variants: authorized Store, destination/contact, carton contents, expected quantities, labels, and provider-evidenced receiving differences. It can reduce ambiguity and ad hoc matching for this narrow receipt workflow. It does not remove product/supplier discovery, ordering, collection coordination, physical handling, or provider follow-up. No merchant baseline or measured reduction in time, shortage, loss, error, dispute, or cost exists.

**Tool/process consolidation and friction:** the Wossol record groups the declaration, labels, status, timeline, exception and evidence-linked receipt. This is partial information consolidation, not replacement of the provider's system or an external purchasing workflow. Wossol operations still manually records “Pickup Requested”; the service writes an actor/time/timeline assertion that the provider was contacted manually, but makes no provider API call and captures no contact-attempt details or provider acceptance/outcome evidence. A plausible old alternative is calls/messages plus handwritten labels or a spreadsheet, but this was not validated with users. Do not quantify steps saved.

**Context continuity and provenance:** Product/Variant + Merchant/Workspace/Store context is retained from expected items through matching Inventory movement references and the Local Pickup receiving projection; relevant Finance ledger entries carry Local Pickup source identity. This is continuity inside the inbound workflow, not an upstream supplier/offer/order context. Matching pickup-code reference and Variant is an evidence association, not proof of physical collection, unique causal origin, or provider reference quality. The optional whole-pickup goods amount is especially limited: UI says the delivery company is expected to pay the supplier, while Product code only persists the amount and posts a merchant ledger debit after reconciliation; it does not prove external payment, supplier identity, invoice, or per-unit acquisition cost.

**Operational truth → economic truth → decision value:** merchant declaration = captured; positive Inventory provider movements matched by pickup code/expected Variant = connected evidence; Local Pickup receiving status/quantity comparison = calculated; configured pickup fee and optional goods-value debit = Finance-recorded economic effect after the completion predicate. Exact receipt is not required: every expected Variant needs some qualifying evidence, but short/over quantities raise an alert and still complete, deactivate expected rows, and permit configured Finance postings. Local Pickup does not itself mutate stock. No supplier payable verification, full landed cost, sourcing recommendation, operational outcome measurement, decision guidance, or learning loop is established.

**Connected value and proof/demo consequence:** a bounded demo can show a Store-scoped declaration, labels, manual request record, provider movement matching, expected/received difference and conditional Finance entry. It must show that an exception can coexist with completion and a debit; it must not imply exact fulfillment, physical collection proof, a supplier being paid, updated stock owned by Local Pickup, or an end-to-end sourcing chain. Inventory, Finance, Products, External Shipping, Sourcing/Network and Market Center retain their separate evidence/authority boundaries. No measured business outcome is established.

## 11. Feature Clusters

1. **Store-scoped declaration → operationally identifiable cartons:** authorized Product/Variant selection plus labels and canonical destination.
2. **Manual coordination → provider-evidence follow-up:** a human request event is distinct from subsequent Inventory movement evidence and scheduler recovery.
3. **Expected → received → financial realization:** expected rows stay advisory until accepted provider movements; finalization and Finance entries are transaction-bound, with mismatches preserved rather than concealed.

## 12. Merchant Journey / Old Way vs Wossol Way

| Stage | Plausible manual alternative (not universal/verified) | Current Wossol evidence |
|---|---|---|
| Declare local goods | Message or spreadsheet listing | Store-scoped carton/Product/Variant/quantity declaration |
| Identify cartons | Handwritten labels | generated pickup/carton labels and versions |
| Coordinate provider | Staff messages/calls | operator records pickup-requested action; actual call is not evidenced |
| Reconcile inbound stock | Manual comparison | pickup-code-linked provider movement receiving lines and visible differences |
| Charge goods/service | Informal/manual finance entry | completion-gated Finance methods with source identity |

The alternative is illustrative only; no customer baseline or time/cost outcome was measured.

## 13. Hidden / Non-Obvious Advantages

- The Store is a hard execution boundary rather than a display filter; list totals and rows share the authorized Store scope.
- Local Pickup only accepts new/edit Variant declarations eligible for the current canonical provider evidence path; eligibility is prospective and does not rewrite historical requests.
- Receipt completion requires evidence for every declared Variant, while quantity equality is not required. This separates completeness of Variant evidence from exact fulfillment.
- The scheduler signal is an acceleration hint; periodic reconciliation is the recovery path, and evidence—not the signal—is authoritative.
- Optional goods value is one pickup-level charge, not a per-carton/unit landed-cost model.

## 14. Data & Intelligence Assets

Current records can relate Merchant/Workspace/Store, destination, pickup request age, Variant-level expected quantity, positive provider movements, external transaction references, actual received quantity, discrepancy, fee profile/quote snapshots, optional goods value, labels, timeline, and alerts. This could support future supplier/provider reliability or inbound variance insight only if supplier/source identities and operational outcomes are captured with validated semantics. Today the data does not include a first-class supplier, pickup mode, provider dispatch/confirmation, physical proof, or verified reason for mismatch; it cannot establish supplier quality, collection SLA performance, unit acquisition cost, or broad replenishment intelligence.

## 15. Cross-Section Compound Advantages

| Connection | Compound effect | Boundary |
|---|---|---|
| Products + Stores | Valid catalog identity restricted to a Merchant's authorized Store | Not supplier selection |
| Local Pickup + Inventory | Expected inbound can be compared with provider movement evidence | Local Pickup does not mutate actual stock |
| Local Pickup + Finance | Fee and optional merchant goods-value debit are source-linked and completion-gated | Ledger posting does not prove cash settlement, supplier payment, invoice truth or acquisition-cost accuracy; mismatches can still complete/post |
| Local Pickup + External Shipping | Both contribute inbound awareness and use provider movements | Distinct models/workflows; no shared lifecycle or carrier dispatch inference |
| Local Pickup + Sourcing / Market Center | Receipt evidence follows merchant product selection; Market Center can display bounded Wossol-observed ordered activity | No supplier/offer/source identity joins the chain; ordered units are not demand, product-fit, delivery or profitability proof |

## 16. Competitive Analysis

The competitive master describes broad commerce-enablement capabilities among regional and global platforms, including sourcing, inbound shipping, warehousing, fulfillment, delivery and COD operations for some providers. These broad categories are not proof that a competitor offers or lacks an equivalent local-pickup request, evidence-matching or finance-gating workflow. Direct Local Pickup feature-equivalence research was not performed. Wossol's defensible current distinction is the inspected implementation pattern—Store-scoped declared Variant cartons reconciled to exact-code positive provider movement before source-linked charges—not a verified market-first claim.

## 17. Marketing Intelligence

**Safe, source-bounded description:** “Record local inbound pickup requests by Store and Product Variant, print carton labels, and compare declared quantities with matched Inventory provider evidence.” If mentioning Finance, say that configured ledger entries are posted after the code's evidence-completion predicate—which allows quantity mismatch. Avoid suggesting external supplier payment from `goodsPurchaseAmount`; avoid “book a courier,” “supplier pickup network,” “automatically receive goods,” “verified physical collection,” “exact fulfillment,” “accurate stock guaranteed,” “procurement,” and quantified savings or speed claims.

## 18. Surprise Findings

- The operator action is explicitly a manual request record, while the execution-control history says external provider acceptance remains pending. An action label does not demonstrate a provider integration.
- Short/over quantity mismatches produce an alert but do not block `INVENTORY_UPDATED` or the associated Finance postings once each expected Variant has any qualifying evidence.
- The current design advertises two pickup types and a human verification alternative; neither is represented by the inspected schema/controller/service path.

## 19. Potential Category Reframes

Candidate for later validation: **evidence-linked local inbound operations** or **local stock intake coordination**. Do not classify as sourcing/procurement or a local delivery network without supplier selection, provider integration, or operating evidence. This is an analytical possibility, not a final positioning recommendation.

## 20. Brand Evidence

| Candidate evidence | Strength | Qualification |
|---|---|---|
| Local request identity persists from cartons through provider receipt lines | STRONG code-level | Requires provider reference correctness and real operation validation |
| Merchant scope and Store boundary enforced on create/list | STRONG code-level | Deployment permissions not checked |
| Expected vs received discrepancies remain visible and audited | STRONG code-level | Completion and Finance still proceed despite mismatch |
| Fee/goods posting is transaction-bound to evidence completion | STRONG code-level | No production ledger/provider verification |
| Physical collection is verified | UNSUPPORTED | No physical proof or human-completion path established |

## 21. Weaknesses / Risks / Gaps

- Spec/code contract unresolved on mode selection and operator completion.
- Manual provider coordination is not a provider dispatch integration.
- Movement matching is by pickup-code reference and Variant; provider data quality/reference uniqueness are not demonstrated.
- A mismatch can finalize lifecycle and charges; escalation or hold policy is not evident in this flow.
- Reconciliation evidence provenance stores latest movement ID, joined external transaction IDs and effective time, not a separately reviewable immutable movement set snapshot.
- No supplier identity, supplier purchase order, per-unit acquisition cost, physical proof or provider SLA result is established.
- Runtime, credentials, periodic scheduler behavior in deployment, financial configuration and production economic outcomes remain unverified.

## 22. Future Strategic Potential

With explicit supplier/provider identity, pickup mode, dispatch/acceptance events, physical evidence, mismatch disposition/hold rules and reconciled cost basis, Local Pickup could contribute to local sourcing reliability and inbound variance intelligence. It cannot responsibly support supplier scoring or autonomous replenishment from current records alone. Any such extension must preserve Inventory movement as actual-stock authority and Finance as ledger authority.

## 23. Claim Safety

| Claim | Safety |
|---|---|
| “Create Store-scoped local inbound requests with carton labels” | GREEN, code-level |
| “Track declared and provider-evidenced received Variants” | GREEN with provider-evidence qualification |
| “Automatically books local pickup with suppliers/couriers” | RED / not established |
| “Confirms physical collection or exact quantities” | RED; no physical proof, and mismatch may complete |
| “Updates stock” | RED as Local Pickup ownership claim; it consumes Inventory evidence |
| “Charges only after receipt” | AMBER: after all expected Variants have evidence, even if quantities mismatch; conditional charge configuration applies |
| “Provides supplier/procurement intelligence” | RED / no current supplier source or decision capability |

## 24. Commercial Magnitude

**OPERATIONAL FOUNDATION.** Reliable inbound visibility can matter to commerce operations, but this implementation covers a bounded declaration/reconciliation workflow. No deployed usage, merchant adoption, local supply coverage, pickup success rate, mismatch outcome, or economic effect was measured. Its commercial importance is plausible, not quantified.

## 25. Strategic Classification

| Area | Classification |
|---|---|
| Store-scoped inbound declaration and labels | OPERATIONAL FOUNDATION |
| Provider-evidence-linked receiving and discrepancy visibility | POTENTIAL DIFFERENTIATOR in control design; production outcome unverified |
| Finance-gated fee and goods-value ledger entries | TRUST / ACCOUNTING CONTROL foundation |
| Courier/supplier dispatch, supplier sourcing and procurement | NOT ESTABLISHED |
| Supplier reliability and landed-cost intelligence | WHITESPACE / requires new validated data |

## 26. Action Register

| Priority | Action | Rationale |
|---|---|---|
| MUST CLARIFY | Reconcile current design pickup modes with the mode-less request schema and behavior; either implement approved modes or qualify/remove the design statement through product governance. | Prevents claiming unsupported courier/supplier workflow. |
| MUST CLARIFY | Decide whether `INVENTORY_UPDATED` is strictly provider-evidence-gated or may be operator-verified; align design/API/UI/tests and name the authority/evidence. | Current design and executable transition contract conflict. |
| MUST VERIFY | Validate provider reference uniqueness/quality, reconciliation behavior, scheduler across deployed instances and actual inventory movements. | Repository tests do not prove production truth. |
| MUST DECIDE | Define whether short/over quantities may finalize and trigger fee/goods debits or require review/hold. | Current mismatch alert does not gate completion/posting. |
| WORTH ADOPTING | Preserve per-movement evidence detail and explicit mismatch disposition if operational review requires stronger auditability. | Current receiving line summarizes references and latest movement. |
| MUST BEAT | Research specific competitor local-inbound workflows before asserting differentiation. | Competitive baseline is broad, not feature-level. |

## 27. Evidence Register

**EV-LP-001 — Merchant Local Pickup UI.** **Type:** P1/P2. **Paths:** `apps/frontend/src/app/merchant/local-pickup/{page.tsx,create/page.tsx,[pickupId]/page.tsx,[pickupId]/edit/page.tsx}`. **Observed:** scoped list and creation, destination quote, cartons, labels, Created-only edit/cancel, Merchant-safe detail and declared/received/difference projection. **Confidence:** High source-level; no authenticated production browser session.

**EV-LP-002 — API/controller and data client.** **Type:** P1/P2. **Paths:** `apps/backend/src/modules/local-pickup/local-pickup.controller.ts`, frontend `local-pickup-data.ts`. **Observed:** Merchant create-options/quote/create/list/detail/edit/cancel/labels; Admin queue/detail/alerts/manual request/resolve/labels. No provider dispatch or manual completion route observed. **Confidence:** High inspected.

**EV-LP-003 — Authorization and declaration validation.** **Type:** P1/P2. **Path:** `apps/backend/src/modules/local-pickup/local-pickup.service.ts` (`assertMerchant`, `assertInternal`, `assertMerchantStore`, `assertStoreAndVariants`, create-options). **Observed:** active Workspace/Merchant/permission and Store access; active Merchant/Workspace/Store-linked Variant plus exact Workspace Accurate/Mayar mapping requirements. **Confidence:** High source/tests.

**EV-LP-004 — Lifecycle mutation.** **Type:** P1. **Path:** `local-pickup.service.ts` (`createMerchant`, `editMerchant`, `cancel`, `markPickupRequested`). **Observed:** create snapshots request and expected lines; only CREATED edit/cancel; operator action transactionally transitions CREATED→IN_PROGRESS and records manual request evidence. **Confidence:** High source and selected tests.

**EV-LP-005 — Alerts, timeline and scheduler.** **Type:** P1/P2. **Paths:** `local-pickup.service.ts`, `local-pickup.scheduler.ts`, related specs. **Observed:** deduplicated mismatch/delay alerts; note-required resolution; prompt signal plus periodic fallback; default 24-hour delay from request timestamp. **Confidence:** High code/tests; deployed scheduler not checked.

**EV-LP-006 — Inventory reconciliation.** **Type:** P1. **Paths:** `local-pickup.service.ts` (`reconcileProviderEvidence`), Inventory provider movement model/signal. **Observed:** queries positive movements matched by exact pickup code as shipment or manifest ref, groups expected Variants, waits for a receiving line for every expected Variant, stores quantities/evidence refs, mismatch-alerts, transitions to INVENTORY_UPDATED. It does not write stock. **Confidence:** High source and focused service tests; provider truth not verified.

**EV-LP-007 — Finance.** **Type:** P1. **Paths:** `local-pickup.service.ts`; `apps/backend/src/modules/finance/finance.service.ts` (`chargeLocalPickupWithClient`, `chargeLocalPickupGoodsPurchaseWithClient`). **Observed:** optional enabled destination fee and optional whole-pickup goods value charged inside accepted-evidence completion transaction; goods value has separate source classification. **Confidence:** High code/test; no production ledger check.

**EV-LP-008 — Schema and product intent.** **Type:** P1/P3. **Paths:** `apps/backend/prisma/schema.prisma` LocalPickupStatus and LocalPickup/carton/expected/receiving/timeline/alert models; `docs/wossol-system-design/03-operational-systems/local-pickup-system.md`; `docs/ui/merchant/MERCHANT_LOCAL_PICKUP_UI_SPEC.md`; `docs/PROJECT_EXECUTION_CONTROL.md`. **Observed:** current enum/model has no pickup type/supplier FK; design names by_courier and supplier_direct_delivery; design allows operator verification while implementation has no such transition endpoint; execution notes call provider acceptance pending. **Confidence:** High for inspected files; P3 is intent, not executable truth.

**EV-LP-009 — Focused tests.** **Type:** P2. **Paths:** backend `local-pickup.service.spec.ts`, scheduler spec, frontend Merchant/Admin Local Pickup specs. **Observed:** backend focused suite passed 35/35 tests; coverage includes scoped lists, mappings, mismatch quantities, replay/idempotency, pickup reference isolation, wait for every Variant, delay alerts and permissions. A direct frontend source-spec run was unavailable because `ts-node/register` is not installed/resolvable from the frontend package. The automated backend tests encode evidence-gated completion; exact quantity mismatch still completes and charges. **Confidence:** High for backend local command and inspected test contracts; frontend specs were inspected but not executed.

**EV-LP-010 — Connected section and competitive context.** **Type:** P1/P3. **Paths:** intelligence `02-section-intelligence/{INVENTORY,FINANCE,EXTERNAL_SHIPPING,SOURCING_NETWORK}.md`; `01-competitive/WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md`; accepted `04-review-history/EXTERNAL_SHIPPING_REVIEW_2026-09-26.md`. **Observed:** Inventory actual stock/provider movement authority, Finance ledger ownership, External Shipping distinct workflow, and Sourcing boundary (no supplier/PO sourcing established). Competitive reference supports broad category context only. **Confidence:** High for repository text; no feature-by-feature competitor review.

**EV-LP-011 — Current source state and Local Pickup delta check.** **Type:** P1. **Repository/commit:** `jetshop7/wossol-platform`, `23fd26572fb82ff86b74539e40eb0e1181bb07f3`. **Observed:** one uncommitted Orders integration-test change was excluded; current committed changes since `de58ec2e4a89a4bc9b751ea669b053e63e8d31a8` did not touch Local Pickup UI/API/service/schema/design sources. **Confidence:** High for local Git status/diff; upstream not freshly fetched.

**EV-LP-012 — Current focused regression run.** **Type:** P2. **Paths:** existing Local Pickup service/scheduler and Merchant/Admin UI specs. **Observed:** backend focused suites passed 35/35; frontend focused suites passed 15/15 on 2026-09-27. These source tests do not establish production provider, scheduler, physical receipt, Finance, or UI runtime behavior. **Confidence:** High for command results.

**EV-LP-013 — V1.2 merchant-work and connected-value synthesis.** **Type:** P1/P2. **Evidence:** EV-LP-001–008, current focused specs, `INVENTORY.md`, `FINANCE.md`, `EXTERNAL_SHIPPING.md`, `SOURCING_NETWORK.md`, Market Center accepted review. **Observed:** partial inbound receipt-ambiguity reduction and context continuity after product selection; no supplier-sourcing job removal, complete provider-tool consolidation, sourcing decision support, or outcome/learning loop. **Confidence:** High on code boundaries, low on merchant outcome.

**EV-LP-014 — UI goods-payment expectation vs Finance ledger behavior.** **Type:** P1/P2. **Paths:** merchant Local Pickup create/edit UI; `local-pickup.service.ts`; `finance.service.ts` (`chargeLocalPickupGoodsPurchaseWithClient`); Prisma `LocalPickup` and `FinancialLedgerEntry`. **Observed:** UI labels amount as expected delivery-company payment to supplier; backend stores a scalar amount and creates a merchant-funded `LOCAL_PICKUP_GOODS_PURCHASE` debit/timeline entry after Local Pickup evidence completion. No supplier payment instruction, recipient, external execution, or settlement confirmation appears in this path. The synchronized `SOURCING_NETWORK_V1_2_MIGRATION_REVIEW_2026-09-27.md` expressly requires carrying this boundary into this audit. **Confidence:** High for inspected code/UI, external payment not verified.

## 28. Contradictions & Uncertainty

1. **CONTRADICTION-LP-001 — Pickup modes and source workflow.** **Source A (P3):** `local-pickup-system.md` says V1 supports `by_courier` and `supplier_direct_delivery`. **Source B (P1):** LocalPickup schema contains no mode or supplier identifier, and controller/service offers no mode-dependent or supplier workflow; operational pickup action is manual. **Nature:** approved design scope is not represented in inspected current code. **Working conclusion:** do not claim either mode or source choice as current behavior. Resolve in product authority; this is not presumed intentional narrowing.
2. **CONTRADICTION-LP-002 — Operator verification vs provider evidence.** **Source A (P3):** design says `inventory_updated` can mean Inventory reflects update “or the operator verified the update.” **Source B (P1):** inspected state transition occurs only in `reconcileProviderEvidence` after matched positive movements and evidence for every expected Variant; no manual completion route exists. **Nature:** completion authority differs materially. **Working conclusion:** report current evidence-gated code behavior and leave design conflict unresolved; do not label “by design.”
3. **CONTRADICTION-LP-003 — Mismatch completion/charge semantics.** **Source A (P3/UI intent):** mismatch is highlighted and internal alert exists; an ordinary reader may infer exception review before completion. **Source B (P1):** code raises mismatch alert then transitions to INVENTORY_UPDATED and posts enabled service/goods charges regardless of exact received quantity, once every Variant has some evidence. **Nature:** operational/financial outcome may differ from intuitive exception control. **Working conclusion:** state the actual automatic behavior; whether it is approved policy remains an open product decision.
4. **UNCERTAINTY-LP-004 — Evidence association and stock truth.** Reference + Variant matching is code-level association, not proof of physical collection or exclusive causality. Provider reference reuse, delayed/corrected movements, negative adjustments and real API-sync semantics were not tested in production.
5. **UNCERTAINTY-LP-005 — Real-world operations.** No production database, live provider credentials/events, supplier/provider contract, physical receipt, production finance ledger, deployment scheduler topology, storage retention, or merchant outcome was inspected. Feature use and success rates are unknown.
6. **CONTRADICTION-LP-006 — Goods purchase amount / supplier payment.** **Source A (P1 UI):** describes `goodsPurchaseAmount` as the amount the delivery company is expected to pay the supplier while collecting goods for Wossol. **Source B (P1 service/schema/Finance):** persists a whole-pickup amount and, after accepted Inventory-evidence completion, creates a merchant-funded Finance debit/timeline; no payment instruction, supplier identity, external recipient, execution or settlement proof is present. **Nature:** payment expectation wording exceeds the demonstrated external payment behavior. **Working conclusion:** treat as product intent/UI copy plus internal ledger evidence, not supplier-payment execution or verified payable. **Required verification:** Product/Finance authority must define whether someone actually pays a supplier and what evidence proves it; align UI and ledger semantics.

## 29. Open Questions

1. Are pickup modes and supplier-direct delivery still approved V1 scope? If so, what entities, roles, provider actions and evidence implement them?
2. Is provider movement the sole approved completion authority, or should an authorized and evidenced operator verification path exist?
3. Should quantity mismatches block Finance postings or require a documented resolution before completion?
4. How are provider reference collisions, movement corrections/reversals and late evidence handled in deployment?
5. Does a real operator call/message occur when marking pickup requested, and is provider acceptance/result recorded elsewhere?
6. Does an external party actually pay the supplier in this workflow? If so, where are recipient, instruction, execution and settlement evidence recorded, and how does that reconcile with the merchant-funded debit?
7. Which direct competitor products expose comparable local inbound workflows, and with what verified scope?

## 30. Methodology Learnings

No reusable methodology change identified. V1.2 was applied using existing rules: trace the executable workflow and authority; extract merchant effort only where a current end-to-end path supports it; follow declaration → provider evidence → Finance ledger without skipping to sourcing decisions or outcomes. The lifecycle terminal-state lesson from the prior audit remains section-specific and is already covered by the contradiction protocol.

## 31. Retroactive Review Impact

No methodology change or extra retrospective item is required. RR-V12-017 is updated for this V1.2 migration; the 2026-09-26 Local Pickup review remains historical acceptance with its open Product issues, and the current V1.2 Quality Gate is pending. The newly synchronized Sourcing / Network V1.2 review asked that the supplier-payment boundary be carried into this audit; this is recorded as CONTRADICTION-LP-006. Inventory, Finance, External Shipping, and Market Center ownership/evidence limitations remain intact.

## 32. Canonical Section Takeaway

Local Pickup currently provides a Store-scoped local inbound declaration, carton labels, manual pickup-request recording, and evidence-linked reconciliation against Inventory provider movements. It finalizes once each declared Variant has qualifying movement evidence, preserves short/over quantities as mismatch alerts, and conditionally posts Finance charges in the same completion transaction. It does not establish supplier sourcing, courier booking, physical collection verification, exact-quantity fulfillment, or stock mutation. The documented pickup modes and operator-verification alternative conflict with current schema and transition code and require explicit product-contract resolution before stronger claims.

## 33. V1.2 Migration Delta Review

- **Prior truth retained:** the prior Director-accepted Local Pickup audit's core P1 workflow and its open P1/P3 issues remain valid; the relevant Product source files have no current committed delta since the prior V1.2 Sourcing review's product HEAD. Evidence IDs EV-LP-001–010 are retained.
- **Product truth changed:** no material Local Pickup UI/API/service/schema/design change was found from Product commit `de58ec2e4a89a4bc9b751ea669b053e63e8d31a8` to current HEAD `23fd26572fb82ff86b74539e40eb0e1181bb07f3`; unrelated Analytics/Orders commits and the one uncommitted Orders integration-test change are excluded. Product was not modified.
- **V1.2 value extraction added:** Local Pickup reduces some inbound declaration/receipt ambiguity after Product selection and partially consolidates its internal record/labels/evidence. Provider coordination remains manual; sourcing, supplier search, purchasing, decision effort and measured time/risk reduction are not established. No upstream supplier/commercial context is carried in.
- **Connected-domain evidence:** Product/Variant + Store scope → expected Local Pickup declaration → positive provider movement reference/Variant → receiving comparison/status → conditional Finance entry. Inventory remains stock authority; Finance entry is not proof of cash/supplier payment; Sourcing/Network/Market Center do not complete missing source/offer/cost/outcome links.
- **Strategic/claim delta:** no new differentiator or sourcing claim. Current safe description is narrowed to comparing a declared inbound request against matched provider evidence. The payment wording/debit gap is now explicit as CONTRADICTION-LP-006; quantity mismatch can still complete and post.
- **Queue/gate:** RR-V12-017 is updated; current Director Quality Gate pending. RR-V12-016 is marked V1.2 Quality-Gate complete per `SOURCING_NETWORK_V1_2_MIGRATION_REVIEW_2026-09-27.md`; its accepted review remains preserved.
