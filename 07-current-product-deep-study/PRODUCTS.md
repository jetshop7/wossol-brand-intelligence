# PRODUCTS — Current Product Deep Study

**Status:** Evidence-led current-product study; code-verified findings distinguished from unverified deployment and unresolved cross-module boundaries.  
**Primary source:** `jetshop7/wossol-platform`, branch `dev/wossol-integration`.  
**Study purpose:** Product truth → Merchant value → Compound advantages → Brand/Marketing/Website evidence.  
**Companion:** `07-current-product-deep-study/HOME.md` (not modified).

## 1. Executive product truth

Wossol Products is more than a catalog-entry screen. Its current code establishes a **scoped product-and-variant identity layer** that several commercial and operational workflows use: store association, provider mappings and inventory visibility, Shopify COD, advertising mapping health, payment policy, Orders, Confirmation Upsell, and reservation-aware inventory.

The strongest evidence-based narrative is **“one product identity, connected to the decisions and operations around it”**, not “universal automatic synchronization.” Product value comes from retaining identity and context as work crosses sections, while making mapping gaps, missing stock evidence, and retry/manual-review states explicit.

This is a **repository-code finding**, not evidence that every path has been exercised on production or that every merchant-visible feature is available in every plan or integration configuration.

## 2. Scope, research method, evidence grades

- **Verified (code):** specific branch implementation observed in identified source paths.
- **Verified (test):** explicit checked-in test asserting a behavior; source-inspection tests are weaker than executed integration tests.
- **Pending:** requires additional source trace, running-system acceptance, or external-provider proof.
- **Rejected:** proposition inconsistent with inspected implementation.
- **Current vs Future:** only currently observed implementation supports present-tense product copy. A roadmap or attractive interpretation is not a feature.

The following are not interchangeable: a feature existing in source; an automated test existing; that test passing on a specific commit; a deployed release; an external integration working with a live account.

## 3. Product's role in the merchant journey

A merchant needs to create a saleable product, manage its variants, place it in the correct Store, understand whether an external provider/Shopify/advertising mapping is usable, and carry its identity into Orders and downstream operations. Without a consistent identity layer, a merchant must repeatedly match products, variants, external IDs, stock numbers, and order lines.

Wossol's current Products implementation reduces parts of this reconciliation through local Product/Variant identities, scoped store links, provider mapping records, explicit connection-health projections, and downstream validation. It does **not** prove all providers, sales channels, or financial workflows are automatically synchronized.

## 4. Merchant-facing surfaces and core operations

**Product list.** `apps/frontend/src/app/merchant/products/page.tsx` and `apps/backend/src/modules/products/merchant-products-list.controller.ts`: scoped list, search, variants and connection/status information, product operations. The backend applies merchant/workspace/store and permission boundaries; list behavior must not be marketed as an unrestricted organization-wide catalog.

**Create.** `apps/frontend/src/app/merchant/products/create/page.tsx` and `products.controller.ts`: creation with variant options/combinations, Store association, explicit Test Product classification, and product commercial settings including payment-related choices. The existence of controls does not establish every option is supported by every external provider.

**Edit and variants.** `merchant-products-edit.controller.ts`, `merchant-products-variants.controller.ts`, `apps/frontend/src/app/merchant/products/edit/page.tsx`, `variant-edit/page.tsx`: product and variant maintenance; image staging/upload/removal; scoped mutations. Local edit transaction uses Serializable isolation for relevant local records and audit. External Mayar sync is performed after the local commit; **do not claim one globally atomic transaction spanning Wossol and Mayar**.

**Detail and associations.** `merchant-products-detail.controller.ts`: detailed product context and permission-gated Shopify references. `ProductConnections.tsx` and `ProductAdvertisingMappings.tsx` expose connected context; available backend data remains the authority for the meaning of statuses.

**Delete.** Product deletion is constrained by provider and historical usage rules. The inspected Accurate Mayar partial-sync tests include provider-first deletion and historical deletion restrictions; do not claim deletion is always immediate or consequence-free.

### Merchant problem → mechanism → benefit

