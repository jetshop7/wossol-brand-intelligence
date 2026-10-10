# Products V2 — Capability Coverage Matrix

**Status:** REOPENED — V2 AUDIT IN PROGRESS. **Source:** `jetshop7/wossol-platform` / `dev/wossol-integration` / `bf2d82f46d3189250e2a0ced6e6010d85c4de699` (2026-10-10). **Method:** independent code discovery before historical reconciliation. No runtime or screenshot verification performed in this pass.

## Discovery footprint
- Git tree enumerated: **2,202 entries**; **154 TypeScript/TSX/Prisma paths** matched the initial Shopify/COD/Product/Variant/Mayar/payment search. This is a **candidate set, not 154 inspected files**, and the keyword filter is not exhaustive.
- Initial source excerpts inspected: `merchant/applications/shopify/cod/page.tsx`, `merchant/products/ProductConnections.tsx`, `shopify-embedded-product-form-configuration.service.ts`, `shopify-cod-product-configuration.service.ts`.
- The merchant COD route currently renders a **Shopify Admin redirect/instruction view**; its longer editor component remains in the file but is **not mounted** by the default export. Do not describe that editor as active.
- Shopify COD product configuration service requires an active connection and checks Product/Variant mapping readiness before enabling COD. This is static source evidence only.

## Capability matrix
| ID | Capability | Merchant/UI entry | Backend / system evidence | Journey | Evidence | Status | Merchant value / unresolved work |
|---|---|---|---|---|---|---|---|
| P-V2-001 | Product list/create/edit and variants | `merchant/products/page.tsx`, `create/page.tsx`, `edit/page.tsx`, `variant-edit/page.tsx` | `products.service.ts`, Products controllers | MJ-01 | Candidate paths; prior audit | DISCOVERED | Trace full create/edit lifecycle and UI |
| P-V2-002 | Provider Accurate Mayar activation/sync | Product create/edit; provider status | `products.service.ts`, `product-provider-sync-state.ts`, external integration | MJ-01 | Candidate paths; prior audit | DISCOVERED | Trace success, partial failure, retry, compensation |
| P-V2-003 | Images and variant media | Product image upload / variant image | `product-image-source.service.ts`, `product-variant-image-storage.service.ts` | MJ-01 | Candidate paths | DISCOVERED | Check UI and provider/Shopify transfer |
| P-V2-004 | Category taxonomy | Product create/edit | `product-taxonomy.service.ts` | MJ-01 | Candidate paths | DISCOVERED | Verify active/superseded category UX |
| P-V2-005 | Payment policy | Product settings | Product payment policy tests/services | MJ-01 | Candidate paths | DISCOVERED | Trace COD/electronic choices and Order snapshots |
| P-V2-006 | Shopify connection and Store | `merchant/applications/shopify/page.tsx`; Shopify app | `shopify.service.ts`, controller | MJ-02 | Candidate paths | DISCOVERED | Inspect OAuth, linking, role/scope, failure |
| P-V2-007 | Shopify Product mapping and creation | `ProductConnections.tsx` | `shopify-product.service.ts` | MJ-02 | Source excerpt S1 | PARTIAL | Unpublished draft, manual mapping, correction and recovery |
| P-V2-008 | Exact Shopify Variant mapping | `ProductConnections.tsx` | `shopify-product.service.ts` | MJ-02 | Source excerpt S1 | PARTIAL | Missing variant sync, explicit variant selection and safeguards |
| P-V2-009 | Shopify COD enablement/readiness | Shopify Admin → Apps → Wossol; merchant route points there | `shopify-cod-product-configuration.service.ts` | MJ-03 | Source excerpt S1 | PARTIAL | Requires connection and mapping readiness; verify live merchant app |
| P-V2-010 | Per-Product COD form labels, fields, order, appearance | Shopify Embedded Product UI (to trace) | `shopify-embedded-product-form-configuration.service.ts` | MJ-03 | Source excerpt S1 | PARTIAL | Customizable fields/labels/styles exist in service; trace active UI and storefront |
| P-V2-011 | COD form customer-facing rendering | Shopify storefront extension (to locate) | Shopify checkout/session code | MJ-04 | Candidate code | DISCOVERED | Identify actual extension and customer interactions |
| P-V2-012 | COD offers, presets and upsells | Shopify app + customer form | `shopify-cod-offers-upsells.service.ts`, `shopify-cod-preset.service.ts` | MJ-03/MJ-04 | Candidate paths | DISCOVERED | Validate Test/Real, pricing, controls, sequence |
| P-V2-013 | COD checkout → Order | Customer form → Orders | `shopify-cod.service.ts`, session processor | MJ-04 | Candidate paths; prior audit | DISCOVERED | Test/Real, timeout, deduplication, source snapshots |
| P-V2-014 | Confirmation upsell → inventory | Confirmation UI | `confirmation.service.ts`, reservation service | MJ-05 | Prior source inspection | PARTIAL | RISK-001 and RISK-002; recheck |
| P-V2-015 | Product connection health | Product detail | `ProductConnections.tsx`, connection health service | MJ-02 | Source excerpt S1 | PARTIAL | Differentiate partial, unavailable and restricted |
| P-V2-016 | Advertising mapping | Product detail | `ProductConnections.tsx`, advertising projections | MJ-06 | Source excerpt S1 | PARTIAL | Trace campaign attribution and permissions |
| P-V2-017 | Product-level delivery policy | Shopify embedded + merchant | `shopify-embedded-product-delivery-policy.service.ts` | MJ-03 | Candidate path | DISCOVERED | Trace delivery options, destination and downstream Order |
| P-V2-018 | History, audit, archival and deletion | Product detail / edit | Products controllers/service | MJ-01 | Prior audit | DISCOVERED | Verify exact lifecycle, guards, audit |
| P-V2-019 | Multi-store product association | Store selector, Product details | ProductStore models and controllers | MJ-01/MJ-02 | Prior audit | DISCOVERED | RISK-004; distinguish read from mutation |
| P-V2-020 | Shopify embedded navigation and ownership | `app/shopify/app/route.ts`, `shopify/continue/route.ts` | Shopify auth/routes | MJ-03 | Candidate paths | DISCOVERED | Trace actual merchant navigation rather than inactive editor |

