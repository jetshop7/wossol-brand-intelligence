# Orders — Section Intelligence

## 1. Audit Metadata

- **Audit date:** 2026-09-25.
- **Methodology:** `MASTER_INSTRUCTIONS.md` v1.1; current operating protocol.
- **Competitive reference:** `WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md` v1.
- **Intelligence source:** `jetshop7/wossol-brand-intelligence`, `main`, `38f7450`, clean and synchronized before inspection.
- **Product source:** `jetshop7/wossol-platform`, `dev/wossol-integration`, `e3912a967827bde06450d3510228e5a5ca9e78a7`, clean.
- **Correction provenance:** targeted application of `04-review-history/ORDERS_REVIEW_2026-09-25.md` to audit commit `9e8c44cf6b5f4c00f85f378a7f6339e1b57aac36`; no Orders re-audit performed.
- **Evidence basis:** P1 code/schema, P2 test sources with execution status stated per evidence entry, P3 Final V1 Merchant Orders specification, and P4 architecture material only when qualified. This audit does not prove production provider connectivity, dispatch success, delivery outcomes, financial settlement, or merchant adoption.

## 2. Audit Coverage Map

| Surface | Coverage | Evidence |
|---|---|---|
| Merchant UI | list, create, import, captures, detail, tracking activity, controlled actions | EV-ORD-001–003 |
| Order API/service | create, list/detail/journey/activity/export, edit/cancel/delete, pricing and picker | EV-ORD-004–005 |
| Lifecycle and allocation | stock reservation, waiting-stock promotion, confirmation/direct dispatch, cancellation | EV-ORD-006–007 |
| Imports/commerce/attribution | workbook/import policy, idempotent commerce ingress, duplicate and attribution evidence | EV-ORD-008–009 |
| Connected domains | Products, Inventory, Customers, Confirmation, Tracking, Finance, Commerce, Messaging | EV-ORD-010–011 |
| Contract/tests | Final V1 specification and Orders test suite inventory | EV-ORD-012–013 |

## 3. Executive Section Truth

Orders is an implemented merchant order-orchestration boundary, not merely an order table. It creates a scoped canonical order graph from manual, import, commerce, or authorized messaging-capture inputs; validates product, customer, destination, pricing, payment and attribution context; applies inventory allocation before confirmation; and preserves merchant-safe lifecycle evidence across confirmation, dispatch, tracking, cancellation, deletion, and support handoff.

Its strongest current quality is disciplined ownership. A merchant receives high-value operating control—create, inspect, correct pre-dispatch data, decide on a duplicate, resolve a blocked-customer order, cancel/delete when safe, copy public tracking, and export scoped history—without exposing confirmation workers, provider calls, raw tracking, finance operations, or stock mutation. That makes Orders a controlled entry point into a broader operational system, not a validated order-performance intelligence engine.

## 4. Scope & Architecture Map

Orders owns canonical identity, status, timeline, validation, merchant-facing projection, and lifecycle transitions. Inventory owns availability/reservations; Products owns catalog facts; Customers owns customer identity/reputation; Confirmation owns workforce operations; Tracking owns normalized delivery evidence; Finance owns ledger/settlement; Commerce owns trusted external ingress; Messaging owns capture handoff; Delivery Pricing/Geography own price/destination truth. Merchant APIs live under `/orders/merchant`; import is separately controlled under `/orders/merchant/import`.

## 5. Current Capability Inventory

| Capability | Status | Current behavior |
|---|---|---|
| Scoped manual order creation | LIVE | specific active Store, validated customer/destination/items/pricing, idempotent request identity and duplicate decision boundary |
| Product selection | LIVE | backend-owned real/test eligibility; Real requires known positive stock, Test requires Test-enabled product with known zero stock |
| Stock-aware routing | LIVE | reserve outbound stock or enter Waiting for Stock; no partial reservation in waiting mode |
| Confirmation routing | LIVE, connected-domain dependent | ordinary orders enter confirmation; merchant-preconfirmed orders request controlled direct dispatch; Tests do not dispatch |
| Imports and commerce ingress | LIVE | preview/classification/confirmation path; atomic commerce graph and idempotent external mapping |
| List/detail/journey/activity/export | LIVE | scoped search/filter/groups, merchant-safe status/timeline/tracking and bounded export |
| Controlled correction/cancel/delete | LIVE, lifecycle-bounded | edit/cancel/delete predicates are restricted to `PENDING_CONFIRMATION` and `WAITING_FOR_STOCK`; blocked-customer correction/cancel is separately gated, and cancel also requires no provider shipment identity/code |
| Customer safety gate | LIVE | blocked customer can be resolved only through authoritative resume/cancel rules |
| Attribution/evidence foundation | LIVE | normalized immutable attribution and unresolved advertising evidence can be preserved separately |
| Provider/finance/worker operations | NOT merchant-controlled | provider dispatch/retry, tracking operations, settlement and confirmation workforce remain domain-owned |
| Order performance intelligence | NOT IMPLEMENTED | no current merchant recommendation/outcome-learning or profit decision projection established in Orders |

