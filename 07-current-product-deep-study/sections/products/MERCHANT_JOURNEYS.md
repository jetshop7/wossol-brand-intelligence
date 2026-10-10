# Products V2 — Merchant Journey Map

**Status:** DRAFT — independent discovery; no observed UI session. **Code baseline:** `bf2d82f46d3189250e2a0ced6e6010d85c4de699`. Labels below are **code-inferred**, not screenshots or executed UI tests.

## Journey index
| ID | Merchant/customer journey | Persona | Status |
|---|---|---|---|
| MJ-01 | Create Product → Variants → Provider readiness → Images → Policy → Edit | Merchant | DISCOVERY |
| MJ-02 | Connect Shopify → create/link Product → map Variants → inspect health | Merchant | PARTIAL TRACE |
| MJ-03 | Open Shopify Admin → Apps → Wossol → manage Product COD → configure form/offers/delivery | Merchant | PARTIAL TRACE |
| MJ-04 | Visit storefront COD Form → choose variant/offer → submit → canonical Order | Customer, then merchant | NOT YET TRACED |
| MJ-05 | Confirmation operator adds/removes Upsell → totals/reservations | Confirmation worker | PRIOR AUDIT / RECHECK |
| MJ-06 | Product → Advertising mappings → campaign context | Merchant | NOT YET TRACED |

## MJ-02 — Shopify Product and Variant mapping (partial)

**Goal:** Connect the correct Wossol Product/Variant identities to Shopify and see readiness. **Preconditions:** Workspace, Store, permission and appropriate Shopify connection. **Source:** `apps/frontend/src/app/merchant/products/ProductConnections.tsx`.

| Step | Merchant sees (source-inferred) | Merchant does | System does | Evidence / gap |
|---|---|---|---|---|
| 1 | Product connection area, Shopify state and connection-health labels | Opens Product detail | Fetches Shopify Product status and scoped health | S1; exact route/navigation pending |
| 2 | Create Shopify Product option | Chooses creation | Calls `createShopifyProduct`; UI says it creates an unpublished Shopify draft | S1 UI handler; backend provider outcome pending |
| 3 | Shopify catalog search and selection | Searches/chooses Product | Loads bounded Shopify Product choices | S1; backend authorization pending |
| 4 | Confirmation before changing existing link | Confirms or cancels relink | `manuallyLinkShopifyProduct`; warns existing Variant links will be cleared | S1; transactional/provider reconciliation pending |
| 5 | Missing-variant sync or exact Variant controls | Synchronizes or manually maps | `syncMissingShopifyVariants` / `manuallyLinkShopifyVariant` | S1 handler imports; full conditions pending |
| 6 | Connection status, mapping health, recovery messages | Reviews state / retries where offered | Refreshes mapping and connection-health projections | S1; runtime pending |

**Value hypothesis:** avoids treating Product-level linking as proof that all sellable variants are correctly mapped. **Not yet proven:** live Shopify API outcomes, actual user screenshots, exact permissions and error recovery.

## MJ-03 — Shopify Admin Wossol COD management (partial)

**Critical discovery:** `apps/frontend/src/app/merchant/applications/shopify/cod/page.tsx` currently exports an instruction-only view: “Manage Wossol COD in Shopify” and directs the merchant to **Shopify Admin → Apps → Wossol**. An older detailed merchant editor remains as an unmounted function; **it must not be presented as the active UX**.

| Step | Merchant sees (source-inferred) | Merchant does | System does | Evidence / gap |
|---|---|---|---|---|
| 1 | Instruction to manage Wossol COD inside Shopify Admin | Opens Shopify Admin / Apps → Wossol | Shopify app entry points still need tracing | S1 route source |
| 2 | Product COD settings within embedded app | Chooses Product and policy | Backend Product COD service lists active scoped Products and mapping readiness | S1 backend; active embedded UI not inspected |
| 3 | Readiness and eligible mapped variant count | Checks if Product is ready | Backend computes `READY_FOR_COD` / `NEEDS_SETUP`; enablement blocked without required mapping | S1 `shopify-cod-product-configuration.service.ts` |
| 4 | Form text/field settings | Changes labels/visible fields/order | Embedded form configuration service stores Product-scoped overrides or inherits defaults | S1 backend; UI wiring and storefront result pending |
| 5 | Appearance and upsell-modal styling | Changes colors/layout/control appearance | Service defines and stores appearance fields | S1 backend defaults; actual UI and customer rendering pending |
| 6 | Offer, delivery, upsell policy | Configures options | Related services identified but not yet read end-to-end | DISCOVERED only |
| 7 | Customer-facing COD form | Customer uses configured form | Storefront integration not yet traced | NOT VERIFIED |

**Value hypothesis:** product-specific COD experience managed where Shopify merchants work, with Wossol retaining order/policy execution. **Do not claim** a completed end-to-end form customization journey until active embedded UI and storefront rendering are verified.

## MJ-01, MJ-04, MJ-05, MJ-06 — required next tracing
For each, complete: persona, trigger, entry point, prerequisites, every visible step and decision, API/DB/provider trace, success, partial failure, retries, Test/Real, audit, downstream effects, manual work reduction, screenshot/runtime evidence and proof/claim eligibility.

## Observability
- Merchant screenshots: **NOT OBSERVED**.
- Customer COD form screenshots: **NOT OBSERVED**.
- End-to-end browser session: **NOT EXECUTED**.
- External Shopify sandbox: **NOT TESTED**.
- Source excerpt review: **S1 only**.