| Merchant problem | Wossol mechanism | Work reduced | Proof surface |
|---|---|---|---|
| Re-entering product/variant identity across workflows | Canonical local Product and Variant records | Repeated identity matching | Product detail → Order line |
| Uncertainty about store eligibility | Workspace/Merchant/Store-scoped links and authorization | Manual checking of ownership | Store-scoped product list/picker |
| Confusing unknown inventory with zero | Nullable provider quantity and confirmation timestamp | Incorrect manual interpretation | Product read projection |
| Unsure which connection needs work | Provider/Shopify/Advertising health projections | Opening every external system to discover gaps | Product Connections |
| Partial provider operation failure | Recovery and manual-review states | Blind retry and duplicate-operation risk | Provider sync recovery UI |
| Test data contaminating live workflows | Product-driven Test classification and downstream checks | Separate manual test tracking | Test Order/Shopify COD scenario |

## 5. Variant identity, catalog read model, and stock truth

`product-read-projection.service.ts` returns authorized ACTIVE products and variants, with stable bounded pagination and search over names/SKUs/variant identifiers. Accurate Mayar-backed availability is derived from mapped inventory evidence (`lastKnownAvailableQuantity`), with `providerInventoryConfirmedAt` freshness context.

**Critical truthfulness rule:** unknown quantity is `null`, **not zero**. A product total is unknown when not all variant quantities are known. This avoids a false assertion that stock is absent when the system lacks reliable evidence. The read projection is not a live guarantee of physical warehouse quantity.

`inventory-reservation-policy.ts` calculates effective availability as provider on-hand minus active reservations and pending provider-sync consumption, floored at zero; unknown provider quantity remains unknown. Reservation-required shipment type is `OUT`. `hasSufficientEffectiveStock` refuses unknown availability and quantities below the request and rejects explicitly inactive provider state.

**Value:** more honest stock context and fewer decisions based on false certainty. **Boundary:** no claim of real-time perfect stock or elimination of overselling under all external races.

## 6. Test Product vs Real Product — cross-system behavioral contract

**Verified from `orders.service.ts`:**
- `Product.isTestProduct`, **not stock quantity**, defines Test eligibility.
- A Test Order must contain Test Products; a Real Order cannot contain Test Products.
- Test Orders do not reserve stock; eligible Test variants need not show positive available quantity in the orderability check.
- Manual Test Order creation has specific restrictions, including Wossol Confirmation and FDP delivery intent; these manual-path constraints must not be indiscriminately projected onto Shopify-originated paths.
- Test Orders are not dispatched to real delivery through the normal Test path; confirm all alternative downstream entry points separately before a universal claim.

**Verified from `shopify-cod-offers-upsells.service.ts` and `shopify-cod.service.ts`:**
- Test base products cannot use Shopify COD upsells.
- Test Products are excluded as targets for real Shopify upsells; the active Store-scoped real-product requirement is enforced.
- Frozen upsell sequences for Test checkout must be empty; attempting an upsell decision on Test checkout is rejected.
- Checkout session classification is checked against product context.
- On Test checkout `INCOMPLETE_TIMEOUT`, session is marked `EXPIRED_UNFINALIZABLE` rather than creating an `INCOMPLETE_CHECKOUT` recovery Order.
- Completed Shopify COD checkout passes the expected Test classification into canonical order ingestion.

**Important unresolved boundary:** `confirmation.service.ts` Confirmation Upsell selection validates ACTIVE Product/Variant and authorized Store, but the inspected query does not explicitly filter `isTestProduct`. It remains **Pending** whether another guard guarantees no Test/Real mixing on this path. This is a review candidate, not a confirmed vulnerability.

**Brand consequence:** describe Test as an **operationally separated product classification across verified workflows**, not “complete test isolation everywhere” until all entry points are audited.

## 7. Accurate Mayar provider lifecycle and recovery

**Observed:** `product-provider-sync-state.ts`, `product-provider-sync-recovery.spec.ts`, `product-accurate-mayar-partial-sync.spec.ts`, `product-provider-connection-health-projection.service.ts`.

Provider state is more nuanced than “connected/disconnected.” Recovery interpretation distinguishes retryable failures, action-required/manual-review states, and expired running claims. Read-only health projections do not themselves execute a retry. Recovery tests cover duplicate-operation prevention, sanitized errors, workspace/mapping reconciliation, compare-and-set transitions, idempotent success, and races.