## Gate A — Remaining
- Inspect all candidate paths and reverse consumers, not merely filename hits; locate Shopify extension files outside TS/TSX.
- Read active embedded Shopify merchant UI, storefront form UI and the routes that connect them.
- Identify models, DTOs, API endpoints, tests, permissions, jobs, events and all significant error paths.
- No historical-document comparison until independent inventory is sufficiently complete.
- **Closure: FAIL / IN PROGRESS**. This matrix is an initial, explicitly incomplete inventory.


## Second-pass discovery additions (2026-10-10)

| ID | Capability | Evidence | Journey | Status | Next proof |
|---|---|---|---|---|---|
| P-V2-021 | Shopify App Home as App Bridge HTML route | `apps/frontend/src/app/shopify/app/route.ts` | MJ-03 | S1 PARTIAL | Read `public/shopify-app-home.js`, auth, API and tests |
| P-V2-022 | Shopify-initiated secure Store selection | `apps/frontend/src/app/shopify/link/page.tsx` | MJ-02 | S1 PARTIAL | Validate proof expiry, claims and scope in backend |
| P-V2-023 | Real Shopify Product-template COD Theme App Extension | `extensions/wossol-cod-form/blocks/wossol-cod-form.liquid` | MJ-04 | S1 PARTIAL | JS runtime, Shopify theme installation, browser test |
| P-V2-024 | Multi-variant/quantity composition on customer form | Same Liquid block | MJ-04 | S1 PARTIAL | JS events, server quote and order line validation |
| P-V2-025 | Customer-facing offer selection and dynamic price breakdown | Same Liquid block; `shopify-cod.service.ts` quote/bootstrap | MJ-04 | S1 PARTIAL | Pricing consistency and full checkout trace |
| P-V2-026 | Customer upsell modal with image gallery, variant and quantity | Same Liquid block | MJ-04 | S1 PARTIAL | Runtime, availability, price and accepted upsell flow |
| P-V2-027 | Shopify App Proxy storefront bootstrap and provider-neutral order adapter | `shopify-cod.service.ts` | MJ-04 | S1 PARTIAL | Checkout processor and canonical Orders handoff |

**Discovery correction:** The Shopify COD Form is not just a planned feature: a concrete Shopify Theme App Extension block and storefront adapter exist in source. Actual deployment, merchant enablement, successful execution and marketing claims remain unverified.


## Checkout-session evidence expansion (2026-10-10)