## 6. Workflow & Lifecycle

1. Merchant, authorized capture, import, or trusted Commerce ingress provides an order candidate in a Workspace/Store scope.
2. Orders normalizes phone/data, validates exact Product/Variant and destination, resolves pricing/payment policy, and checks duplicate/idempotency boundaries.
3. Commercial outbound lines are allocated through Inventory. Insufficient stock either rejects according to policy or leaves the complete order in `WAITING_FOR_STOCK`; it does not invent partial sellability.
4. A reserved ordinary order enters `PENDING_CONFIRMATION`; a merchant-preconfirmed order attempts controlled post-commit dispatch; a Test Order is confirmation-only and never sent to delivery.
5. Confirmation and Tracking evolve the canonical status while Orders exposes simplified merchant-safe group/status projections, journey and timeline facts.
6. Waiting orders are reconsidered under a serializable transaction; only full fresh allocation promotes them to confirmation and triggers assignment best-effort.
7. P1 exposes ordinary edit/cancel/delete in `PENDING_CONFIRMATION` and `WAITING_FOR_STOCK`; `BLOCKED_CUSTOMER` is a separate limited correction/cancel case. Cancellation also rechecks that provider shipment ID/code are absent in the transaction. The relationship between those predicates and P3's phrase “before processing starts” remains unresolved. Provider-shipment deletion is a separate guarded operation and does not expand ordinary merchant cancellation authority.

## 7. Value Recipient Map

| Recipient | Current value |
|---|---|
| Merchant operator | fast but constrained order entry, scoped list, customer/product/destination context and clear next step |
| Merchant owner/manager | controlled delegated access, export, immutable activity/history and safe cancellation boundaries |
| Confirmation operations | only properly scoped, allocated eligible orders enter workload; Tests stay out of delivery |
| Inventory operations | no duplicate stock authority; reservations and waiting demand are fed through canonical order state |
| Tracking/support | dispatch evidence directs communication to the correct support/confirmation channel and exposes safe status facts |
| Finance/analytics future | price, payment, delivery-policy and attribution snapshots form useful downstream evidence, not present profitability truth |

## 8. Control & Merchant Agency

The merchant controls commercial intent before dispatch: Store, valid items, customer and delivery details, payment selection where permitted, explicit duplicate override, capture completion, blocked-customer resolution, and eligible cancel/delete/edit. The merchant does not control stock, worker assignment, confirmation attempts, provider retry, dispatch payload mechanics, raw tracking, settlement, or post-processing cancellation. This is Level 3 operational control with selected Level 4 guidance (next-step, status grouping, duplicate/stock safety), not Level 5 intelligent control.

## 9. Transparency & Trust

- List, detail, journey and activity expose merchant-safe lifecycle/progress rather than raw provider/worker mechanics.
- Creation preserves request identity, scoped duplicate evidence, status history, timeline, audit/domain events, and actor context.
- Waiting-stock promotion, cancellation, blocked-customer resolution and provider-delete paths write causally useful transition evidence.
- Public tracking links are explicitly gated on dispatch state rather than assumed from a label.
- Export defensively prefixes spreadsheet-formula-sensitive text and is capped/scoped.

## 10. Merchant Value Extraction

The merchant gets a usable operating path from customer intent to a safe operational handoff. The system lowers rework and ambiguity by refusing invalid Store/product/destination combinations, distinguishing real versus test order purpose, not selling unknown stock, keeping a fully unallocated demand waiting instead of silently splitting it, and showing a simplified next step rather than exposing internal team/provider complexity.

## 11. Feature Clusters

1. **Safe acquisition-to-operation intake:** normalization + pricing/destination validation + duplicate policy + request identity + attribution turns a form/import into controlled commercial evidence.
2. **Stock-honest order routing:** order allocation + reservation + waiting-stock promotion + confirmation eligibility avoids treating a created order as fulfilment-ready.
3. **Merchant-safe operational visibility:** lifecycle groups + activity/journey + tracking projection + public-link gate reveal useful progress without leaking operations infrastructure.
4. **Correctable before irreversible:** edit/cancel/delete/blocked-customer actions are deliberately available only while they cannot conflict with provider/confirmation history.

