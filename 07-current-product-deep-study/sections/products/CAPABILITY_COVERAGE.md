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
