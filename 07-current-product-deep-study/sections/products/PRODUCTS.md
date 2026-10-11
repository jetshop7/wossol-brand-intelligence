# Products — independent current-code investigation

Status: IN PROGRESS. Code inspected on 2026-10-11: jetshop7/wossol-platform, dev/wossol-integration. Tests identified but not run. This is a fresh study, not a copy of old Codex or archived work.

## Verified source observations
- Workspace-scoped Product and Variant identity with Store links.
- Merchant Products UI uses scoped cursor pagination, search, sorting, category and Shopify health filters. Backend explicitly rejects providerHealth filtering and non-ACTIVE status filtering. The older P-01 audit statement about the main UI loading all Products is outdated.
- Product attention is derived from Accurate/Mayar connection failures and incomplete Shopify links, not a general readiness score.
- Product creation prepares inactive foundations; required Accurate/Mayar Variant links precede activation. Partial remote creation triggers compensation; unresolved compensation retains non-operational reconciliation evidence.
- Variant creation also starts inactive and requires provider mapping. Retry of provider LINK is allowed for FAILED_RETRYABLE; ACTION_REQUIRED requires review. Concurrency claims and idempotency are explicitly covered by checked-in tests.
- Product-level edits persist locally before attempting Accurate/Mayar update, so cross-system atomicity must not be claimed.
- Product delete checks historical OrderItems before contacting provider. With no history and provider-deletable Variants, it deletes remotely and locally archives Product and Variants. Sequential remote deletes may leave partial external results on mid-batch failure; local archival is withheld. Old Product System V2 hard-delete/activation rule does not describe this implementation.
- Shopify connections UI provides explicit draft creation, Product/Variant mapping, missing-Variant synchronization, relink and unlink. Advertising UI provides Meta mapping and lifecycle actions. Full backend-to-provider trace remains pending.
- Orders owns a bounded Product/Variant selector scoped to one authorized Store. REAL and TEST purposes are distinct. Search results use effective Inventory availability, while exact hydration retains already-selected lines.
- Commercial Order creation snapshots Product/Variant identity and prices, then requests Inventory reservation for eligible lines. Inventory locks Variants, checks scope and aggregates demand; insufficient stock can lead to WAITING_FOR_STOCK if allocation mode allows, otherwise a conflict. Test Orders and blocked customers skip reservation.
- Waiting-stock promotion retries full reservation in a transaction and promotes only on successful allocation; trigger reliability and deployed timing not yet verified.

## Existing tests, not executed
product-provider-sync-recovery.spec.ts, product-accurate-mayar-partial-sync.spec.ts, merchant-products-list.pagination.spec.ts, product-operational-attention.spec.ts; further Orders and Inventory tests pending inspection.

## Merchant-value hypotheses (not measured)
Identity continuity from Product/Variant to Order line; safer recovery from uncertain provider outcomes; stock-aware selection and waiting states; preserved historical Order references; explicit Shopify/Advertising connections.

## Claims not yet justified
Universal instant synchronization, guaranteed recovery, zero overselling, live provider inventory, all-or-nothing distributed delete, AI-driven Product features, measurable sales lift, competitive uniqueness.

## Next investigation
Backend Shopify/Advertising mapping ownership, Product payment policy and Order snapshots, Order stock promotion triggers, permissions, cross-Store isolation, test execution, runtime checks.

## Key code
https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/products.service.ts
https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/orders/orders.service.ts
https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/inventory/inventory-reservation.service.ts

---

# Products — Payment policy, Shopify and Advertising: verified addendum (2026-10-11)

Status: code-inspected, tests not executed, deployment not verified. Supplements the independent Products study.

## Product payment policy to Orders
`ProductsService.capturePaymentPolicySnapshots` selects policies effective at Order creation time for each active OUT Order line and every supported PaymentMethod. Exact Variant policy overrides Product fallback; missing or ambiguous resolution throws ConflictException. Each snapshot retains source policy ID/version, scope, effective date, enabled state and merchant/customer fee shares. `OrdersService` calls capture within the Order creation transaction after persisting OrderItems. `allowedPaymentMethodsForOrder` requires every sale line to enable a method. This preserves historical commercial policy evidence; it does not prove payment collection or settlement success. Checked-in `product-payment-policy.service.spec.ts` covers fallback and fail-closed resolution but was not executed.

## Shopify backend mapping
`ShopifyProductService.manualLinkProduct` authorizes `merchant.commerce.products.publish`, reads the selected remote Shopify Product, blocks conflicting ownership in the Store, requires explicit confirmation for replacement, retires old Variant/Media associations, fences old automatic-create operations, and audits changes. `endProductLink` removes local Product/Variant/Media links and fences old operations, leaving Shopify Product and Store connection untouched. `manualLinkVariant` requires ACTIVE local Variant and an exact remote Variant belonging to linked Shopify Product; forbids external Variant being owned by another local Variant. No name/SKU inference. These are code-proven controls, not deployed success claims.

## Meta Advertising backend mapping
`MerchantAdvertisingService` gates mapping mutations with `advertising.mappings.manage` and validates eligible Meta target; Product and Variant mapping lifecycle includes create, retire, correct wrong, relink. `productMappings` requires Advertising section access and returns a Workspace-wide Product-owned read projection, including historical links even if Meta authorization later changes. Advertising owns mapping lifecycle; Product UI is an access surface. No evidence that Products creates campaigns or optimizes ads.

## Next gates
Trace policy update UX and authorization, Shopify automatic create and uncertain outcomes, Meta ownership/attribution use in Orders, stock-promotion trigger, and relevant integration tests.

Sources:
https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/products.service.ts
https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/orders/orders.service.ts
https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/shopify/shopify-product.service.ts
https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/advertising/merchant-advertising.service.ts