| ID | Capability | Code evidence | Status | Open question |
|---|---|---|---|---|
| P-V2-028 | App Proxy bootstrap/quote/preflight/session/intent/upsell-decision endpoints | `shopify.controller.ts` | S1 TRACED | Runtime storefront invocation order |
| P-V2-029 | Bounded persisted COD continuation, 30-minute TTL | `shopify-cod.service.ts` | S1 PARTIAL | Database persistence and restart tests |
| P-V2-030 | Freeze base quote and Upsell sequence before Order | `confirmCheckoutIntent` | S1 PARTIAL | Exact session transition and concurrency outcomes |
| P-V2-031 | Upsell cursor and replay conflict handling | `decideCheckoutUpsell`; `shopify-cod-checkout-session.spec.ts` | S1 + S2 TEST DEFINED | Execute focused tests; inspect all variant and price guards |
| P-V2-032 | Due-session recovery and lease-aware retry | `shopify-cod-checkout-session.processor.ts`; service `processDueCheckoutSessions` | S1 + S2 TEST DEFINED | Execute tests, verify process restarts and error recovery |
| P-V2-033 | Fail-closed legacy direct submit | `ShopifyCodService.submit` | S1 VERIFIED SOURCE | Verify deployed runtime compatibility |
| P-V2-034 | Test Product-specific timeout and Upsell exclusion | `shopify-cod-checkout-session.spec.ts` | S1 + S2 TEST DEFINED | Run tests, verify downstream Test Order handling |

**Important:** “test defined” is not “test executed/passed”. Source claims of exactly-once are implementation intent pending transaction/ingestion verification.


## Finalization-to-Orders findings (2026-10-10)

| ID | Capability | Evidence | Status | Unverified |
|---|---|---|---|---|
| P-V2-035 | Session-bound stable external order identity | `ShopifyCodService.finalizeCheckoutSession` | S1 TRACED | Database replay test |
| P-V2-036 | Commerce scope and exact Product/Variant resolution | `CommerceOrderResolutionService.ingestNormalizedCommerceOrder` | S1 TRACED | Live mismapping test |
| P-V2-037 | Transactional mapping lookup + canonical Order + mapping insert | `OrdersService.createCommerceImportedOrder`, `createOrderWithStockAllocationPolicy` | S1 TRACED | DB uniqueness and concurrent tests |
| P-V2-038 | Inventory reservation / waiting-for-stock inside Order transaction | `OrdersService.createOrderWithStockAllocationPolicy` | S1 TRACED | Stock shortage and rollback execution |
| P-V2-039 | Test Product reservation bypass | Same Orders service | S1 TRACED | Test Order downstream visibility |
| P-V2-040 | Completed vs incomplete checkout capture provenance | Shopify finalization → Commerce → Orders | S1 TRACED | Merchant-facing classification and recovery |
| P-V2-041 | Post-commit best-effort Shopify projection | Shopify COD finalization | S1 TRACED | Provider failures and recovery queue |

**Correction to prior open question:** transactional Commerce deduplication is now visible in Orders source: mapping lookup, Order creation and mapping insertion share a Serializable transaction. Do not claim runtime exactly-once until unique constraints and tests are verified.


## Database and merchant Orders projection — 2026-10-10

| ID | Capability | Evidence | Grade / status | Next verification |
|---|---|---|---|---|
| P-V2-042 | Unique Commerce external Order identity per connection | Prisma `CommerceOrderMapping @@unique([commerceConnectionId, externalOrderId])` | S1 SCHEMA VERIFIED | Applied migration, physical concurrent inserts |
| P-V2-043 | One Commerce mapping per canonical Order | Prisma `CommerceOrderMapping @@unique([orderId])` | S1 SCHEMA VERIFIED | Applied database constraints |
| P-V2-044 | Checkout session token and finalized Order uniqueness | Prisma `CommerceCodCheckoutSession` unique token and finalizedOrderId | S1 SCHEMA VERIFIED | Runtime race/rollback tests |
| P-V2-045 | Incomplete checkout origin badge in Merchant Orders list/detail | `merchant/orders/page.tsx`, `detail/page.tsx` | S1 UI SOURCE VERIFIED | Browser screenshots and actual order projection |
| P-V2-046 | Recovered-from-incomplete label after confirmation | Same UI files; `checkout-origin-projection.spec.ts` | S1 + test defined | Execute UI test, check lifecycle semantics |
| P-V2-047 | Waiting-for-stock filter and order state | `merchant/orders/page.tsx`, `OrdersService` | S1 UI SOURCE VERIFIED | Live list/filter with waiting stock |