Partial multi-variant provider operations have compensation/fail-closed logic when downstream provider creation fails. If compensation fails, code avoids pretending mappings/activation succeeded. Tests support intent and selected cases, **not a blanket guarantee of distributed atomicity**.

Health includes mapped vs active variants and recovery attention counts; `NOT_CONNECTED`, `PARTIAL`, `CONNECTED`, and `ATTENTION_REQUIRED` are not interchangeable. `product-operational-attention.ts` prioritizes provider attention before Shopify incomplete state, but does **not** flag every unconnected or partially connected provider as general readiness failure.

**Merchant value:** reduce the time spent determining whether an operation completed and whether the next step is safe retry or human reconciliation.  
**Proof/demo:** a simulated interrupted multi-variant sync and its resulting attention/recovery status; do not stage a destructive live-provider failure without safeguards.  
**Copy boundary:** no “one-click guaranteed recovery” or “zero duplicates in every failure mode.”

## 8. Shopify relationship

`shopify-product-health-projection.service.ts` presents a read-only view of canonical local mapping completeness; batch reads do not make a live Shopify API call. Therefore “Shopify health” is a **local mapping/completeness assessment**, not evidence of live external storefront uptime or every SKU being available for purchase.

`product-landing-references.ts` accepts valid HTTP(S) links, combines manual landing references and permission-gated Shopify storefront references, and deduplicates/sorts them.

Shopify COD has product-level configuration, form configuration, commercial calculations, offer/upsell rules, checkout sessions and order projection. A product's value extends beyond its catalog page when correctly mapped into checkout and Orders. But the product mapping, checkout session and downstream order are distinct states; avoid equating “linked” with “all checkout flows healthy.”

**Demo:** product detail → Shopify connection state → eligible checkout configuration → canonical order creation, using a test environment and clearly labeled Test/Real behavior.

## 9. Advertising relationship

`advertising-product-connection-health-projection.service.ts` validates eligible Meta mapping targets and returns `COMPLETE`, `PARTIAL`, or `NOT_CONNECTED`. The meaning of `COMPLETE` is **not necessarily “every active variant mapped”**: a valid product-level mapping may qualify, or all active variants may be eligible. Product mapping eligibility is not proof of attribution completeness, campaign performance, or ad delivery.

Advertising health is permission-gated through `product-connection-health.service.ts`; restricted/unavailable is not silently presented as a healthy empty result.

**Value:** reduce uncertainty about whether a Product has an eligible advertising mapping before moving into other ad-related work. **Boundary:** no claim that creating a Product automatically launches or optimizes ads.

## 10. Connection health as an operational interface

`product-connection-health.service.ts` combines scoped health views for provider, Shopify and Advertising, with explicit permission boundaries (`products.view`, commerce access, advertising access). Failures are surfaced as unavailable/restricted rather than fabricated success.

This is an important pattern: **Product Connections describes multiple distinct relationships**, not one global “connected” boolean.

**Merchant work removed:** checking three areas independently to understand the current mapping/attention state.  
**Safety:** unknown or unauthorized state is not a false “OK.”  
**Design opportunity:** make each status explain *what is connected*, *what is missing*, *what is unknown*, and *what the merchant can do next*.

## 11. Products → Orders

`orders.service.ts` validates ACTIVE Product/Variant, scoped eligibility and Test/Real consistency. A Product/Variant is not merely a label; its identity governs which order lines are permitted. In the inspected order creation path, external Accurate Mayar validation is deferred from local Order creation toward Confirmation; this separation reduces synchronous provider dependency at creation but must be understood alongside later reservation/confirmation rules.

For Real Order stock-picker checks, known positive effective stock is required where that check is enforced. For Test, that positive-stock condition does not apply.

**Value:** fewer invalid cross-store selections and more consistent order provenance. **Boundary:** the existence of product validation does not imply every external ingestion route is identical to manual creation.

## 12. Products → Confirmation → Inventory: explicit Upsell reconciliation

`confirmation.service.ts` `updateUpsell`:
- requires pre-dispatch editability and access;
- validates Product, Variant, positive integer quantity, positive row commercial total, and duplicate Variant exclusion;
- locks the Order row within a database transaction;
- validates ACTIVE scoped Product/Variant and Store link;
- releases reservations and deactivates removed upsell lines;
- updates reservation quantity and line totals for existing upsells;
- creates new OrderItems, payment-policy snapshots where applicable, upsell records and reservations for new upsells;
- adjusts Order subtotal/amount to collect by the commercial delta;
- writes action log, audit and timeline in the same transaction.