## 12. Merchant Journey / Old Way vs Wossol Way

| Stage | Common risk | Current Wossol behavior |
|---|---|---|
| Capture | orders arrive from forms/messages/sheets with ambiguous ownership | canonical manual/import/Commerce/capture paths retain source and scope |
| Create | a form saves before stock/destination conditions are known | validates and allocates before operational confirmation route |
| Monitor | merchant chases separate teams/systems | single merchant-safe list, detail, journey, activity and tracking link |
| Intervene | edits/cancellations conflict with real operations | lifecycle gates and explicit blocked-customer resolution prevent unsafe shortcuts |
| Recover | waiting demand disappears or is partially allocated | serializable retry promotes only a fully allocatable order |
| Understand | status names hide evidence | grouped labels, timeline/history and safe tracking context explain enough without raw internals |

## 13. Hidden / Non-Obvious Advantages

- An order is not automatically confirmation-eligible just because it was created; inventory allocation and customer state can safely divert it.
- Duplicate protection does not merely block: a merchant can make an explicit `CREATE_ANYWAY` decision after authoritative possible-duplicate evidence.
- A customer unblock does not resurrect historic orders; a current eligible order must be consciously resumed or cancelled and re-allocated.
- Test purpose is immutable and deliberately reverses stock eligibility (known zero), making demand-validation activity distinct from commercial delivery.

## 14. Data & Intelligence Assets

Orders persist scoped identity, source, actor, status history, timeline/audit/domain evidence, item/price/payment/delivery snapshots, normalized customer contact, Store/Workspace context, confirmation mode, provider shipment/tracking references, duplicates, import/commerce origin, messaging capture handoff, and attribution/advertising evidence. This supports future cross-domain causal analysis if linked carefully to confirmation, delivery, return, cost and settlement outcomes.

It is not itself a completed intelligence loop: stored attribution or delivery status does not demonstrate campaign profitability, customer quality, merchant recommendations, or learning from actions.

## 15. Cross-Section Compound Advantages

- **Products × Inventory × Orders:** exact Variants plus protected availability prevent a catalog selection from becoming an unsafe order commitment.
- **Orders × Confirmation:** only eligible orders route into confirmation, while merchant projections retain safe next-step language.
- **Orders × Customers:** a blocked identity becomes a controlled Order decision, not an automatic historical rewrite.
- **Orders × Tracking/Support:** dispatch evidence changes communication eligibility and unlocks tracking/public-link visibility.
- **Orders × Commerce/Advertising/Finance:** immutable ingress/attribution and commercial snapshots are prerequisites for later downstream performance truth, not evidence of it today.

## 16. Competitive Analysis

Orders, tracking, confirmation, delivery, COD collection and basic status visibility are category table stakes across direct competitors. COD Network demonstrates material operational/API breadth; CODZOSS publicly describes stage tracking; Fufills emphasizes hard-gated confirmation and operational warnings. The competitive master does not verify a competitor-equivalent of Wossol’s precise combination of scoped merchant agency, reservation-aware waiting stock, trusted ingress/idempotency, immutable purpose boundaries, and merchant-safe evidence projection. That is a candidate depth advantage, not a superiority claim.

## 17. Marketing Intelligence

**Asset ID:** ORD-01
**Capability:** Safe order handoff, not just order capture.
**Evidence IDs:** EV-ORD-004–007, EV-ORD-013. **Evidence status:** GREEN for executable lifecycle rules; YELLOW for live operational reliability.
**Merchant problem:** an order can look created while stock, customer or delivery readiness is unresolved.
**Angle:** “Create orders that are ready for the next operation—not just saved in a list.”
**Claim eligibility:** qualified; never claim delivery success or instant fulfilment.

**Asset ID:** ORD-02
**Capability:** Merchant-safe order control with operational accountability.
**Evidence IDs:** EV-ORD-001, EV-ORD-005, EV-ORD-010.
**Angle:** “See what your order needs next, without needing to run the operations team.”
**Caveat:** status visibility is not control over confirmation, delivery or settlement.

## 18. Surprise Findings

The unusually strong behavior is restraint: unknown availability is not orderable, a waiting order is not partially reserved, a blocked customer is not silently resumed, and dispatched work is not relabeled as a simple merchant cancellation. These rules are more strategically meaningful than a broad status table because they preserve trust at cross-domain handoffs.