**Caution:** Existing checkout-session unit tests include mocked DB and projection services. Test presence does not prove applied constraints, successful test runs or real concurrent PostgreSQL behavior.


## Products merchant UI and migrations — 2026-10-10

| ID | Capability | Static evidence | Status | Next |
|---|---|---|---|---|
| P-V2-048 | Create Product with option-group combinator | `merchant/products/create/page.tsx`; groups color/size/material/gender/custom | S1 UI CODE | Validate combination cap, duplicate handling and save payload |
| P-V2-049 | Product image selection/upload in create/edit | `ProductImageUploadSection` imported in create/edit | S1 UI CODE | Trace upload API, storage, ordering and provider propagation |
| P-V2-050 | Category selection from active taxonomy | create/edit `fetchActiveProductCategories` | S1 UI CODE | Trace backend taxonomy and invalid category |
| P-V2-051 | Edit Product with provider Variant sync confirmation | `edit/page.tsx` `ProviderVariantSyncDialog` | S1 UI CODE | Follow save/sync outcomes and cancellation |
| P-V2-052 | Variant editing with independent image handling | `variant-edit/page.tsx` | S1 UI CODE | Trace image upload/remove and variant authorization |
| P-V2-053 | Product list variants and per-item actions | `merchant/products/page.tsx` | S1 UI CODE | Follow delete guards and list filters |
| P-V2-054 | Checkout session migration foundation | `20260927_shopify_cod_checkout_session_v1/migration.sql` | S1 MIGRATION FILE | Verify applied in target DB |
| P-V2-055 | Checkout optimistic revision, cursor, scoped FK hardening | `20260928_shopify_cod_checkout_session_hardening_v1/migration.sql` | S1 MIGRATION FILE | Verify applied in target DB |

**Important:** the repository also contains migration directories dated after the study date; their presence does not establish deployment or execution. No database or test runner was accessed.


## Product creation and media persistence — 2026-10-10

| ID | Capability | Code evidence | Grade | Gap |
|---|---|---|---|---|
| P-V2-056 | Authenticated Store-scoped Product creation via POST merchant-create | `create/page.tsx`, `products.controller.ts`, `ProductsService.createMerchantProduct` | S1 TRACE | API/runtime test |
| P-V2-057 | Atomic Product, Variant, Store link, taxonomy and audit creation | `ProductsService.createMerchantProduct` Prisma transaction | S1 TRACE | Rollback and concurrent code generation tests |
| P-V2-058 | Inactive Product/Variant foundation before Accurate/Mayar sync | Same service; audit metadata REQUIRED_BEFORE_ACTIVATION | S1 TRACE | Activation lifecycle and failure UX |
| P-V2-059 | Five Product images, 2MB each, validated MIME and local storage | `ProductImageUploadSection.tsx`, `ProductsController`, `ProductsService.uploadMerchantProductImage` | S1 TRACE | Orphan file cleanup, deployment persistence |
| P-V2-060 | Variant image stage/move/DB update with best-effort old binary cleanup | `ProductsService.uploadMerchantVariantImage` | S1 TRACE | Failure injection, rollback and cleanup |
| P-V2-061 | Delete guard on historical Order usage; provider-first delete then archive | `ProductsService.deleteMerchantProduct` | S1 TRACE | Provider partial success and local transaction failure |

**New risks for shared register:** Product image uploaded before Product creation uses `product-images/<merchantId>/new-product` and may be orphaned on cancelled/failed creation; provider deletes occur before local archive transaction, creating a possible partial-success divergence. Neither is a confirmed production defect. Product image UI explicitly states images are Wossol-only and not propagated to external systems.


## Merchant-value qualification rule — approved 2026-10-10

**Study objective:** extract differentiated, evidenced benefits that the merchant/customer experiences and that can legitimately inform public website messaging, positioning, verbal identity and visual identity. Wossol-internal capabilities are not strengths for this purpose.

