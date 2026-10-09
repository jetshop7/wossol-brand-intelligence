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

### P-06 Structured global Product category, historical classification
- `ProductTaxonomyService` lists only active canonical categories, requires active ID at create/edit, and replaces the *current* Product category assignment while retaining prior assignment history and its source/provenance; re-selecting the same category is idempotent, not spurious history.
- Current V1 migration seeds **26 approved global ROOT categories**, not a live merchant-defined hierarchy. Each has code, name, version and a nullable parent for *possible* later hierarchy; do not assert V1 contains deployed subcategories. Unknown legacy labels remain unclassified, not guessed into OTHER. Legacy `categoryLabel/categoryKey` remain compatibility evidence; Product views filter/read current canonical assignments.
- Source semantics `MERCHANT` / `ADMIN` / `AI` are modeled; AI classification requires confidence and model version **if supplied**, but none of this proves a live AI classifier in Merchant Product creation/edit. These send source `MERCHANT`.
- Merchant problem: disparate ad-hoc names make categorizing, finding, and filtering a growing catalog harder, and reclassification can erase knowledge of prior choices.
- Work reduced: repeated manual normalization/search; changes to categories produce an audit-like chronological assignment trail.
- Value/control: explicit allowed choices, stable category identity, active/retired gates, current category filters including UNCATEGORIZED, no guessed legacy categorization.
- Section Strength: **Canonical Category Discipline & Historical Classification**.
- Brand: precision, disciplined catalog structure, historical truth (supporting, not a standalone top-level brand promise).
- Visibility/friction: Create/Edit drop-down displays `canonicalName` in English. V1 static legacy labelAr entries are missing for some categories; no claim of fully localized Arabic category UX.
- Evidence: [taxonomy service](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/product-taxonomy.service.ts), [migration contract](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/product-taxonomy-migration.spec.ts), [migration](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/prisma/migrations/20261004_p0_10_structured_product_taxonomy/migration.sql), [list/filter](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/merchant-products-list.controller.ts), [frontend Create](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/frontend/src/app/merchant/products/create/page.tsx).

