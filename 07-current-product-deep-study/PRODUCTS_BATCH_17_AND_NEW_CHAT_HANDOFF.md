# WOSSOL PRODUCTS — BATCH 17 & NEW CHAT CONTINUITY HANDOFF
**Status:** ACTIVE research, not final PRODUCTS.md. **Date:** 2026-10-09.
**Purpose:** Preserve batch 17 from the conversation reaching its length limit, and give the next assistant an exact restart plan.
**Repositories:** source `jetshop7/wossol-platform` branch `dev/wossol-integration`; Brand Intelligence `jetshop7/wossol-brand-intelligence` branch `main`.
**Evidence note:** The findings below are faithfully transferred from the user-supplied transcript of the prior assistant's batch 17. The present handoff has NOT independently re-run its code inspection or executable acceptance tests. Recheck the current source before expanding or using in public copy.

## Read first, in this order
1. `07-current-product-deep-study/CURRENT_PRODUCT_DEEP_STUDY_PROTOCOL.md`
2. `07-current-product-deep-study/DEEP_STUDY_EXECUTION_AND_QUALITY_GATES.md`
3. `07-current-product-deep-study/PRODUCTS_WORKING_EVIDENCE_AND_CROSS_SECTION_REGISTER.md` — comprehensive accumulation for batches 01–12 (~124k chars at handoff).
4. `07-current-product-deep-study/PRODUCTS_QA_TEST_ORDERS_AND_FIFO_COVERAGE_V0.1.md` — prior QA evidence.
5. `07-current-product-deep-study/PRODUCTS_RESEARCH_CONTINUITY_BATCHES_13_TO_16_AND_NEXT.md` — consolidated and saved previous session; commit `1f7811fb25a8afcfaebb798546446fcea4e1cc01`.
6. THIS file — batch 17 and exact restart.
Read all, not merely titles. If oversized, read sequentially. GitHub is persistent memory, not the chat transcript.

## BATCH 17 — Free offers × inventory × financial metrics × upsell origin

### B17-01 — Free Variant units still reserve physical stock
Previous assistant reported tracing `FREE_VARIANT_UNITS` from Shopify COD to canonical OrderItems and Inventory reservation. Free reward line has preserved Variant ID, quantity and `unitPrice=0.00`; stock reservation is by Variant identity and quantity, NOT sale price. Example: 2 paid units plus 1 free unit of same Variant require 3 physical units. Reservation occurs during eligible Order creation, not offer preview. When unavailable, Order may become `WAITING_FOR_STOCK`. Classification from previous chat: Verified Compound Advantage, Products × Offers × Orders × Inventory; **handoff evidence level:** previous source inspection, recheck code/tests before public claim.
Source paths:
- `apps/backend/src/modules/shopify/shopify-cod.service.ts` lines ~445–480
- `apps/backend/src/modules/orders/orders.service.ts` lines ~1330–1430
- `apps/backend/src/modules/inventory/inventory-reservation.service.ts` lines ~270–421

### B17-02 — Potential discount allocation / Product revenue overstatement
Previous assistant reported that Shopify COD commercial calculator reduces order-level subtotal for qualifying percentage/fixed Offers, but base `OrderItem.totalPrice` may remain Shopify list unit price × quantity; Product Profitability Analytics uses `OrderItem.totalPrice` when collection is present, rather than a verified allocated post-discount item amount. Example: 2 × 50 = 100 item gross, 10% offer means order subtotal 90, but item totals may still sum to 100. **Classification:** HIGH PRIORITY source-level accounting consistency risk; **NOT** executed financial acceptance test or measured production defect. May affect Product/Variant profitability and Product-attributed Advertising ROAS, while Finance collection reflects actual collected total. Discount allocation across multiple items requires explicit financial rule, not ad-hoc correction. Investigate actual paths and tests.
Source paths:
- `apps/backend/src/modules/shopify/shopify-cod.service.ts` ~510–538
- `apps/backend/src/modules/orders/orders.service.ts` ~1125–1150
- `apps/backend/src/modules/analytics/merchant-analytics.service.ts` ~1930–2025
- Related Advertising Analytics attribution and Finance collection services to inspect.
**Acceptance fixture:** 2 units at 50, 10% discount, delivery=0; compare Order.subtotalAmount (90 expected), sum OrderItem.totalPrice (potentially 100), Finance collection (actual collected), Product Profitability and Product ROAS. Also test fixed discount and multi-Product allocation. Never claim profitability is precise after all discounts before this passes.

### B17-03 — WAITING_FOR_STOCK recovery
Previous assistant reported source inspection of `WaitingStockPromotionService`: tries to reserve **all** required Order Items in one transaction; availability events trigger work and periodic catch-up checks; successful reserve moves `WAITING_FOR_STOCK` → `PENDING_CONFIRMATION`, not straight to delivery; excludes Test Orders. **Classification:** Verified bounded compound behavior from prior inspection; production timeliness not proven. No instant automatic fulfillment claim.
Sources:
- `apps/backend/src/modules/orders/waiting-stock-promotion.service.ts`
- `apps/backend/src/modules/orders/waiting-stock-promotion.processor.ts`
- `apps/backend/src/modules/orders/waiting-stock-promotion.processor.spec.ts`
**Merchant value:** stock safety for paid, free and Upsell lines; avoids partial stock commitments and reduces manual checking; needs evidence of scheduler/event operation in deployed environment.

