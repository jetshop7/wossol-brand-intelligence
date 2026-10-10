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