## 19. Potential Category Reframes

Current defensible framing: **order operations with safe handoffs** or **orders that carry their operational context forward**. Avoid “autonomous order management,” “guaranteed delivery,” “full order intelligence,” “one-click fulfilment,” or “merchant controls every operation.”

## 20. Brand Evidence

- **Control:** merchants control commercial intent and correction within safe boundaries.
- **Transparency:** order state, next step, tracking facts, history and source evidence are visible in merchant-safe language.
- **Accountability:** actor, request, transition, audit and timeline evidence make sensitive actions challengeable.
- **Reliability orientation:** validation, reservation, serializable promotion, idempotency and gated irreversible actions favor correct operations over superficial speed.

## 21. Weaknesses / Risks / Gaps

1. Live provider dispatch, tracking accuracy, confirmation performance, delivery rates, returns, payout and finance outcomes were not verified.
2. The Order module is highly coupled to several operational domains; the merchant experience depends on those services and their permissions/availability.
3. No current Order-level recommendation, outcome attribution, profitability, SLA, or performance intelligence was established.
4. Test Order eligibility requires known zero effective availability; this is intentionally narrow and can surprise merchants if its education is weak.
5. Cancellation authority has an unresolved P3/P1 boundary: the Final V1 contract permits merchant cancellation only before processing starts, while ordinary P1 merchant cancel accepts `PENDING_CONFIRMATION`, `WAITING_FOR_STOCK`, or `BLOCKED_CUSTOMER` and then requires provider shipment ID/code to be absent. The P3 phrase has not been proven equivalent to those exact gates. Provider-shipment deletion is separately guarded and is not ordinary merchant cancellation authority.
6. P3 includes broad UI intent; every documented acceptance state was not individually re-executed in a browser. P1 governs executable conclusions, while material P3/P1 contract conflicts remain unresolved.

## 22. Future Strategic Potential

| Category | Assessment |
|---|---|
| Current foundation | canonical order evidence, protected allocation, source/attribution, lifecycle history and safe handoffs |
| Inferred extension | join ads, confirmation, delivery, returns, cost and settlement into qualified order-quality/profit decisions |
| Strategic relevance | Orders can become the causal spine for merchant operational intelligence if downstream evidence is verified |
| Brand relevance | future territory is “understand which demand becomes a healthy business,” not a current claim |

## 23. Claim Safety

| Claim | Safety | Reason |
|---|---|---|
| Create, import and follow scoped orders with merchant-safe progress | GREEN | implemented routes/projections |
| Keep stock-aware orders from entering confirmation prematurely | GREEN, provider-qualified | reservation/waiting rules are implemented; live provider data unverified |
| Correct orders under the implemented pre-dispatch lifecycle gates | GREEN, P1-qualified | guarded P1 behavior; broader contract relationship is unresolved |
| Merchant cancellation only before processing starts | YELLOW / unresolved | P3's phrase is not proven equivalent to P1's `PENDING_CONFIRMATION` / `WAITING_FOR_STOCK` / `BLOCKED_CUSTOMER` status gates plus absent provider shipment ID/code |
| Give merchants full confirmation/tracking/provider control | RED | deliberately not exposed |
| Guarantee delivery, prevent all duplicates, or optimize profit | RED | no outcome proof or optimization found |
| Offer end-to-end order intelligence | RED | evidence foundation exists; intelligence loop does not |

## 24. Commercial Magnitude

**FOUNDATIONAL.** Orders is the commercial and operational handoff where customer demand becomes a commitment affecting stock, confirmation capacity, delivery, cash collection and later finance. Its immediate value is high even without intelligence because unsafe order creation can contaminate every later domain.

## 25. Strategic Classification

| Area | Classification |
|---|---|
| Order creation, status list, import, tracking link | TABLE STAKES / MUST MATCH |
| Merchant-safe confirmation/tracking separation | PARITY / MUST MATCH |
| Reservation-aware waiting-stock path and full reallocation | POTENTIAL DIFFERENTIATOR |
| Controlled evidence-rich lifecycle/duplicate/block resolution | WOSSOL STRONGER / potential differentiator |
| Ad-to-delivery-to-profit decision loop | WHITESPACE / future |

## 26. Action Register