- API integrations, carrier/provider identities, internal orchestration, infrastructure, database architecture and operational advantages to Wossol are **technical evidence only**, not merchant-facing strengths. Do not disclose the identity or existence of the delivery provider in merchant-facing claims.
- A capability qualifies as a merchant-value candidate only when a specific merchant job/problem, observable outcome, credible proof and communication-safe wording are established.
- Translate backend mechanics into merchant outcomes only when evidence supports the outcome (e.g. reliable Order handling, fewer duplicate Orders, simpler Product management); do not claim measured speed, error reduction or reliability without measurement.
- Maintain three classifications for every finding: **Merchant-visible value candidate**, **technical enabler only**, or **unverified hypothesis**. Technical enablers may remain in cross-system audit but must not be promoted into strengths/website/identity.
- Provider sync analysis remains necessary to validate real Product behavior and failure modes, **not** to promote delivery-provider/API integration as a Wossol customer benefit.


## Provider lifecycle — merchant-value screen (2026-10-10)

| ID | Observed capability | Evidence | Merchant-value classification | Proof needed |
|---|---|---|---|---|
| P-V2-062 | Product activates only after all Variant links complete | `ProductsService.createMerchantProduct` and `finalizeMerchantProductCreate` | Merchant-value candidate: avoid falsely ready products | Live multi-variant acceptance |
| P-V2-063 | Failed multi-variant creation attempts compensation and archives local foundation when successful | `compensateNewAccurateMayarProductsAfterFailedCreate`, `archiveCreatedProductAfterFailedProviderSync` | Technical enabler; customer outcome still unverified | Failure injection and merchant UI |
| P-V2-064 | Compensation failure leaves inactive audit evidence | `recordMerchantProductCreateCompensationFailure` | Technical enabler; possible customer protection | Reconciliation and UI proof |
| P-V2-065 | Merchant-safe success response omits providerSync implementation | `createMerchantProduct`, `product-accurate-mayar-partial-sync.spec.ts` | Merchant-facing UX candidate; provider details not a marketing strength | Live response and UI |
| P-V2-066 | Variant recovery distinguishes safe retry from review-required | `retryMerchantVariantProviderSync`, `product-provider-sync-state.ts` | Merchant-value candidate: clear recovery action | Real UI affordance and retry behavior |
| P-V2-067 | Stale provider claim projected to merchant attention | `resolveMerchantProviderSyncState` | Technical enabler until actual actionable UI proven | List/detail status and screenshot |

**Marketing exclusion:** Do not present delivery-company API integration, Accurate/Mayar provider identity or internal orchestration as customer benefits. Distill only verifiable merchant outcomes; avoid unsupported zero-error/zero-loss claims. Test files are defined but not executed in this study.


## Merchant-visible recovery audit — 2026-10-10

| ID | Evidence | Classification | Validation |
|---|---|---|---|
| P-V2-068 | `provider-sync-recovery-ui.spec.ts` explicitly asserts no internal recovery status or Retry controls on merchant Products page | INTERNAL ONLY; EXCLUDE FROM MARKETING | Static test not executed; no live UI |
| P-V2-069 | Product list attention badge routes to detail or Shopify Connections | MERCHANT-VISIBLE CANDIDATE | Verify alert priority and click |
| P-V2-070 | Product Detail warns on unmapped or partially mapped Shopify Product and opens Connections | MERCHANT-VISIBLE CANDIDATE | Live UI and permissions |
| P-V2-071 | Connections displays Shopify mapping counts and missing Variant names | MERCHANT-VISIBLE CANDIDATE | Verify current status accuracy |
| P-V2-072 | Connections offers create unpublished Shopify draft, link existing Product, manual Variant link and missing Variant sync | MERCHANT-VISIBLE CANDIDATE | Live end-to-end outcomes |
| P-V2-073 | Changing/ending Shopify Product link requires explicit confirmation; end does not delete remote Product | MERCHANT-VISIBLE CANDIDATE | Check partial failures and recovery |

**Correction:** Do not suggest that merchant can use the internal Accurate/Mayar retry path in Products UI. Its absence is intentional per static test definition. Shopify connection controls are separate merchant-visible functionality; their value must be tested and framed without promises of automatic publication.


## Daily merchant Product management — 2026-10-10

