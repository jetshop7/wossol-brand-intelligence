# Products V2 — Cross-System Trace

**Status:** OPEN / partial edges only. **Code baseline:** `bf2d82f46d3189250e2a0ced6e6010d85c4de699`. No end-to-end execution claimed.

## Initial graph (research plan, not proof of complete live chain)

```text
Merchant Wossol Product UI
  → Product / Variant / Store identity
  → Accurate Mayar provider lifecycle (conditional)
  → Shopify Product and exact Variant mapping
  → Shopify Embedded App (merchant COD management)
  → Product COD policy + form fields/appearance + offers/presets/delivery
  → Shopify storefront COD Form (customer)
  → Checkout session / canonical Order
  → Confirmation / inventory reservations / delivery / analytics (conditional)
```

## Edge ledger
| Edge | Evidence examined | Status | Missing proof / merchant impact |
|---|---|---|---|
| Product detail → Shopify mapping controls | `ProductConnections.tsx` handlers for create, search, relink, missing variants | PARTIAL | Verify backend/provider side, errors and full UI |
| Product → Store → Shopify COD readiness | `shopify-cod-product-configuration.service.ts` scopes active Products and mappings; blocks enable if not ready | S1 PARTIAL | Readiness predicates, embedded app UI and integration tests |
| Merchant COD route → Shopify Admin | `merchant/applications/shopify/cod/page.tsx` current default export | S1 VERIFIED (route text) | Confirm navigation behavior and embedded app entry |
| Embedded configuration → persisted form labels/fields | `shopify-embedded-product-form-configuration.service.ts` read/save/reset | S1 PARTIAL | Verify active UI route and storefront rendering |
| Embedded configuration → form appearance/order | Same service default appearance and field order | S1 PARTIAL | Verify UI editing and customer-visible result |
| Shopify COD → checkout → Order | `shopify-cod.service.ts` and session processor identified | PENDING | Full request, validation, timeout and Order proof |
| Order → Confirmation Upsell → inventory | Prior Confirmation/Inventory source inspection | PENDING | RISK-001 and RISK-002 tests |
| Product → Advertising mapping | `ProductConnections.tsx` loads mappings | S1 PARTIAL | Advertising service and attribution consumers |
| Product → Accurate Mayar → inventory | Provider services identified | PENDING | Full lifecycle and stock truth |

## Mandatory questions
1. Is the embedded Shopify Admin UI active, authenticated and connected to these backend services?
2. Which extension/theme block renders the customer COD Form, and how are selected Product and Variant resolved?
3. Which exact settings reach the customer form: field labels, order, appearance, delivery, offers, upsell modal?
4. What does the merchant see when Shopify Product mapping is incomplete, or the provider fails?
5. How does a customer form submission create an Order, and how are duplicates and Test Products handled?
6. Which downstream steps are optional versus automatic, and which are not implemented?
7. What is the merchant's actual reduction in copying, rematching, tool switching and error handling? Do not quantify without measurements.

**Gate C:** NOT PASSED. Edges remain partially inspected.


## Second-pass traced nodes and edges (2026-10-10)

- **Shopify Admin entry:** `apps/frontend/src/app/shopify/app/route.ts` serves App Bridge and loads `/shopify-app-home.js`. The actual browser controller is still to be inspected.
- **Store linking:** `apps/frontend/src/app/shopify/link/page.tsx` connects merchant-authenticated Store selection to Shopify return; backend link-intent contract pending.
- **Storefront block:** `extensions/wossol-cod-form/blocks/wossol-cod-form.liquid` exposes Product ID, App Proxy root, form runtime URL and actual customer controls for variants, bundles, destination, totals and upsell modal.
- **Storefront backend:** `shopify-cod.service.ts` `bootstrap()` reads variants/destinations/form/offers; `quote()` calculates merchandise, discounts, delivery and upsells. Checkout and normalized Commerce Order handoff not yet followed to completion.
- **New gap:** distinguish the existence of Shopify Theme App Extension code from evidence that it is enabled on a live Shopify theme and fully functional. Runtime/browser test required.


## COD checkout edge trace (2026-10-10)

```text
Shopify Product Theme App Extension
  -> signed Shopify App Proxy bootstrap (mapped variants + destinations + form + offers)
  -> quote / preflight (pricing + phone validation)
  -> persisted scoped checkout session (COLLECTING; 30-min TTL)
  -> Order intent (freeze quote + Upsell sequence; ORDER_INTENT_CONFIRMED)
  -> sequential Upsell accept/skip with cursor/replay controls
  -> finalizeCheckoutSession (immediate or due-session recovery)
  -> normalized Commerce Order ingest (downstream transaction trace PENDING)
  -> Orders / Inventory / Confirmation (downstream proof PENDING)
```