### B17-04 — Financial inconsistency can propagate to multiple metrics
Previous assistant found: general revenue may use Finance collection, while Product/Variant revenue and Product-attributed ROAS can sum un-discounted OrderItem totals. Thus one discounted Order can appear correct in Finance but overstated in Product analytics. **HIGH priority pending executable acceptance and exact per-service provenance checks**; do not generalize to all Analytics views.

### B17-05 — Shopify COD Upsell origin missing in Merchant Order Details
Previous assistant reported `commerceCommercialSnapshot` stores Upsell commercial evidence, but Merchant OrderItem origin projection labels `CONFIRMATION_UPSELL` only for Confirmation system; Shopify COD accepted Upsell can appear as `ORIGINAL` alongside ordinary lines. Merchant UI has distinct Confirmation upsells group but not Shopify COD Upsell label. **Classification:** prior-source-inspected Verified UX/Projection Gap; recheck current code and actual UI. Merchant consequence: harder to identify which item was bought because of an Upsell. No measured conversion/revenue claim.
Sources:
- `apps/backend/src/modules/orders/orders.service.ts` ~5295–5320
- `apps/frontend/src/app/merchant/orders/detail/page.tsx` ~210–240
- Shopify COD commercial snapshot flow.

## Critical methodology — user confirmed
- Continue discovering current Products source code and merchant workflows. Do NOT write a document or GitHub commit after every message. Save at substantive milestones only; this handoff is an exceptional milestone because chat context is being lost.
- Study cross-section relationships DURING each Products research batch. If both sides verified, classify verified (with precise bounds). If a downstream module has not been studied comprehensively, mark Pending and reopen when that module's turn comes; do not discard it. Cross-section verification can also confirm previous sections.
- Code-first: frontend, backend, tests, DB, permissions, provider integrations, audit, failures, merchant UI and screenshots. Legacy Brand Intelligence cross-check ONLY after independent Products discovery, and before final document.
- Analyze Feature → Merchant Problem → Mechanism → Manual Work Reduced → Merchant Value → Control/Transparency/Safety → Section Strength → Cross-System Value → Proof/Demo → Marketing/Website → Brand Relevance.
- Distinguish proven current capability, reasoned interpretation, emerging principle, future vision and unverified hypothesis. No invented metrics or production evidence.
- Prioritize merchant strengths and value, not only technical bugs. Brand ≠ logo; do not start naming/colors/logo.
- `PRODUCTS.md` is NOT ready; no premature closure. Do not edit Wossol source code during research unless user separately authorizes.

## Immediate next action after opening new chat
1. Read six files listed above via GitHub, verify current `dev/wossol-integration` SHA, then inspect current code for B17-01..05 and classify bounded findings. Avoid redundant full restart of batches 01–16.
2. Continue unfinished cross-links: free units × FIFO COGS; offer discounts × actual OrderItem allocations × Finance/ROAS; Shopify COD Upsell origin × Merchant Orders UX; multi-Store deletion; Product Detail payment failure isolation; Shopify pricing freeDelivery flag; remaining Products scope and coverage gates.
3. Continue substantive discovery in batches with clear merchant-value chain, evidence, proof/demo and Brand relevance. Do not save every batch; save only at next meaningful milestone.
4. Once Products discovery is comprehensive, perform mandatory historical Brand Intelligence cross-check, resolve/label open gates, then produce final `PRODUCTS.md`. Later sections revisit Pending relationships.
5. The immediate research continuation should focus on **free reward FIFO cost accounting and discount allocation to Order Items**, as this may materially alter Product profitability and marketing claims.

## Links
- Protocol: https://github.com/jetshop7/wossol-brand-intelligence/blob/main/07-current-product-deep-study/CURRENT_PRODUCT_DEEP_STUDY_PROTOCOL.md
- QA gates: https://github.com/jetshop7/wossol-brand-intelligence/blob/main/07-current-product-deep-study/DEEP_STUDY_EXECUTION_AND_QUALITY_GATES.md
- Register: https://github.com/jetshop7/wossol-brand-intelligence/blob/main/07-current-product-deep-study/PRODUCTS_WORKING_EVIDENCE_AND_CROSS_SECTION_REGISTER.md
- Earlier continuity: https://github.com/jetshop7/wossol-brand-intelligence/blob/main/07-current-product-deep-study/PRODUCTS_RESEARCH_CONTINUITY_BATCHES_13_TO_16_AND_NEXT.md
- QA: https://github.com/jetshop7/wossol-brand-intelligence/blob/main/07-current-product-deep-study/PRODUCTS_QA_TEST_ORDERS_AND_FIFO_COVERAGE_V0.1.md
- Source: https://github.com/jetshop7/wossol-platform/tree/dev/wossol-integration