`inventory-reservation.service.ts`:
- locks Variant rows in deterministic order;
- checks ownership/scope and effective availability;
- aggregates multiple requests for the same Variant before allocating;
- rejects insufficient stock with structured `INVENTORY_INSUFFICIENT_STOCK`;
- in explicitly allowed waiting mode, returns `WAITING_FOR_STOCK` with no partial reservations;
- when modifying an existing reservation, adds its currently reserved quantity back to available stock before assessing the new quantity.

`inventory-reservation.service.spec.ts` has unit-style/harness tests for insufficient stock, duplicate-Variant aggregate preflight, last unit, waiting-mode no partial allocation, and deterministic lock order. **Do not misread test harness `source: 'TEST'` as evidence that the Order is a Test Order**: it is an event/source label in those cases.

**Compound value:** an upsell changes both the commercial Order and the reserved physical demand. This reduces the manual reconciliation that would otherwise be needed between the sales decision and stock allocation.

**Open validation:** although operations run inside a Prisma transaction, a targeted executed integration test is still needed to establish end-to-end rollback behavior under each injected failure, including audit and reservation state. Do not claim an experimentally verified rollback guarantee from source structure alone.

## 13. Product payment policy and commercial snapshots

Products have payment-policy/electronic settings endpoints (`products.controller.ts`). Confirmation Upsell captures product payment-policy snapshots for newly created items when `paymentPolicySnapshotVersion === 1`. The key product value is continuity of commercial context across the product-to-order transition; the precise policy variants and payment-provider execution require dedicated review before making public claims.

**Avoid:** “all payment methods supported” or “payment automatically collected” without verifying the actual supported channels, eligibility and settlement behavior.

## 14. Permissions, tenancy and trust

The inspected product list, detail, edit, health and reservation paths use Workspace/Merchant/Store identity and permission checks. Store links matter operationally: a product belonging to the same merchant is not necessarily valid for every store or order.

Audit/history is present in local edit and Confirmation Upsell flows. External synchronization and local mutation can have different transaction boundaries; therefore “auditable local change” and “external provider successfully updated” must remain separately legible.

**Merchant benefit:** fewer accidental cross-store operations and better traceability of selected changes. **Do not claim:** formally proven tenant isolation for every uninspected endpoint.

## 15. Failure behavior and negative evidence

| Case | Observed protection | Limitation |
|---|---|---|
| Unknown provider stock | Keep unknown rather than zero | Not a live physical count |
| Insufficient reservation stock | Reject with structured conflict | Not proof of zero oversell in every race |
| Waiting-mode shortage | No partial reservation | Only when waiting mode explicitly enabled |
| Interrupted provider sync | Retry/manual-review distinction | Some states need human action |
| Provider compensation failure | Fail-closed/no false success mapping | External side effects may need reconciliation |
| Missing Shopify mapping | Local incomplete status | Not a live Shopify health check |
| Missing Advertising mapping | Eligibility/status projection | Not campaign performance |
| Health source unavailable | Restricted/unavailable status | Not an automatic repair |
| Test incomplete checkout | No recovery Order from timeout | Specific Shopify COD path |
| Local product edit vs provider sync | Local transaction then external sync | No cross-system atomic commit |

## 16. Current strengths ranked by evidence and strategic value

**1 — Product identity carries into operational work (High).** Product/Variant and Store scope remain relevant in Orders, Confirmation and reservations. Differentiates the narrative from a standalone catalog UI; not necessarily unique to Wossol in the market.

**2 — Stock truthfulness and reservation-aware decisions (High).** Unknown quantities, pending consumption and active reservations are distinguished. More defensible than vague “real-time inventory.”

**3 — Explicit Test/Real behavior in verified paths (High).** Test Product is a functional classification rather than a naming convention. Qualification required for Confirmation Upsell boundary.

**4 — Provider failure transparency (High).** Retryable vs manual review, partial compensation and attention states address operational uncertainty. Not universal automatic repair.

**5 — Cross-channel connection visibility (Medium–High).** Provider, Shopify and Advertising relationships appear around the Product with separate semantics and permissions. Health is not live verification of all external systems.