| Priority | Action | Rationale |
|---|---|---|
| MUST FIX | Measure and expose merchant-safe creation, confirmation, dispatch and delivery reliability where evidence supports it. | Current robustness is code-level, not demonstrated operation-level trust. |
| MUST MATCH | Keep order-create/import guidance clear for Test purpose, waiting stock, duplicate decisions and pre-dispatch boundaries. | Safety is only valuable when merchants understand it. |
| MUST BEAT | Build verified downstream order-quality/profit decision projections from the existing evidence spine. | This converts infrastructure into intelligence. |
| WORTH ADOPTING | Consider concise merchant-safe operational warnings inspired by Fufills, only with outcome-quality evidence. | Visibility needs actionable, trustworthy context. |
| DO NOT COPY | Do not expose raw provider/worker mechanics as a substitute for merchant control. | It would increase noise and leak internal operational detail. |
| POTENTIAL MOAT | Preserve immutable cross-domain source, decision and outcome evidence. | History/provenance compounds more slowly than a list UI. |

## 27. Evidence Register

**EV-ORD-001 — Merchant Orders UI.** **Type:** P1. **Paths:** `apps/frontend/src/app/merchant/orders/page.tsx`, `create/page.tsx`, `detail/page.tsx`, `import/page.tsx`, `captures/page.tsx`, `tracking-activity.tsx`. **Observed:** scoped order operating views, creation/import/capture handoff, detail/actions and merchant-safe tracking presentation. **Confidence:** High.

**EV-ORD-002 — Client projections and navigation.** **Type:** P1. **Paths:** `order-data.ts`, `order-list-request.ts`, order navigation/filter specs. **Observed:** typed merchant APIs and controlled filters/navigation states. **Confidence:** Medium-high.

**EV-ORD-003 — Merchant Orders contract.** **Type:** P3. **Path:** `docs/ui/merchant/MERCHANT_ORDERS_UI_SPEC.md`. **Observed:** approved boundaries for merchant-safe lifecycle, confirmation/dispatch/tracking/finance separation, Test Orders and blocked-customer resolution. **Confidence:** High for intent.

**EV-ORD-004 — Merchant API surface.** **Type:** P1. **Path:** `apps/backend/src/modules/orders/orders.controller.ts`. **Observed:** authenticated scoped create/list/detail/filter/picker/pricing/activity/journey/export/cancel/resolve/update/delete routes. **Confidence:** High.

**EV-ORD-005 — Orders orchestration.** **Type:** P1. **Path:** `orders.service.ts`. **Observed:** input normalization, scope/permission checks, pricing, product validity, duplicate/idempotency, status projections, create/edit/cancel/delete and merchant-safe detail/journey/activity behavior. **Confidence:** High.

**EV-ORD-006 — Inventory-aware lifecycle.** **Type:** P1. **Paths:** `orders.service.ts`, `inventory-reservation.service.ts`, `inventory-reservation-policy.ts`. **Observed:** outbound reservations, no partial waiting allocation, Test/Real stock eligibility and dispatch-consumption boundary. **Confidence:** High.

**EV-ORD-007 — Waiting-stock recovery.** **Type:** P1. **Path:** `waiting-stock-promotion.service.ts`. **Observed:** serializable lock/reallocation, exact promotion to confirmation, history/timeline/audit/domain evidence, best-effort assignment after commit. **Confidence:** High.

**EV-ORD-008 — Import control.** **Type:** P1 with P2 test-source references. **Paths:** `order-import.service.ts`, workbook/policy/destination/confirmation specs. **Observed:** source inspection supports import preview/classification, durable created-order duplicate evidence, and confirmation-aware execution boundaries. The related specs are evidence locations; no passing execution for these specs was observed during this audit. **Confidence:** High for the described source behavior; test execution not verified here.

**EV-ORD-009 — Commerce and attribution intake.** **Type:** P1 with P2 test-source references. **Paths:** `createCommerceImportedOrder`, `order-attribution.ts`, `commerce-order-ingestion.spec.ts`, attribution specs. **Observed:** source inspection supports trusted backend-only ingress, atomic canonical graph, idempotent external import mapping, immutable attribution evidence and separate unresolved advertising evidence. The related specs are evidence locations; no passing execution for these specs was observed during this audit. **Confidence:** High for the described source behavior; test execution not verified here.

**EV-ORD-010 — Communications/tracking boundary.** **Type:** P1. **Path:** `order-communication-eligibility.service.ts`; detail projection in `orders.service.ts`. **Observed:** dispatch evidence, not only a status label, determines support versus confirmation communication eligibility; tracking data is projected merchant-safely. **Confidence:** High.