### P-07 Wossol Product and Variant image management
- Product Create/Edit provides ordered shared Product image collection, up to **five**, individual Product images **2 MB** max. First image is shown as main in Merchant UI; Product create preserves ordered reference in local transaction. Product upload currently validates claimed MIME and size (JPEG/PNG/WebP/GIF) but not file signatures at that initial upload point; later Shopify image source performs a byte-signature check.
- Each Variant supports **one** canonical image in current V1, up to **5 MB**, JPEG/PNG/WebP bytes verified against claimed MIME. Staging is bound to actor + exact Workspace/Merchant/Product/Store link, promoted to final Variant-specific reference only on successful create; replacement/removal updates Audit and cleans appropriate local references under defined conditions.
- Variant image local storage implementation explicitly refuses use in `NODE_ENV=production` or non-local storage mode and tells callers a production storage adapter is needed. **DO NOT claim deployable production Variant image uploads from this implementation**, and do not assume an adapter is deployed without evidence.
- Product Image Upload UI performs sequential uploads. If a later upload in the batch fails, completed earlier uploads may already be stored but the UI only calls `onChange` after the whole batch; potential unreferenced/orphan file clean-up risk — require end-to-end verification before labeling defect. Failed Product creation archives and clears Product image reference, but whether pre-uploaded binaries are cleaned must be checked separately.
- Merchant benefit: visible multi-photo catalog and Variant-specific visual identification; reduce separate asset lookups within Wossol and potential wrong Variant visual association. This is *not* AI image creation and not proof of instant Shopify media replication.
- Section Strength: **Product Media with Variant Precision and Local Audit**; current production deployment constraint is a material caveat.
- Evidence: [ProductImageUploadSection](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/frontend/src/app/merchant/products/ProductImageUploadSection.tsx), [ProductsService upload](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/products.service.ts#L1270-L1320), [Variant storage](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/product-variant-image-storage.service.ts), [Variant upload tests](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/product-variant-image-upload.spec.ts), [Product create image test](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/product-create-image-persistence.spec.ts).

### P-08 Multi-source merchant landing references
- Product stores an optional manually provided `landingPageUrl`; Commerce owns separate persisted, scoped Shopify `storefrontUrl` per Product mapping and Store. Merchant Product Detail may compose manual + authorized Shopify URLs labeled by Store. Commerce source is omitted when permission denied (manual reference still visible).
- `safeHttpUrl` accepts only HTTP/HTTPS and rejects javascript:/data: or malformed links at *display/navigation composition*; it does **not** verify current availability, actual conversion performance, merchant ownership of a manual URL, or the external page's content.
- Read/UX is direct outgoing navigation, not embedded iframe, live fetch, analytics, checkout completion, or auto-publishing.
- Merchant job: know where this Product is presented to customers across Store channels without switching separately into all Shopify stores; could reduce tab-search and link-copy workload.
- Section Strength: **Store-aware Product Destination Visibility**.
- Evidence: [merchant Product detail](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/merchant-products-detail.controller.ts#L200-L245), [safe landing references](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/product-landing-references.ts), [landing tests](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/product-landing-references.spec.ts), [Product detail UI tests](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/frontend/src/app/merchant/product-detail/product-landing-pages-ui.spec.ts). `creativeUrl` is stored/displayed in Product; automatic connection to Ads asset ingestion **not verified** and cannot be advertised.

### P-09 Accurate/Mayar Variant provider LINK recovery — safe retry versus uncertain outcome
- Each scoped Variant LINK is guarded by persisted `VariantProviderSyncState` states `RUNNING`, `SYNCED`, `FAILED_RETRYABLE`, `ACTION_REQUIRED`. Claim has random token and two-minute expiry; compare-and-set prevents simultaneous provider-link attempts. Before an actual provider call, `providerAttemptStartedAt` is marked. Expired `RUNNING` before provider attempt becomes `FAILED_RETRYABLE`; expired after provider attempt becomes `ACTION_REQUIRED`.
- The strict `FAILED_RETRYABLE` classifier is **only** provider's structured validation/known non-mutation, not generic network exceptions. Unknown transport, lost response, persistence-after-provider-success risk, or other uncertain provider effects require review. `ACTION_REQUIRED` cannot be retried as ordinary provider create.
- Existing exact `variantExternalMapping` short-circuits to `ALREADY_LINKED` without provider create. When claim-based success arrives late after an expired lease, `ACTION_REQUIRED` may transition to `SYNCED` **only with exact matching Workspace/Merchant/Variant/provider/external-product mapping** and safe state compare-and-set. Wrong identity or competing newer claim fails closed.
- Backend `retryMerchantVariantProviderSync` is designed only for `FAILED_RETRYABLE` `LINK` and audits attempted/success/failed retries. **Merchant HTTP retry endpoint and corresponding UI control are NOT exposed** in the current ProductsModule/controllers/list UI. The status notion is currently principally internal and aggregated to a generic list attention badge.
- Failures record safe codes/messages without exposing raw provider request/transport data to merchant. No guarantee of complete automatic resolution, always-online provider, or one-click merchant recovery.
- **Merchant job:** understand safe vs uncertain external product creation and avoid duplicate provider products. **Work reduced:** avoid repeatedly sending uncertain attempts, reduce reconciliation caused by blind retries. **Control/safety:** persisted claim, scope, status, exact identity, audit. **Section Strength:** uncertainty-aware external link coordination. **Commercial limitation:** direct merchant diagnosis/Retry action not currently available.
- Evidence: [ProductsService LINK and Retry](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/products.service.ts#L650-L980), [lease/state/finalization](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/products.service.ts#L3639-L3784), [recovery behavioral tests](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/product-provider-sync-recovery.spec.ts), [no merchant API coverage](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/product-accurate-mayar-partial-sync.spec.ts#L109-L118), [no UI retry controls](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/frontend/src/app/merchant/products/provider-sync-recovery-ui.spec.ts).

### P-10 Automatic resolution of exact provider-name collisions
- Product/Variant links and relevant mapped updates use `saveAccurateMayarProductWithCleanNameFallback`: try clean human-readable provider-facing name once; on **only exact structured provider input.name uniqueness failure**, retry with bounded numbered suffix (up to **50 provider saves total**) while preserving canonical full `variantCode` and the Wossol display name.
- Does not infer duplicate names from network/authorization, price validation or generic failures. Refuses speculative retry for unknown mutations.
- **Merchant problem:** external provider refuses a duplicate display name, blocking a sellable Variant even though the local item's business identity is distinct. **Value:** some name collisions can resolve without merchant manually renaming Products or losing local display consistency. **Work reduced:** repeated manual name experimentation. **Strength:** safe bounded compatibility with external naming constraints, not generic error healing or guaranteed availability.
- Evidence: [name fallback function](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/products.service.ts#L3580-L3638), [collision behavior and non-name failure tests](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/product-accurate-mayar-partial-sync.spec.ts#L120-L184).

### P-11 Accurate/Mayar provider connection health and limited Merchant Product attention
- `accurateMayarHealthFromVariants` interprets persisted ACTIVE Variant mapping coverage as `CONNECTED`, `PARTIAL`, `NOT_CONNECTED` or elevated `ATTENTION_REQUIRED`. It counts `FAILED_RETRYABLE` vs `ACTION_REQUIRED` and sees expired RUNNING leases as needing attention **without mutating DB in the read path**. ARCHIVED variants excluded.
- On bounded Products listing, `productOperationalAttention` maps only `ATTENTION_REQUIRED` to generic `Needs attention`, otherwise Shopify `UNMAPPED_PRODUCT` or `PARTIAL` to `Shopify incomplete`; no alert for `NOT_CONNECTED`, `NO_CONNECTION`, inactive alone or all possible operational issues. Provider attention has precedence over Shopify incomplete signal. Products list badge links to Product detail rather than a provider recovery screen.
- Merchant-facing Product Detail intentionally **does not expose generic provider readiness or recovery projection**. Backend health's specific retryable/action-required counts are not displayed at Variant granularity by this Product listing. Bounded Product List providerHealth query currently **rejected**, so cannot claim that merchants can filter their catalog by Accurate/Mayar failure status.
- **Merchant problem:** discover problematic linked catalog options within many products. **Value:** targeted aggregate attention flags can reduce blind inspection of every Product. **Gap:** generic flag without exact failure or remedy delays triage; severe backend ↔ UX exposure gap.
- Evidence: [connection health projection](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/product-provider-connection-health-projection.service.ts), [health tests](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/product-provider-connection-health-projection.service.spec.ts), [attention projection](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/product-operational-attention.ts), [list projection](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/merchant-products-list.controller.ts#L260-L304), [UI no retry](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/frontend/src/app/merchant/products/provider-sync-recovery-ui.spec.ts), [detail no recovery tests](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/merchant-products-detail.readiness-removal.spec.ts).

### P-12 Local Product edit ↔ external provider UPDATE is a separate, sometimes partial operation
- `MerchantProductsEditController.editMerchantProduct` commits Product name/category/images/local base Variant attributes and Audit first, then calls `syncMerchantProductVariantsUpdateToAccurateMayar` sequentially for nonarchived Variants. Individual `syncMerchantVariantUpdateToAccurateMayar` may return `UPDATED`, `SKIPPED`, `FAILED` without throwing; caught provider mutation failure writes `ACTION_REQUIRED` under operation `UPDATE`. The controller obtains `providerSync` array but returns a generic Product updated success and **does not forward per-Variant provider outcomes**.
- Thus local Product edit can be saved and shown as successful even when one or more provider Variant updates returned FAILED; provider attention can subsequently appear as generic badge on Product List. This **source-proven split-state / UX visibility risk** must not be marketed as automatic consistent update. It is not the same as dedicated `updateMerchantVariant` path, which attempts mapped provider update *before* local mutation and may reject local update on provider failure.
- A public manual Retry for `UPDATE` is not supported by the existing `retryMerchantVariantProviderSync` (restricted to `LINK`). Surface exact remediation route needs follow-up with product engineering.
- **Merchant job:** update catalog descriptions and Variant price/weight once and know whether external warehouse/courier sees the same change. **Observed failure:** saved locally does not imply provider parity. **Brand requirement:** make source-of-truth and sync result visible rather than promising seamless sync.
- Evidence: [edit controller](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/merchant-products-edit.controller.ts#L203-L365), [post-save sync loop](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/products.service.ts#L2275-L2314), [Variant update sync behavior](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/products.service.ts#L3035-L3245), [Retry limited to LINK](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/products.service.ts#L950-L977).

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

### C-10 Products ↔ Test/Real Order Purpose ↔ Inventory ↔ Shopify COD ↔ Upsells ↔ Incomplete Recovery
**Status: Verified Compound Advantage (bounded Test-purpose pathways reviewed from both sides).**  
- A Product's boolean `isTestProduct` is **commercial purpose**, independent of stock. Product creation and edit write this classification; create/edit UI explicitly states it is independent of quantity.
- Orders' manual picker and validation enforce Test Product ↔ Test Order, Real Product ↔ Real Order. Zero/unknown effective available stock does **not** prevent a designated Test Order's variant from being selected. Real Orders need positive known effective stock in the picker path.
- Canonical Order creation revalidates **all** Product purposes within the scoped transaction: a single Order cannot mix Test and Real Product purposes; trusted Shopify checkout expectation must agree with current canonical Products. For Test Orders, Orders skips inventory reservation; no automatic assumption of stock deduction.
- Shopify COD product context reads current Wossol classification; a properly completed mapped Test checkout passes `trustedExpectedIsTestRecord` into canonical Order ingestion. Incomplete `INCOMPLETE_TIMEOUT` for a Test Product expires checkout as `EXPIRED_UNFINALIZABLE` without creating a Recovery Order.
- Test Product cannot be **base** or **target** in Shopify COD Upsell config. Create/update/activation revalidate both sides inside transaction; read/projection and actual checkout suppress prior stale Upsells; test checkout cannot accept Upsell decision. Real-only eligible targets additionally scoped to Shopify/Store mappings.
- A checkout session whose frozen Test/Real classification no longer matches current Product classification must restart with `CHECKOUT_RESTART_REQUIRED`; critical merchant/visitor safety contract when merchant changes Product purpose during shopping.
- Analytics' studied standard/recovery queries exclude `isTestRecord: true`; Confirmation's studied merchant performance rate and attention cohorts also exclude Test Orders (operational queue counts are not universally excluded). Orders uses `TEST_ORDER_CREATED` operational fee type, **not** proof that Test Orders are free.
- **Merchant problem:** testing product-market response without contaminating normal stock allocation, add-on sales workflow, checkout recovery, or selected merchant performance metrics.
- **Manual work removed/reduced:** fewer spreadsheet/manual markers to separate tests from real sales, fewer manual stock adjustments and mistaken recovery follow-ups for test sessions; actual measured savings not established.
- **Control:** merchant can classify and reclassify Product in create/edit, with active checkout restart guard; cannot freely mix purposes at Order level.
- **Transparency:** Product checkbox explanation; Order `isTestRecord`, Shopify COD `isTestProduct`, explicit control messages; classification state projected rather than guessed from stock.
- **Safety/Trust:** exact purpose checks against canonical Products, no mixed-purpose Orders, no Test inventory reservations, no Test Upsells or incomplete recovery.
- **Section strength:** commercial product-purpose control; **Compound strength:** purpose-aware orchestration from Product to Checkout, Order, Inventory, Upsells, selected Analytics/Confirmation reporting.
- **Claim tier:** Proven Current Capability for these code-path rules, not a full A/B testing suite, automatic market validation, all payment/fulfillment behavior, or blanket exclusion from every KPI.

**Evidence (current branch):**
[Product create service](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/products.service.ts#L981-L1050);
[Product edit class persistence](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/merchant-products-edit.controller.ts#L110-L350);
[create UI wording](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/frontend/src/app/merchant/products/create/page.tsx#L245-L247);
[edit UI wording](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/frontend/src/app/merchant/products/edit/page.tsx#L540-L546);
[Orders picker & purpose checks](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/orders/orders.service.ts#L4350-L4425);
[Orders canonical purpose](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/orders/orders.service.ts#L4780-L4810);
[Orders reservation exclusion](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/orders/orders.service.ts#L1398-L1437);
[Orders picker tests](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/orders/orders.orderable-product-picker.spec.ts);
[Shopify Upsell policy](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/shopify/shopify-cod-offers-upsells.service.ts#L95-L140);
[Shopify Upsell write checks](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/shopify/shopify-cod-offers-upsells.service.ts#L215-L240);
[Shopify Upsell tests](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/shopify/shopify-cod-offers-upsells.service.spec.ts#L8-L75);
[Shopify checkout timeout and trusted Order flag](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/shopify/shopify-cod.service.ts#L445-L488);
[Checkout state/classification tests](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/shopify/shopify-cod-checkout-session.spec.ts#L130-L177);
[Analytics standard/recovery isolation](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/analytics/analytics.service.ts#L124-L172);
[Confirmation merchant overview](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/confirmation/confirmation.service.ts#L1530-L1583).

### C-11 Test Product ↔ Product Offers (not the same as Upsells)
**Status: Pending Cross-Section Verification (do not conflate).**  
Shopify COD `availableOffers` does **not** currently test `context.isTestProduct`, while `availableUpsells` does. Therefore a categorical claim that Test Products cannot use any promotional **Offers** is **not supported** by this study. Inspect promotion and order rules separately before settling whether a Test base Product may receive regular Offers.  
Source: [Shopify COD availableOffers](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/shopify/shopify-cod.service.ts#L612-L637).

### C-12 Test/Real reclassification and historical boundaries
**Status: Verified guard, open risk/UX review.**  
Merchant edit path saves `isTestProduct` through a scoped serializable Product transaction and audits before/after; the checked method contains no explicit rejection for a Product with historical Orders or already-configured Upsells. Existing Order rows retain their `isTestRecord` purpose, and a Shopify COD checkout session with a mismatched frozen Product purpose is forced to restart.
**Open question:** product reclassification may affect still-persisted Upsell configuration, ongoing unfinalized flows, merchant explanation, old/new performance reporting. Do not call a bug without end-to-end reproduction; add to QA.  
Sources: [edit controller](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/merchant-products-edit.controller.ts#L203-L350); [session classification guard](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/shopify/shopify-cod.service.ts#L375-L383).

### C-13 Products ↔ Test Order financial/confirmation boundaries
**Status: Verified narrow facts; broader effects pending.**  
Order creation invokes Finance operational charge with fee type `TEST_ORDER_CREATED` for Test Orders vs `ORDER_CREATED` for Real Orders. Confirmation analytics/rate queries exclude Test for specific cohorts, but operational work queues may still include them. It is **wrong** to market Test Orders as guaranteed free/no-cost or universally excluded from Confirmation.  
Sources: [Orders fee invocation](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/orders/orders.service.ts#L1604-L1625); [Confirmation overview cohort](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/confirmation/confirmation.service.ts#L1530-L1583).  
**Pending:** exact Finance fee schedule, settlement exclusion, delivery/shipping enforcement across all system paths.

### C-14 Products ↔ Canonical Taxonomy ↔ Merchant List
**Status: Verified Compound Advantage (classification and Product list filter paths).**  
Products create/edit enforces canonical active category; current assignments support list filtering by category or UNCATEGORIZED, while prior assignments remain traceable. Merchant value: consistent classification and faster search/finding across catalog; no personalized AI categorization proven.  
Evidence: ProductTaxonomyService + MerchantProductsListController + migration above.

### C-15 Products Media ↔ Shopify Commerce Media
**Status: Verified Compound Advantage (bounded explicit sync and safe retry/fence).**  
Products owns ordered image references and Variant canonical image; Shopify product Create/Sync Missing explicitly reads exact scoped Wossol local binaries, uses Product image mapping + Variant-to-media association, stores exact Shopify media IDs and Audit, and reuses known successful mappings. Before non-idempotent Shopify media creation it records an uncertainty fence; on uncertain provider response or failed local persistence it enters `ACTION_REQUIRED / MEDIA_RECONCILIATION_REQUIRED` and will not blindly recreate duplicate media. A read-only exact Shopify media snapshot reconciliation may mark it `MEDIA_SAFE_TO_RETRY` when provider identity sets match; unknown/contradictory media remains fenced. **Shopify media is NOT synchronized on every Product Edit and NOT by background job.**
- Merchant pain: wrong Variant image, repeated manual media upload, duplicate Shopify images after failed requests, difficult guesswork on whether a file was already transmitted.
- Value: one authorized sync action may transfer and associate product content, with verified identities and bounded uncertainty. Not a guarantee of continuous media parity.
- Control: explicit action and failure states; not automatic per-edit.
- Safety/transparency: scoped binary read, idempotent known mappings, precreate fence, auditable reconciliation, no speculative matching.
- Demo: Wossol Product 2 photos + Variant image → explicit Create in Shopify → compare exact media/Variant association; intentionally failed-response case only in isolated test fixture; show ACTION_REQUIRED and no duplicate creation on retry.
- Brand: **Controlled Cross-Channel Merchandising** + **Verified, not guessed, synchronization**; evidence supports exact paths, not all-store promise.
- Evidence: [ShopifyProductService media methods](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/shopify/shopify-product.service.ts#L426-L631), [Shopify media tests](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/shopify/shopify-product.service.spec.ts#L272-L426), [ProductImageSource scoped bytes](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/product-image-source.service.ts), [image-source tests](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/product-image-source.service.spec.ts).

### C-16 Products ↔ Commerce Product Mapping ↔ Landing Destinations
**Status: Verified Compound Advantage (authorized read-only composition).**  
Manual Product URL + Commerce-owned, permitted per-Store Shopify storefront URLs shown together with source and Store labels; URL scheme validated at navigation projection. Merchant benefit: less back-and-forth to find the customer-facing page for each listed Store; not proof the storefront is currently published, reachable, or converting.  
Sources: ProductLandingReferences + MerchantProductDetailController + ShopifyProductService provider URL snapshots.

### C-17 Products creativeUrl ↔ Advertising creative execution
**Status: Pending Cross-Section Verification.**  
`creativeUrl` appears in Product Create/Edit, Product detail/read projections and is *not* consumed by the Advertising mapping services examined. Do **not** market automated creative library, media asset handoff, Meta campaign creative propagation, or performance optimization absent a verified consumer. Determine any other relevant downstream usage before closing.

### C-18 Image storage deployment and media lifecycle
**Status: Pending Deployment Verification + confirmed development-mode limitation.**  
Backend Variant image local service rejects production (requires storage adapter); production adapter wiring/deployment not established. Product image upload writes local filesystem, and its failure/rollback/orphan behavior and cross-host persistence must be tested. This is a **high-priority merchant experience/commercial reliability risk**, not brand differentiator until fixed/verified.  
Sources: ProductVariantImageStorageService; ProductImageUploadSection; ProductsService upload.

### C-19 Products ↔ Accurate/Mayar ↔ Provider Recovery Evidence
**Status: Verified Compound Advantage (backend-only for safe recovery; merchant-facing self-service NOT VERIFIED / currently not exposed).**  
Products Variant identity + provider LINK + lease state + exact external mapping + scoped audit protect against an unsafe duplicate create or overwriting a newer attempt. Recovery classifies safe to retry vs need manual review. This does **not** establish a deployed merchant Retry UI, automatic resolution, or historical consistency across every provider update.  
Source: P-09 and P-11 above.

### C-20 Products ↔ Accurate/Mayar External Naming Policy
**Status: Verified Compound Advantage (bounded creation/update helper).**  
External provider exact name uniqueness conflicts can be resolved with numbered human-readable suffixes without changing internal Product/Variant name or `variantCode`. This reduces manual renaming when provider collision is the only error; cannot guarantee success.  
Source: P-10 above.

### C-21 Products ↔ Home (provider failures)
**Status: REJECTED as direct current compound attention integration.**  
Home `MerchantHomeSummary.attention` only has `failedDelivery`, `customerRisk`, `waitingForStock`, `confirmationAction`; it does not consume Product provider health states/Variant provider sync records. Therefore a Products `NEEDS_ATTENTION` badge **does NOT directly roll up into Home's native attention rows**. Home's generic recent Notifications and Analytics recommendation feed are separately scoped sources; they are not proof of such a provider-specific rollup.  
Source: [Home summary type/getHomeSummary](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/merchant-portal/merchant-portal.service.ts#L145-L160), [Home summary aggregation](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/merchant-portal/merchant-portal.service.ts#L530-L624), [frontend Home](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/frontend/src/app/merchant/page.tsx#L205-L234).

### C-22 Products ↔ Inventory ↔ Orders WAITING_FOR_STOCK ↔ Home actionable impact
**Status: Verified Compound Advantage (specific read-only cross-system path).**  
Home reads merchant/Workspace/permitted Store-scoped Orders in `WAITING_FOR_STOCK`; groups *active shipment OUT* items per order/Variant, queries the authoritative InventoryReservation batch effective availability, selects candidates only when inventory data is **known** and insufficient, and ranks product/Variant shortages by `directlyUnblockableOrders` (other requirements of same Order known sufficient), then `affectedOrders`, `shortageQuantity`, stable identity tie-breakers. Home displays top Product + Variant name/code, affected orders and relative stock impact within a single `waitingForStock` attention row, next action links to filtered Orders list.
- **Merchant question answered:** `Of all products missing stock, which option should I focus on first because it may relieve the bottleneck for more waiting orders?`
- **Work reduced:** cross-checking pending Orders one-by-one, grouping demands by Variant, guessing which replenishment is most useful. Exact time saved not measured.
- **Value:** a prioritized *candidate* for attention with clearly labeled shortage and impacted orders, not a guarantee that procurement/shipment/confirmation follows or that adding the displayed shortage automatically unblocks every order. Home uses `isKnown` guard; unknown stock is not counted as confidently insufficient; other unknown items prevent `directlyUnblockable` classification.
- **Control/transparency:** source is Orders+Inventory, and links to exact Orders status rather than modifying stock. Home read is no mutation.
- **Brand/website value:** **Operational decisions from joined data** > generic `all-in-one dashboard`. A powerful honest product story: `See which stock shortage is holding up waiting orders—and where replenishment may have the biggest impact`.
- **Demo:** isolated dataset with several waiting orders / mixed Variant demand; change inventory availability, refresh Home, show the change in top shortage priority and linked Orders list. Validate actual UI against screenshot.
- **Boundaries:** ranks one top Variant in response, not every affected product; not automatically restocking; no promised real-time provider data; requires Orders permission + Inventory available. Does not imply Home surfaces Provider LINK errors.
- Evidence: [getWaitingForStockImpact](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/merchant-portal/merchant-portal.service.ts#L413-L529), [Home summary aggregation](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/merchant-portal/merchant-portal.service.ts#L530-L624), [behavioral test with three waiting orders](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/merchant-portal/merchant-home-summary.service.spec.ts#L86-L118), [Home UI copy](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/frontend/src/app/merchant/page.tsx#L32-L41), [Home render](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/frontend/src/app/merchant/page.tsx#L220-L227).

### C-23 Products ↔ Analytics Recommendations ↔ Home
**Status: Pending specific Product recommendation verification; provider-sync-to-recommendation claim NOT established.**  
Home reads at most 3 `nextBestActions` from Analytics' canonical 7-day Recommendations feed; no current evidence this feed uses `VariantProviderSyncState` or provider LINK failures. Do not present a cross-system product-synchronization alert as an existing Home recommendation.  
Source: [getMerchantHomeRecommendations](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/analytics/merchant-analytics.service.ts#L1571-L1592), [Home source](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/merchant-portal/merchant-portal.service.ts#L583-L597).

### C-24 Product edit ↔ Provider UPDATE ↔ List Attention
**Status: Verified split-state risk (not an approved strength claim).**  
Safe local Product edit transaction can succeed before per-Variant external UPDATE; failures returned as `FAILED` are not exposed by edit response, while provider sync state `ACTION_REQUIRED` may later drive generic Products list attention. This has merchant trust/operational-clarity consequences. Demo or acceptance test must explicitly inspect update result and provider state before claiming synchronized edits.  
Source: P-12 above.

## Proof moments to script and verify in UI
1. Create multiple Variants → external link prerequisite → activation/failure states and audit. Do not perform destructive provider mutation solely for demo without isolation.
2. Shopify product mapping `PARTIAL` → explicit Variant linking → `COMPLETE`; then demonstrate bounded commerce order identity acceptance vs conflict.
3. Provider stock 20 − reserved 6 − pending sync 3 = effective available 11 (illustrative quantities only).
4. Product settings: optional electronic methods, fee share percentages, and permission-gated effective Order methods. Validate both Product and Workspace settings.
5. Advertising origin: `UNRESOLVED` → `LATE_RESOLVED` only with unique canonical evidence, and a remaining ambiguous example. Never link automatically using Product mapping.
6. Attempt to delete a Product with OrderItem history → rejection; no provider deletion.
7. Create Test Product with stock (and separately with zero/unknown effective stock) → confirm purpose differs from inventory classification; inspect manual Test Order eligibility and no reservation.
8. Shopify COD mapped Test base Product → no usable Upsell choices or Test targets; expire incomplete checkout → no recovery Order; freeze session and change Product classification → checkout restart.
9. Verify selected standard merchant Analytics excludes Test Orders but do **not** claim every operational/finance view excludes them.
10. Product categorized Electronics → filter by exact canonical category → change category → confirm current list change and historical assignment remains (do not claim AI classification).
11. Product image gallery (5 × max 2 MB) and one Variant image; Create in Shopify explicitly then inspect exact product/Variant media mapping. Test uncertain upload only against isolated mocks or non-production sandbox.
12. Open Product Detail Landing pages with manual and multiple authorized Shopify stores; verify source labels and that unauthorized Commerce data is omitted.
13. Using isolated mocks, force provider LINK structured validation failure vs uncertain network failure: verify FAILED_RETRYABLE vs ACTION_REQUIRED and lease expiry before/after provider attempt, with no duplicate provider call.
14. In current Product List, show generic Needs attention for provider recovery but **no merchant Retry** or detailed recoverable Variant status in Product Detail; confirm Home does not show provider-specific attention.
15. Test external duplicate name collision in a safe mock: automatic numbered suffix, Wossol Variant code/display unchanged, no retry for network uncertainty.
16. Home: several WAITING_FOR_STOCK Orders with known shortages; reveal top Product/Variant bottleneck, 'directly unblockable' distinction, unknown-stock boundary and linked Orders navigation.
17. Edit a mapped Product while one external provider Variant update returns FAILED: confirm local edit succeeds, external sync failure recorded, and UI cannot truthfully claim external parity.

## Marketing & Website extraction — working hypotheses (NOT approved)
- Narrative: **Operationally connected product management**, with accurate identities, usable stock context, controlled publishing, and payment decision traceability.
- Product feature page: what changes for merchant when product moves from catalog → inventory → store → advertising association → order, with **explicit evidentiary gaps** in attribution.
- Sales demo: show verifiable state transitions, no invented absolute time savings/ROI.
- Potential brand territory: `Operational Clarity + Controlled Commerce + Traceable Decisions`. This is an emerging strategic pattern to test across all sections, not final positioning.
- Negative claims: no `all-in-one`, `fully automated`, `real-time stock`, `complete tracking`, `all orders attributed`, `guaranteed profitable ads`, `atomic cross-system actions`, or `best/only/first`.

## Test/Real — Feature-to-Merchant-to-Brand extraction (current-study working section)

**Feature → Merchant problem:** Merchant wishes to test a product or retest a limited-stock product, while live stock commitments, recovery follow-ups and advertising/reporting should not be confused with real sales. Wossol uses an explicit Product `isTestProduct` commercial-purpose flag rather than deducing test intent from on-hand stock.

**Mechanism → work reduced:** The classification gates Manual Order variant eligibility and trusted imported Shopify COD Orders, skips Test Order stock reservation, prevents Shopify COD Upsells for Test bases/targets, expires incomplete Test checkout instead of creating recoverable Order, and excludes Test records from selected standard Analytics cohorts. It reduces repeated human filtering and manual classification work **in those specific paths**. No benchmarked time savings, experimental performance assessment or universal Finance/Analytics/Confirmation isolation is established.

**Merchant value → control / safety:** Product checkbox allows purpose changes; Order canonical checks prevent mixed Test/Real order lines and mismatched trusted source; Shopify checkout session requires restart when commercial purpose changes mid-session. Historical Order purpose is stored separately. Fee operations still have a Test-specific operational type.

**Section Strength:** Purpose-aware Product classification with UI explanation. **Compound Strength:** Connected commercial testing boundaries across Products, Inventory, Orders, Shopify COD, Upsells, and selected reporting. **Proof/Demo Moment:** show Test Product with positive stock, no stock and unknown stock; create an eligible Test Order and show no reservation; compare Shopify COD Upsell absence and non-recovered incomplete Test session; demonstrate session restart if purpose changes. All demo stages must be tested in a safe isolated environment.

**Potential website feature page (draft, requires demo validation):**
- Working headline: `Test a product without treating it as a normal sale`.
- Supporting explanation: `In supported Wossol flows, Test Product status controls order purpose, inventory reservation, Shopify COD upsells and incomplete-checkout recovery. Stock quantity alone does not decide whether an item is being tested.`
- Proof section: step-by-step Test vs Real flow showing exact exclusions, without promises of free tests or universal analytics isolation.
- FAQ / SEO / AEO topics: `Can a Test Product have stock?` (yes); `Does a Test Order reserve inventory?` (not on the confirmed Order creation path); `Can I upsell a Test Product in Shopify COD?` (no); `What happens to an incomplete Shopify Test checkout?` (timeout does not create a recovery order); `Does changing Test status affect an open checkout?` (requires restart if frozen classification conflicts).
- **Disallowed marketing claims:** `Run A/B product tests automatically`, `Test orders cost nothing`, `Every test event is invisible to all analytics`, `Test Products disable all Offers`, `no stock is ever required in any downstream fulfillment scenario`, `test purchases always reach delivery`, `Test status is Shopify's own product classification`.

**Product/UX critical observation:** The merchant may change Test/Real on the same Product, including one with earlier operational context. Session restart protects ongoing checkout from mismatched purpose, but merchant-facing warnings about impact on past Upsell configuration and live funnels need observation before future product-design recommendations.

## Merchant-to-Brand Extraction — Taxonomy, Media, and Landing Destinations

**Merchant job narrative A — `Find and manage a growing catalog without losing category meaning`:** merchant selects from active globally consistent categories, filters the Product list by current category, and the system preserves prior classification when a deliberate reclassification occurs. **Control:** explicit category choices. **Effort compression:** lower manual normalizing/filtering load, amount unmeasured. **Proof:** category switch + list filters + historical records. **Marketing:** `Organize your catalog with consistent product categories` (validated wording only). **Website:** Catalog/Products feature explainer; FAQ `Can I find products by category?` yes for current active classifications.

**Merchant job narrative B — `Present the correct option across Wossol and Shopify`:** product owns shared image gallery, Variant owns exact image reference; authorized, explicit Commerce sync maps media to known external Product/Variant IDs and makes uncertain retry safe rather than duplicating uploads. **Control:** explicit publishing/sync action. **Trust:** strict mapping and reconciliation. **Effort compression:** some duplicated upload/matching may be reduced; no instantaneous sync claim. **Proof:** safe staged image/Shopify sandbox demonstration. **Marketing:** `Connect product images to their mapped Shopify variants through an explicit, verified sync` (technical draft; simplify after UX demo). **Website:** Show Product → Variant → Shopify association, and accurate caveat about manual sync. **Design/brand relevance:** consistency/precision of imagery and connected merchandising, *not* arbitrary decoration.

**Merchant job narrative C — `Open the right sales page from one place`:** Product Detail surfaces manual and per-Store authorized Shopify storefront URL snapshots. **Control:** access to specific Store; **Transparency:** source labels; **Safety:** safe HTTP(S) URL projection; **Work reduced:** fewer searches/tabs to find destination URLs. **Proof:** two stores plus manual URL within same Product detail. **Marketing:** `Find the landing pages linked to each product and store` (do not imply monitoring/live reachability/conversion).

**Key risk register:**
1. Current Variant local image storage explicitly rejects production without an adapter: **cannot market production availability absent deployment proof**.
2. Initial Product image upload validates claimed MIME/size but does not sniff binary signatures at upload; Shopify consumption does signature-check later.
3. Product image upload batch has no demonstrated compensating cleanup for prior successful file writes after later failure; possible orphan binary references. Validate safely.
4. Frontend upload placeholder `not sent to external systems` is true at upload time but could mislead about *later explicit Shopify sync*. Improve merchant copy context after UX QA.
5. Shopify media does not auto-update on every Product Edit; explicitly check how merchant discovers resync of changed Product/Variant pictures.
6. No proven AI image creation, automatic merchandising, AI category assignment, Meta Creative URL import or live landing-page performance monitoring.
7. Legacy category translations partly absent and Create/Edit drop-down uses English canonical names; multilingual visual/verbal identity decisions should not presume end-to-end bilingual product experience.

**Emerging brand pattern:** Identity and provenance preserved across category changes, sales destinations and images; merchants receive structure plus bounded cross-system alignment. This is a *provisional pattern*, not final Wossol positioning.

## Merchant-to-Brand Extraction — Provider Recovery, Attention, Home Impact

**Story 1: `Don't duplicate provider products when connectivity fails`.** Behind the scenes, Wossol preserves exact mapping identities, distinguishes known-safe failures from uncertain writes, and prevents races/retry storms for LINK. Merchant outcome: less risky synchronization and fewer avoidable duplicate external entries; **not** fully available self-service through current UI. Demonstrate safe mock behavior, not public one-click claim. Brand principle: disciplined reliability and truth under uncertainty.

**Story 2: `Keep your naming consistent across systems even when the provider rejects a duplicate label`.** A bounded, exact name-conflict fallback may retry provider-facing suffixes while keeping Wossol Product/Variant names and canonical Variant codes stable. Merchant outcome: reduce manual naming work from certain provider constraint mismatches. Brand principle: systems adapt to operational reality within rules, not magical all-errors automation.

**Story 3: `Which product should I replenish first?`** Home connects WAITING_FOR_STOCK Orders, Product/Variant identities, and available stock; surfaces top demand bottleneck with an impact count, respecting known vs unknown evidence. Merchant value: better prioritization of attention; remove repeated navigation/math, not guarantee of immediate shipment. Demo/storyboard strongest candidate of this batch. Website headline draft: `Find the stock shortage holding up your orders`; subcopy `Wossol can identify a priority Product/Variant and show the related waiting-order impact, based on recorded order demand and available stock.` Integrates Product/Inventory/Orders/Home as a distinct merchant workflow, not a parade of module labels. FAQ topics: `Can Wossol show why orders wait for stock?`, `Does it show which variant needs attention?`, `Does replenishment automatically ship waiting orders?` (no guarantee).

**Story 4: `See the problem and understand the next step` — ASPIRATIONAL / NOT CURRENT CAPABILITY.** List badge reveals general Product issue but does not reveal exact failing Variant or merchant Retry. Product-detail route does not expose provider recovery. Brand promise `fix failed provider sync directly from Home` is **unproven / contradicted**. UX priority: expose scoped variant status, non-sensitive reason and appropriate retry/reconcile guidance; surface priority to Home only after a deliberate product decision, not as a current claim.

**Critical trust risks for product engineering:** (1) Local Product edit followed by external UPDATE may return generic success even when an internal sync result is FAILED; (2) Home provider attention absent, Product generic badge may hide complex failure state; (3) inactive created Products may remain invisible in standard active listing following creation partial failure; (4) no public merchant self-service LINK retry; (5) generic ProviderSyncCoordinatorService used for inventory sync capabilities must not be conflated with per-Variant Product LINK recovery — independent mechanisms.

**Evidence tiers:** Product LINK safe retry backend = `Proven Current Capability`; seamless self-service recovery = `Rejected/Not Exposed`; Home Stock Bottleneck = `Verified Compound Advantage`; automatic time saved / improved order completion = `Reasonable Product Interpretation` but not benchmarked; completed operational recovery or instant restock = `Unverified / NOT CLAIMABLE`.

## Outstanding QA gates before PRODUCTS.md
- Systematic Product list/search/pagination/permissions plus all product-detail panels (screenshots ↔ current UI ↔ backend).
- Verify provider sync UI gap, Product post-edit provider FAILED hidden-state risk, full retry and manual reconciliation path, exact Model/DB scope tests, Home waiting-stock UX and real API integration, active-category current UI, true Arabic category coverage, orphan Product image cleanup, production Variant media storage adapter, explicit Shopify media sync discoverability, Product image security, creativeUrl consumers, Test/Real transition UX and Offer-vs-Upsell distinction, variant matrix, payment merchant-facing accessibility and advanced override UI, provider recovery, deletion test coverage.
- Inventory reservation lifecycle, Shopify media and uncertain reconciliation, Meta product mappings, attribution origin to final order projection/analytics, and payment ↔ Finance.
- Validate Home rollup, lifecycle downstream effects, limitations, weakest UX points; extract proof moments, SEO/AEO opportunities, explainable merchant job stories.
- Perform **mandatory legacy Brand Intelligence cross-check only after independent research**, verify each old finding in current code, log discoveries/conflicts.
- Final standalone comprehensive `07-current-product-deep-study/PRODUCTS.md` only after all required gates.

**Claim discipline:** Code/test verification above supports specific software pathways, not production deployment, commercial outcome guarantees, or an untested end-to-end live demo.