**6 — Upsell-commercial/inventory reconciliation (High).** Confirmation Upsell ties row total, OrderItem, reservation, order total and audit; requires end-to-end rollback acceptance.

**7 — Scoped management and audit (Foundational).** Important for trust, but not by itself a distinctive market position.

## 17. The effort-compression analysis

The strongest credible labor-saving hypotheses are:
1. Fewer manual matches between Product/Variant and Order line.
2. Less cross-screen investigation of external mapping status.
3. Less ambiguity when stock is unknown rather than zero.
4. Fewer manual adjustments when Confirmation Upsell quantity changes.
5. Fewer repeated retries of uncertain provider operations.
6. Less risk of mixing Test checkout behavior with ordinary sales in verified paths.

These are **mechanism-based benefits**, not measured time savings. No verified benchmark supports “X hours saved” or “Y% fewer errors.” Such claims require merchant research or instrumented usability studies.

## 18. Proof and demo plan

| Demonstration | Evidence to capture | Acceptance condition |
|---|---|---|
| Create Product with variants and Store association | UI + backend persisted records | Correct Store/variant scope |
| Unknown stock | Product detail/list + source state | `null` not misrepresented as zero |
| Partial provider sync | Simulated provider failure + recovery UI | No false success; action type legible |
| Shopify mapping health | Local mapped/unmapped cases | Label explains local completeness |
| Advertising mapping health | Eligible product-level vs variant-level cases | `COMPLETE` semantics correct |
| Test Product manual Order | Product + Order screenshots | No stock reservation and correct classification |
| Test Shopify checkout timeout | Session and Orders evidence | No `INCOMPLETE_CHECKOUT` recovery Order |
| Confirmation Upsell add/change/remove | Order totals, OrderItems, reservations, timeline | All affected values reconcile |
| Insufficient Upsell stock | Error + DB state | No unintended partial changes |
| Unauthorized Store/section | Permission-bound UI/API | No cross-store product selection |

Use a dedicated test database and test provider environment. Never imply a scenario was executed simply because it is specified here.

## 19. Website messaging opportunities

### Primary story: Products connected to operations
**Draft:** “Manage products and variants in Wossol, with their identities carried into orders and selected downstream workflows.”  
**Proof:** Product/Variant → Order → Confirmation → reservation.

### Secondary story: Know what is connected
**Draft:** “See the state of selected product connections to your provider, Shopify and advertising mappings, including where attention is needed.”  
**Proof:** scoped connection-health UI.  
**Qualification:** connection state is not a live guarantee of all external services.

### Secondary story: Keep stock decisions grounded
**Draft:** “Distinguish known availability from missing stock information, and account for active reservations in supported workflows.”  
**Proof:** product projection and inventory policy.

### Secondary story: Test without treating test activity as ordinary sales
**Draft:** “Use explicitly classified Test Products and Test Orders, with separate behavior in supported order and Shopify COD workflows.”  
**Proof:** test classification and incomplete-checkout rules.  
**Qualification:** avoid universal isolation claim until all Upsell/ingestion paths are audited.

### Secondary story: Upsells that update the Order
**Draft:** “When supported Confirmation Upsells are changed, the Order’s commercial lines and inventory reservations are reconciled together.”  
**Proof:** add/change/remove transaction and resulting audit.  
**Qualification:** show actual failure acceptance before making strong rollback promises.

**AEO/SEO:** create indexable explanations for “product variants,” “Shopify COD product mapping,” “test products,” “stock reservations,” and “confirmation upsell,” each with problem, concrete workflow, eligibility, and limitations. Do not stuff keywords or invent feature coverage.

## 20. Brand and design implications

**Brand territory:** Connected Control, Operational Clarity, and Effort Compression. Products supports a promise of *continuity of context* more strongly than a promise of effortless automation.

**Visual identity/system implications:**
- Design clear visual distinctions between **Product**, **Variant**, **Store**, **Provider**, and **Channel**; do not collapse different identities into one icon.
- Status language must distinguish **connected**, **partial**, **needs attention**, **unavailable**, **restricted**, and **unknown**.
- Treat Test as a conspicuous functional classification, not a decorative badge.
- Show the relation between Product → Order → Inventory → Confirmation through diagrams and actual UI examples.
- Avoid generic “all-in-one magic” visual metaphors. A coherent identity should convey traceable connections, dependable boundaries and clear next actions.
- A later identity system may use relationship graphics, structured grids, and status hierarchy, but these are **design recommendations**, not existing product features.

