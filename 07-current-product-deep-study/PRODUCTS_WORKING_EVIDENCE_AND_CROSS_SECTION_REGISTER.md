# PRODUCTS — Independent Working Evidence & Cross-Section Register

**Status:** ACTIVE RESEARCH — NOT FINAL / NOT APPROVED FOR PUBLIC CLAIMS  
**Source:** `jetshop7/wossol-platform` · branch `dev/wossol-integration` · tree `bf2d82f46d3189250e2a0ced6e6010d85c4de699`  
**Method:** Current-code-first, test-confirmed where possible. Existing Brand Intelligence and legacy findings intentionally **not yet reviewed**.  
**Scope:** Products current behavior and its demonstrated interfaces with Provider, Inventory, Orders, Commerce/Shopify, COD, Advertising, and payment configuration.  
**Protocol:** `CURRENT_PRODUCT_DEEP_STUDY_PROTOCOL.md` + `DEEP_STUDY_EXECUTION_AND_QUALITY_GATES.md`.  
**Important:** This file is the research register, **not** the required final `PRODUCTS.md`. Screenshots already provided previously; final screenshot-to-code reconciliation and historical cross-check remain open.

## Research A — Verified current section-level mechanisms

### P-01 Controlled Product activation
- Current service creates Product, Variant(s), ProductStore and audit foundation as `INACTIVE` inside local transaction, verifies selected active Store / Workspace / actor authority; then attempts Accurate/Mayar provider sync for each Variant; only finalizes `ACTIVE` after required link evidence.
- On failed provider sync, attempts compensation of newly created external products. When compensation completes, local foundation is archived; failed compensation leaves non-operational `INACTIVE` data and a high-severity audit for reconciliation. Creation may fail when provider is unavailable; no guaranteed atomic distributed transaction.
- Merchant job: establish a usable product without manually reconciling partial provider creation; avoid a false signal that unlinked product is operational.
- Commercial outcome: lower risk of silently inconsistent product creation; controlled activation rather than just a catalog form.
- Brand hypothesis: operational trust / safe progress, **not** fully automated or guaranteed.
- Evidence: [products.service.ts lines 981–1255](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/products.service.ts#L981-L1255).

### P-02 Variant-specific identity and update
- Each Variant receives its own `variantCode`, SKU optional, name, price, weight; additional Variant begins `INACTIVE` and requires mapping before finalization. Update checks merchant ownership, active Store/Workspace, edit permission, existing external mapping, positive price/weight, and attempts provider update before local update/audit.
- Provider error causes rejection before local Variant mutation; uncertain provider writes may require human reconciliation (no global transaction).
- Merchant value: reduce double entry and matching across catalog and provider; precision at option level.
- Evidence: [products.service.ts lines 1416–1817](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/products.service.ts#L1416-L1817); [variant lifecycle tests](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/product-variant-create-lifecycle.spec.ts).

### P-03 Product/Variant deletion protects commercial history
- Both deletion paths reject any historical `OrderItem` references **before** provider deletion. Provider deletability and deletions precede local `ARCHIVED` transitions and audit (no hard deletion); removing last Variant archives Product.
- Merchant job: retire unused catalog options without casually breaking historical order references.
- Boundary: sequential external provider deletes mean partial failure remains possible. Do not advertise one-step fully atomic cleanup.
- Evidence: [products.service.ts lines 1867–2255](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/products.service.ts#L1867-L2255).

### P-04 Product management projects decision-oriented availability
- Merchant list allows scoped search (product name/code or active Variant SKU/name/code), category, sorting, cursor pagination. It returns per-Variant `effectiveAvailableQuantity` through InventoryReservations batch rather than merely copying provider number; uses exact Workspace/Merchant/Store/Product/Variant scope.
- `effectiveAvailable = max(0, providerOnHand - activeReservations - pendingProviderSyncConsumption)`; unknown provider stock stays `null` (not zero).
- List also projects targeted provider attention and, with exact Store permissions, Shopify incomplete state. No global guarantee that all problems are surfaced; do not conflate NO_CONNECTION with an urgent error.
- Merchant work reduced: looking up stock, subtracting commitments, checking some provider/Shopify statuses.
- Evidence: [merchant-products-list.controller.ts](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/merchant-products-list.controller.ts); [inventory-reservation-policy.ts](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/inventory/inventory-reservation-policy.ts).

### P-05 Product-specific payment policy (COD + optional electronic methods)
- New Product bootstrap creates mandatory `CASH_ON_DELIVERY` enabled, plus separate optional `POS_CARD` and `E_PAYMENT` policy rows. Merchant cannot disable COD in its product settings. POS/E flags can differ; electronic fee responsibility is a *shared* merchant/customer split and must sum exactly to 100 (up to two decimal places).
- Changes require merchant permission, scope, previous effective policy, auditable versioned history; electronic pair changed within serializable transaction with audit and event. Transactional failure rolls both changes back.
- Variant policy rows can override product rows per payment method when evaluating an Order; no averaging of different fee splits.
- Merchant job: choose accepted payment channels and who bears electronic processing fees while preserving effective history.
- Evidence: [products.service.ts lines 399–665](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/products.service.ts#L399-L665); [electronic tests](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/product-electronic-payment-settings.service.spec.ts).
- **UX / reliability caveat:** Edit Product frontend saves catalog via `merchant-edit`, then separately saves electronic settings if changed. Product save can succeed while payment update fails; UI explicitly displays partial success. Do **not** describe combined edit as atomic. [edit/page.tsx lines 142–225](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/frontend/src/app/merchant/products/edit/page.tsx#L142-L225); [frontend test](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/frontend/src/app/merchant/product-payment-settings.spec.ts).

## Research B — Cross-Section Discovery Register

Classification: **Verified Compound Advantage** only where both relevant pathways were checked, with limited, exact wording. **Pending Cross-Section Verification** cannot become public marketing claim.

### C-01 Products ↔ Provider Accurate/Mayar
**Status: Verified Compound Advantage (code-path level).**  
Link: Product + Variant creation/update/delete to provider mapping and safe state transitions. Merchant consequence: less duplicate management and fewer silent half-completed states. Risk: provider outcomes can be uncertain and intervention may be required.  
Sources: ProductsService creation/update/delete and Variant lifecycle tests above.

### C-02 Products ↔ Inventory ↔ Orders
**Status: Verified Compound Advantage (specific checked flows).**  
List obtains InventoryReservations batch effective availability; Orders orderable-product-picker tests distinguish real sale eligibility when quantity zero/unknown from permitted Test Product scenarios. Merchant consequence: orders reflect inventory commitments, not gross stock alone.  
Sources: [InventoryReservationService](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/inventory/inventory-reservation.service.ts); [orders picker tests](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/orders/orders.orderable-product-picker.spec.ts).  
Boundary: full lifecycle of waiting-stock, reservations, external shipment and alerts is for later systematic verification, not a settled global claim.

### C-03 Products ↔ Shopify ↔ Commerce Orders
**Status: Verified Compound Advantage (mapping and identity-resolution paths).**  
Shopify product/variant connections track `NO_CONNECTION`, `UNMAPPED_PRODUCT`, `PARTIAL`, `COMPLETE`. Local COMPLETE is mapping completeness, **not** a live inventory, tracking, publishing, or operational-health guarantee. Ingested commerce order lines verify product+variant mapping, exact Workspace/Merchant/Store/Connection scope and local ACTIVE eligibility; fail closed on missing/conflicting identities.  
Sources: [shopify-product.service.ts](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/shopify/shopify-product.service.ts), [commerce-order-resolution.service.ts](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/commerce/commerce-order-resolution.service.ts).  
Merchant value: controlled product publishing/linking; reduce accidental wrong-variant orders.

### C-04 Products ↔ Shopify COD
**Status: Verified Compound Advantage (local readiness).**  
COD readiness requires connection + product link + nonempty active Variants + mapping for all active Variants; `READY_FOR_COD` vs `NEEDS_SETUP`, reasons retained. Merchant value: gate unsafe activation when links incomplete. Boundary: local readiness cannot guarantee end-to-end live checkout/delivery availability.  
Source: [shopify-cod-product-configuration.service.ts](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/shopify/shopify-cod-product-configuration.service.ts).

### C-05 Products ↔ Meta Advertising Mapping
**Status: Verified Compound Advantage (product association only).**  
Current eligible Advertising mappings at Product/active-Variant level drive `NOT_CONNECTED` / `PARTIAL` / `COMPLETE`. COMPLETE may result from eligible Product-level link or every active Variant mapped. Merchant value: see which products are associated with advertising entities.  
Source: [advertising-product-connection-health-projection.service.ts](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/advertising/advertising-product-connection-health-projection.service.ts).  
**Prohibited leap:** Product-to-ad mapping does NOT establish which ad acquired any particular Order, nor ROAS/tracking accuracy.

### C-06 Advertising acquisition evidence ↔ Orders — independently verified, but **not inferred from Products mappings**
**Status: Verified Compound Advantage (Orders + Advertising evidence subsystem); Product-specific causal compound claim REJECTED.**  
Orders has EXACT and UNRESOLVED acquisition evidence. Advertising resolver uses scoped persisted ad entities and canonical hierarchy, no Product or Variant mappings, no provider/network call in resolution. Unknown/ambiguous identity stays UNRESOLVED; contradictory hierarchy fails closed. Bounded late reconciliation appends `OrderAttributionAdvertisingResolution` proof without rewriting original unresolved evidence. Merchant projection distinguishes `RESOLVED`, `LATE_RESOLVED`, `UNRESOLVED`.  
Merchant value: evidence-led confidence and later improved attribution transparency, not guessed conversion causality.  
Sources: [advertising-acquisition-evidence-resolver.service.ts](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/advertising/advertising-acquisition-evidence-resolver.service.ts), [resolver tests](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/advertising/advertising-acquisition-evidence-resolver.service.spec.ts), [order-attribution-advertising-resolution.service.ts](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/orders/order-attribution-advertising-resolution.service.ts), [late resolution tests](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/orders/order-attribution-advertising-resolution.service.spec.ts), [merchant projection test](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/orders/order-attribution-late-projection.spec.ts), [Orders evidence tests](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/orders/order-attribution-advertising-evidence.spec.ts).  
**Do not claim:** Marketing spend/revenue correlation, ROAS, every Order's conversion source, product mapping as attribution evidence.

### C-07 Products ↔ Orders ↔ Workspace Payment Configuration
**Status: Verified Compound Advantage (policy eligibility paths).**  
At Order creation, ProductPaymentPolicy rows are snapshotted for *each active OUT sold line* with Variant-specific precedence then Product fallback, policy ID/version and fee-share evidence. Eligibility at order-level requires all active sale lines to permit a method; Workspace operational permission further gates POS/E. Historical orders without snapshot version fall back to COD only, without inventing electronic evidence. Pre-confirmation edit that replaces lines explicitly deletes/rebuilds snapshots at edit time—therefore avoid saying snapshots are absolutely frozen across all order edits.  
Merchant benefit: specific product-level control + coherent multi-item checkout eligibility + attributable historical policy decisions; safeguards against unsupported payment choices and policy backfill assumptions.  
Sources: [products.service.ts lines 593–665](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/products.service.ts#L593-L665); [orders.service.ts lines 1379+ and 2634+](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/orders/orders.service.ts); [workspace-payment-configuration.service.ts lines 100–170](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/workspace-payment-configuration/workspace-payment-configuration.service.ts#L100-L170); [policy tests](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/product-payment-policy.service.spec.ts).

### C-08 Products ↔ Home attention
**Status: Pending Cross-Section Verification.**  
Products list projects targeted provider / Shopify incomplete operational attention. Need read Home consumer and explain to merchant whether/how signals roll up (not assume).  

### C-09 Products ↔ Finance and commercial impact
**Status: Pending Cross-Section Verification.**  
Product/Variant payment processing shares and historic snapshots are verified. Do not infer Finance fee settlements, reconciled charges, net profit, or explicit ROI yet. Trace consumer path before promotion.

### C-10 Test Product ↔ Orders ↔ Upsell / Incomplete Recovery
**Status: Pending Cross-Section Verification, with independent rule leads.**  
Classification is independent of stock. Orders picker tests confirm test product may be selectable at zero/unknown availability for designated test flow. Must check exact source of Test Product upsell restrictions, Shopify test propagation, and incomplete recovery before final marketing claims.

## Proof moments to script and verify in UI
1. Create multiple Variants → external link prerequisite → activation/failure states and audit. Do not perform destructive provider mutation solely for demo without isolation.
2. Shopify product mapping `PARTIAL` → explicit Variant linking → `COMPLETE`; then demonstrate bounded commerce order identity acceptance vs conflict.
3. Provider stock 20 − reserved 6 − pending sync 3 = effective available 11 (illustrative quantities only).
4. Product settings: optional electronic methods, fee share percentages, and permission-gated effective Order methods. Validate both Product and Workspace settings.
5. Advertising origin: `UNRESOLVED` → `LATE_RESOLVED` only with unique canonical evidence, and a remaining ambiguous example. Never link automatically using Product mapping.
6. Attempt to delete a Product with OrderItem history → rejection; no provider deletion.

## Marketing & Website extraction — working hypotheses (NOT approved)
- Narrative: **Operationally connected product management**, with accurate identities, usable stock context, controlled publishing, and payment decision traceability.
- Product feature page: what changes for merchant when product moves from catalog → inventory → store → advertising association → order, with **explicit evidentiary gaps** in attribution.
- Sales demo: show verifiable state transitions, no invented absolute time savings/ROI.
- Potential brand territory: `Operational Clarity + Controlled Commerce + Traceable Decisions`. This is an emerging strategic pattern to test across all sections, not final positioning.
- Negative claims: no `all-in-one`, `fully automated`, `real-time stock`, `complete tracking`, `all orders attributed`, `guaranteed profitable ads`, `atomic cross-system actions`, or `best/only/first`.

## Outstanding QA gates before PRODUCTS.md
- Systematic Product list/search/pagination/permissions plus all product-detail panels (screenshots ↔ current UI ↔ backend).
- Product category/taxonomy, images/storage/media propagation, Test/Real restrictions, variant matrix, payment merchant-facing accessibility and advanced override UI, provider recovery, deletion test coverage.
- Inventory reservation lifecycle, Shopify media and uncertain reconciliation, Meta product mappings, attribution origin to final order projection/analytics, and payment ↔ Finance.
- Validate Home rollup, lifecycle downstream effects, limitations, weakest UX points; extract proof moments, SEO/AEO opportunities, explainable merchant job stories.
- Perform **mandatory legacy Brand Intelligence cross-check only after independent research**, verify each old finding in current code, log discoveries/conflicts.
- Final standalone comprehensive `07-current-product-deep-study/PRODUCTS.md` only after all required gates.

**Claim discipline:** Code/test verification above supports specific software pathways, not production deployment, commercial outcome guarantees, or an untested end-to-end live demo.
