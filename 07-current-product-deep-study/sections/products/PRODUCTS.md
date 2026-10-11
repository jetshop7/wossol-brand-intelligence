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
