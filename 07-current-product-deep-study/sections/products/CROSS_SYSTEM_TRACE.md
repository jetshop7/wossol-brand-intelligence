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