| ID | Code-backed finding | Merchant-value screen | Remaining proof |
|---|---|---|---|
| P-V2-074 | Product list searches Product/name/code/Variant/SKU and filters category/Shopify state; sorts age/name | Merchant-visible candidate: find and organize catalog | Runtime pagination/search scope |
| P-V2-075 | Product edit generates missing option combinations while skipping existing signatures | Merchant-visible candidate: manage multi-option catalog | Edge cases, duplicate rules and performance |
| P-V2-076 | Per-Variant name/SKU/price/weight PATCH | Merchant-visible candidate: individual control | Backend validation and UI acceptance |
| P-V2-077 | Product edits and electronic payment settings save in separate requests with partial-success message | Merchant-visible UX; not yet a selling point | Verify retry semantics and consistency |
| P-V2-078 | Variant catalog edits and image upload/removal are separate requests | Merchant-visible capability with partial-success risk | Failure injection and merchant feedback |
| P-V2-079 | Product and Variant images have distinct upload paths; new Variant image requires single-combination creation | Merchant-visible capability with constraints | Image persistence and accessibility |

**Do not claim** bulk editing, speed advantage, reduced errors or superior competitor experience from these sources. Batch generation of missing combinations is not bulk edit of existing Variants. No live UI or tests executed.


## Inventory and deletion merchant-value review — 2026-10-10

| ID | Code evidence | Classification | Caveat |
|---|---|---|---|
| P-V2-080 | Product list sums effective available quantities across non-archived Variants | Merchant-visible value candidate | Any unknown Variant quantity makes total `Unknown` |
| P-V2-081 | Merchant can refresh Product quantities; UI reports updated/skipped/failed or BUSY with cached quantities | Merchant-visible value candidate | Freshness, accuracy and provider failure need runtime proof |
| P-V2-082 | Product/Variant deletion blocks historical OrderItem references before external calls | Merchant-protection candidate | Deletion restrictions may frustrate catalog maintenance; no competitive differentiation established |
| P-V2-083 | Delete eligibility verified before local archive; UI warns available quantity must be zero | Merchant-protection candidate | Backend uses deletability verification, not only displayed numeric quantity |
| P-V2-084 | Variant deletion archives Variant and automatically archives Product if final non-archived Variant | Merchant-visible behavior, not automatically a strength | Verify expectation and messaging |
| P-V2-085 | Product deletion archives Product and all non-archived Variants, not physical deletion | Internal safety enabler / customer transparency | Provider-first sequential delete consistency window (RISK-008) |

**Marketing screen:** Product quantity visibility and actionable refresh are stronger customer-value candidates than provider integration. Do not claim real-time stock, perfect accuracy, inventory automation or safe deletion under every failure condition without end-to-end proof.


## Cross-system stock allocation — 2026-10-10

| ID | Code-backed behavior | Merchant-value qualification | Required verification |
|---|---|---|---|
| P-V2-086 | Effective available stock subtracts active reservations and pending consumption; unknown stock remains unknown | Candidate: clearer availability and fewer false stock assumptions | Freshness and live stock projections |
| P-V2-087 | Order reservation locks Variant rows, aggregates demand by Variant and checks effective availability | Technical enabler for merchant outcome | Concurrent PostgreSQL execution and all order channels |
| P-V2-088 | Insufficient stock either blocks reservation or yields WAITING_FOR_STOCK depending on allocation policy | Candidate: explicit handling of unavailable stock | Merchant journey and channel policy |
| P-V2-089 | Waiting-stock promotion rechecks and reserves full stock within Serializable transaction | Technical enabler for order recovery outcome | Scheduler/events and concurrency |
| P-V2-090 | Successful promotion moves Order to PENDING_CONFIRMATION with merchant-safe timeline event | Merchant-visible candidate: continuity from waiting to confirmation | UI and operational confirmation handoff |
| P-V2-091 | Stock snapshot can emit availability-increased event; promotion service processes waiting orders in bounded batches | Technical enabler for eventual recovery | Trigger consumer, retries and scheduling |

**Claims restriction:** Do not promise zero overselling, no lost orders, guaranteed automatic promotion or real-time stock. Sources are code paths; no live acceptance tests executed.


## Waiting-stock channel and recovery verification — 2026-10-10