**EV-ORD-011 — Connected architecture.** **Type:** P1. **Path:** `orders.module.ts`. **Observed:** explicit dependency boundaries to Confirmation, Inventory, Tracking, Finance, Customers, Products, Pricing, Geography, Commerce and Messaging-related order intake. **Confidence:** High.

**EV-ORD-012 — Schema and status taxonomy.** **Type:** P1. **Paths:** `apps/backend/prisma/schema.prisma`, `merchant-order-status-groups.ts`, `merchant-order-create-next-step.ts`. **Observed:** canonical status model and merchant-safe grouped/next-step projection. **Confidence:** High.

**EV-ORD-013 — Test evidence and execution status.** **Type:** P2 sources, execution not verified. **Paths:** Orders service/controller/list/detail/journey/import/duplicate/attribution/communication/waiting-stock/commerce specs. **Observed:** the repository contains focused specs covering lifecycle, scope, ingress, allocation and recovery. A broad Orders glob test command was started during the audit but returned no completion result; no Orders test command is recorded as having run to completion during the audit. These files establish test coverage intent only here, not passing behavior. **Confidence:** Medium for test-source inventory; no P2 pass result claimed.

## 28. Contradictions & Uncertainty

1. **CONTRADICTION-ORD-001 — Contract versus runtime verification:** P3 is Final V1 intent, but this audit used P1 source inspection rather than browser/live-provider execution. P1 supports the executable lifecycle conclusions; individual UX acceptance behavior remains unverified.
2. **CONTRADICTION-ORD-002 — Cancellation contract versus executable lifecycle boundary:** **Source A (P3):** Final V1 says a merchant may cancel only before processing starts. **Source B (P1):** ordinary merchant cancellation accepts `PENDING_CONFIRMATION` or `WAITING_FOR_STOCK`, and also permits `BLOCKED_CUSTOMER` through the cancellable-status predicate; transaction-time checks require provider shipment ID and provider shipment code to be absent. These are guarded by `orders.cancel_before_processing`. Provider-shipment deletion follows a separate guarded path with distinct later lifecycle statuses. **Nature:** the P3 phrase has not been shown equivalent to the P1 status/dispatch-evidence predicates. **Evidence strength:** P3 approved contract and P1 executable behavior are both material; neither silently supersedes the other. **Working conclusion:** merchant cancellation exists under P1's stated gates, but contract alignment is UNCERTAIN. **Unresolved resolutions:** product authority may amend/clarify Final V1 to match P1, or P1 may be incomplete/misaligned with the current contract. Provider-shipment deletion does not broaden ordinary merchant cancellation authority. **Required verification:** product-authority decision and, if needed, implementation/spec correction.
3. **CONTRADICTION-ORD-003 — Intelligence risk:** attribution, order history and tracking snapshots are present, but none prove ad-to-order-to-delivery-to-profit decision intelligence. Any claim beyond evidence capture is future territory.
4. Provider/runtime/database state was not observed. Implemented behavior must not be represented as a delivery, settlement, stock, or support-service guarantee.

## 29. Open Questions

1. Will product authority amend/clarify the Final V1 “before processing starts” cancellation rule to match P1's `PENDING_CONFIRMATION` / `WAITING_FOR_STOCK` / `BLOCKED_CUSTOMER` gates plus the absent provider shipment ID/code requirement, or is P1 incomplete/misaligned and implementation correction required?
2. What are production create/import/confirmation/dispatch/delivery failure and recovery outcomes?
3. How do Finance settlement and final costs attach to an order without rewriting historic commercial snapshots?
4. What verified downstream outcomes can safely power order-quality or campaign decisions?
5. How are operator/support escalations experienced by merchants after dispatch?

## 30. Methodology Learnings

No reusable methodology change identified. Existing evidence hierarchy, ownership-boundary analysis and contradiction protocol adequately distinguish a robust orchestration layer from unproven intelligence/outcomes.

## 31. Retroactive Review Impact

No methodology change and no retroactive queue entry. Orders evidence should inform later Confirmation, Tracking/Delivery, Finance, Customers, Advertising and Integrations audits.

## 32. Canonical Section Takeaway

Orders is Wossol’s controlled commercial handoff: it turns scoped demand into a validated, stock-aware, evidence-preserving operational lifecycle while keeping sensitive confirmation, dispatch, tracking and finance work behind merchant-safe boundaries. P1 implements cancellation gates, but their relationship to the Final V1 “before processing starts” contract remains unresolved. The audit supports disciplined handoff and accountability; cancellation scope, delivery outcomes, and order-performance intelligence require the qualifications recorded above.