## 21. Discovery register and unresolved work

| ID | Finding/question | Status | Required next evidence |
|---|---|---|---|
| P-01 | Scoped Product/Variant list and editing | Verified (code) | UI acceptance for current release |
| P-02 | Unknown stock retained as unknown | Verified (code) | Screenshot with missing mapping |
| P-03 | Test/Real separation in Orders | Verified (code) | Execute representative acceptance |
| P-04 | Test Shopify upsell exclusion | Verified (code/test) | Current deployed checkout |
| P-05 | Test timeout excludes recovery Order | Verified (code/test) | Session integration acceptance |
| P-06 | Provider retry/manual-review distinctions | Verified (code/test) | Provider sandbox failure run |
| P-07 | Partial provider compensation/fail-closed | Verified (selected code/tests) | External-provider integration test |
| P-08 | Shopify health is local projection | Verified (code) | Verify UI explanatory copy |
| P-09 | Advertising mapping completeness semantics | Verified (code) | Mapping fixtures |
| P-10 | Reservation-aware effective stock | Verified (code/test) | DB integration under contention |
| P-11 | Confirmation Upsell reconciles Order and reservations | Verified (code) | Add/edit/remove acceptance |
| P-12 | End-to-end rollback on Upsell failure | Pending | Inject failure in DB integration |
| P-13 | Test/Real guard in Confirmation Upsell | Pending / potential gap | Trace all guards; add regression test |
| P-14 | Universal provider sync atomicity | Rejected | External sync occurs after local commit |
| P-15 | “Every product is fully mapped if advertising says COMPLETE” | Rejected | Product-level mapping can satisfy COMPLETE |
| P-16 | “Unknown inventory means zero” | Rejected | Projection explicitly preserves null |
| P-17 | Real-time live Shopify health | Rejected | Batch health is local projection |
| P-18 | Quantified merchant time savings | Pending | Measured merchant study |
| P-19 | Production availability and feature entitlements | Pending | Deployed release and plan audit |
| P-20 | Current UX friction and screenshot validation | Pending | Merchant UI screenshots |
| P-21 | Historical intelligence cross-check | Pending | Retrieve prior intelligence branch/docs and revalidate claims |

## 22. Cross-check against HOME

`HOME.md` describes **Attention Compression** and waiting-for-stock impact. Products contributes the upstream Product/Variant identity and inventory evidence that make selected stock problems explainable. The combined narrative is **Product context → operational impact → prioritized merchant attention**.

Do not claim HOME owns inventory truth; it consumes operational projections. Do not claim Products owns HOME prioritization; the two sections create value through their connection.

## 23. Historical-documents reconciliation and publication limits

The target branch of `jetshop7/wossol-brand-intelligence` currently contains this study directory and `HOME.md`. Historical intelligence from the old project must be located and assessed separately before claiming an exhaustive reconciliation. This document intentionally does not silently import or certify claims from unavailable historical files.

**Do not publish as proven:** autonomous end-to-end commerce, AI optimization, all-provider real-time synchronization, universal Test isolation, guaranteed cross-provider atomicity, universal rollback proof, zero overselling, complete live Shopify health, all variants mapped for advertising, or numerical time savings.

## 24. Next acceptance and completion gate

This is a substantive evidence baseline, **not a claim that the mandatory deep-study protocol is fully satisfied**. Before declaring Products fully closed:
1. Trace Confirmation Upsell Test/Real boundary and add targeted regression coverage if needed.
2. Execute reservation/Confirmation transaction-failure integration acceptance.
3. Test provider partial failure and recovery in an isolated environment.
4. Inspect current UI/screenshots for merchant-facing accuracy and friction.
5. Retrieve old intelligence documents from the historical repository and reconcile each relevant assertion.
6. Record exact tested commit, environment, screenshots, and test results.
7. Revisit Website claims after those acceptance results.

**Decision:** Products is a connected operational identity layer with credible strengths in truthful state, scoped context and cross-workflow consistency. Its brand should express those demonstrated strengths without promising more automation or reliability than the verified code supports.