| ID | Code evidence | Merchant-value status | Verification gap |
|---|---|---|---|
| P-V2-092 | Canonical commerce Order create uses allowWaitingForStock allocation policy | Merchant outcome candidate | Each integration entry point and real order acceptance |
| P-V2-093 | Standard Order creation persists WAITING_FOR_STOCK and excludes it from Confirmation workload | Merchant outcome candidate | Merchant creation modes and blocked/test exceptions |
| P-V2-094 | Order import classifies VALID_WAITING_FOR_STOCK and can create waiting Orders | Merchant outcome candidate | Workbook UI and import acceptance |
| P-V2-095 | Background processor polls every 30 seconds when writes enabled and consumes availability events with durable checkpoint | Internal enabler only | Deployment runtime, event replay and outage recovery |
| P-V2-096 | Recovery sweep every 10 minutes scans non-test WAITING_FOR_STOCK Orders | Internal enabler only | Actual scheduling, batch coverage and fairness |
| P-V2-097 | Orders list exposes Waiting for Stock filter | Merchant-visible capability | Detail/status clarity and merchant actions |

**No claims of universal channel coverage or guaranteed timing.** Code contains separate blocked-customer and test-order paths. Source inspected, tests not run.


## Merchant waiting-stock UX — 2026-10-10

| ID | Code-backed behavior | Value classification | Remaining proof |
|---|---|---|---|
| P-V2-098 | Orders list has explicit Waiting for Stock filter | Merchant-visible | Real UI and discoverability |
| P-V2-099 | Order Detail presents merchant-safe current status and tracking/activity history | Merchant-visible | Verify stock allocation event renders in actual journey |
| P-V2-100 | Waiting-for-stock Order is merchant-editable before dispatch | Merchant-visible | Actual edit and reservation recalculation |
| P-V2-101 | Waiting-for-stock Order is cancellable before dispatch | Merchant-visible | Cancellation behavior and inventory release |
| P-V2-102 | Promotion emits merchant-safe `Stock allocated` event and moves Order to Pending Confirmation | Merchant-visible candidate | End-to-end transition and notification discoverability |
| P-V2-103 | No dedicated manual stock-retry control found in inspected Order Detail UI | UX limitation | Check other routes and operations before universal claim |

**Merchant promise candidate:** keep shortage-affected Orders visible and manageable while waiting for full stock, then return them to Confirmation when available. Avoid promising user-triggered recovery, guaranteed timing, or that no Order is lost.


## Waiting-stock edge cases — 2026-10-10

| ID | Code observation | Merchant-facing assessment | Validation |
|---|---|---|---|
| P-V2-104 | Editing a waiting Order computes changed stock demand and resets waiting priority timestamp if demand changes | Important operational behavior; not automatically a benefit | Verify merchant expectations and FIFO fairness |
| P-V2-105 | After waiting-Order edit, service directly invokes promotion attempt | Merchant outcome candidate | Edit with partial/full stock and failure behavior |
| P-V2-106 | Pre-dispatch cancellation releases reservations transactionally and records merchant-safe cancellation history | Merchant-visible protection | Real cancellation and reservation state |
| P-V2-107 | Waiting Order promotion requires full demand allocation; partial availability remains BLOCKED | Important constraint | Multi-Variant partial restock journey |
| P-V2-108 | Promotion processor catches individual failures, logs and retries availability wake-up events | Internal reliability enabler | Persistent failures, event checkpoint, retries |
| P-V2-109 | Waiting promotion uses FIFO order by waitingForStockAt and bounded batches of 100 | Operational fairness candidate | Starvation, repeated failures and scale |

No runtime tests executed. Do not market partial-stock fulfilment, guaranteed fairness, guaranteed recovery or immediate promotion.


## Waiting-stock editing clarity and test-evidence review — 2026-10-10

| ID | Source-supported finding | Classification | Verification needed |
|---|---|---|---|
| P-V2-110 | Editing Order form redirects to Order Detail after successful update, without an identified stock-priority explanation in submit flow | Merchant UX clarity gap | Live UI, notices elsewhere |
| P-V2-111 | Backend compares stock-demand signature; changed demand resets waiting priority timestamp, unchanged demand retains it | Operational policy, not automatic differentiator | Fairness rationale and merchant disclosure |
| P-V2-112 | Promotion service spec defines PROMOTED, BLOCKED, FIFO mocked scenarios | Test-definition evidence only | Execute tests, physical database |
| P-V2-113 | Processor spec defines transient event retry, lease recovery, malformed events, bounded recovery mocked scenarios | Test-definition evidence only | Execute tests, restart/outage recovery |

Do not claim priority policy is merchant-explained or fairness is proven. Test definitions were read, not run.