**Evidence:** `shopify.controller.ts`, `shopify-cod.service.ts`, `shopify-cod-checkout-session.processor.ts`, `shopify-cod-checkout-session.spec.ts`. Direct `/shopify/cod/submit` endpoint exists but its service deliberately rejects bypass with session-confirmation error. The processor does not poll Shopify; it processes durable due sessions with lease-aware scheduling. **No end-to-end test was run.**


## Verified static trace — finalization to Orders transaction (2026-10-10)

**Sources:** `apps/backend/src/modules/shopify/shopify-cod.service.ts` (`finalizeCheckoutSession`), `apps/backend/src/modules/commerce/commerce-order-resolution.service.ts` (`ingestNormalizedCommerceOrder`), `apps/backend/src/modules/orders/orders.service.ts` (`createCommerceImportedOrder`, `createOrderWithStockAllocationPolicy`). Source only; tests not executed.

1. Session finalization uses optimistic revision and an expiring lease/claim token; its canonical external identity is `wossol-cod-session:<session.id>`.
2. Commerce validates the normalized input, verifies active Connection scope and resolves exact mapped Product/Variant identities and destination; then calls Orders-owned `createCommerceImportedOrder`.
3. Orders uses `allowWaitingForStock`, a **Serializable Prisma transaction**, and a transactional `commerceImport.findExisting(tx)` mapping lookup. If the mapping exists, it returns `ALREADY_IMPORTED` and the scoped existing Order.
4. If no mapping exists, the same transaction creates the canonical Order, resolves pricing/delivery and purpose, handles Inventory reservation or waiting-for-stock (Test Products skip reservation), writes audit data, and calls `commerceImport.createMapping(tx, order.id)` **inside the same transaction**.
5. The caller retries once on designated Commerce import race errors. This strongly supports transactional deduplication for repeated session finalization; database unique constraints and concurrent replay tests still need inspection.
6. After the Order is committed, Shopify mirror/projection is best effort for completed checkouts only. The session is then marked `FINALIZED` and linked to `finalizedOrderId`. If session update fails after commit, the stable external identity plus transactional mapping is designed to return the existing Order on retry.

**Critical distinction:** `INCOMPLETE_CHECKOUT` timeout can create a canonical Order when `orderReady` and eligible; not every abandoned session is merely discarded. Non-ready `COLLECTING` sessions expire without an Order. This must be described carefully in merchant messaging and Orders classification.

**Remaining:** check unique index on Commerce mapping, precise retryable error types, runtime tests for concurrent finalization, Inventory waiting and rollback, Confirmation routing, projection failure and status-page behavior.


## Identity constraints and Merchant Orders UI — 2026-10-10

- Prisma `CommerceOrderMapping`: `@@unique([commerceConnectionId, externalOrderId])`, `@@unique([orderId])`; this is the database-backed uniqueness design supporting Commerce import idempotency.
- Prisma `CommerceCodCheckoutSession`: unique `opaqueToken` and unique `finalizedOrderId`; lease and revision are persisted. **Schema review only**; physical migrations and concurrency tests pending.
- `merchant/orders/page.tsx` and `detail/page.tsx` render separate `Incomplete` / `Recovered from Incomplete` origin labels; list includes `Waiting for Stock` filter. The messaging `Order Captures` page is unrelated to COD incomplete origin.
- Checkout session unit tests contain mock-based replay/lease/timeout/projection checks; they are **not executed** and do not substitute for PostgreSQL concurrency or live merchant UI tests.


## Migration files and Products UI discovery (2026-10-10)

- Checkout persistence migration `20260927_shopify_cod_checkout_session_v1` creates checkout-session table, state enum, unique token/finalized Order indexes, and lease/due indexes.
- Hardening migration `20260928_shopify_cod_checkout_session_hardening_v1` adds optimistic `revision`, Upsell decision cursor, scoped foreign keys and an additional composite unique constraint.
- These are **migration definitions**, not evidence of applied migrations or real PostgreSQL concurrent replay behavior.
- Product UI sources now identified: create (options/chips/combination generation, category, images), edit (provider sync confirmation), variant-edit (name/SKU/price/weight/image), list (thumbnail, variant expansion, actions). Backend request/response trace remains open.
