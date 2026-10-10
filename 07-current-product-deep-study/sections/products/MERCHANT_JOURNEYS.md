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


## MJ-03/MJ-04 — Second-pass source discoveries (2026-10-10)

**Shopify merchant entry:** `apps/frontend/src/app/shopify/app/route.ts` serves an actual HTML App Home document with Shopify App Bridge loaded as first executable script and `/shopify-app-home.js` deferred. The page contains a hidden COD Products section and client-controlled navigation, rather than a conventional Next.js React page. **Next evidence:** read the served `public/shopify-app-home.js` and backend App Home endpoints before claiming exact controls are live.

**Store linking:** `apps/frontend/src/app/shopify/link/page.tsx` checks a time-bounded-looking proof format (40–256 URL-safe characters), restores/login session, fetches eligible Stores, lets the merchant select the Wossol Store and confirm, and restricts the return URL to same-origin `/shopify/app`. The expiry/security properties must be verified in the backend, not inferred from UI wording.

**Customer-facing Shopify Theme App Extension:** `extensions/wossol-cod-form/blocks/wossol-cod-form.liquid` defines a Product-template section containing variant/quantity composition, preset bundle selector, governorate/destination, dynamic totals/discount/delivery, customer details and a modal upsell with product image gallery, variant/quantity selection and accept/skip. This is **actual storefront block source**, not proof of installation or successful checkout. The block loads `wossol-cod-form.js` and a dynamic runtime script; full JS/runtime interaction is pending.

**Backend storefront bootstrap:** `apps/backend/src/modules/shopify/shopify-cod.service.ts` reads scoped orderable mapped variants, delivery destinations, form configuration and offers, then supplies Shopify variant labels/prices and COD availability. Its header explicitly describes a Shopify App Proxy → provider-neutral Commerce Order adapter, leaving Orders responsible for creation, Inventory and Confirmation. Trace the checkout method and its consumers before verifying this contract end-to-end.

**Evidence:** static code S1 only. **Observed merchant UI:** no. **Observed storefront:** no. **External provider:** not tested.


## MJ-04 — Checkout session and finalization trace (2026-10-10)

**Static-source verified sequence, NOT a live storefront test:** `GET /shopify/cod/bootstrap` → `POST /shopify/cod/quote` → optional `POST /shopify/cod/preflight` → `POST /shopify/cod/checkout/session` → `POST /shopify/cod/checkout/intent` → zero or more `POST /shopify/cod/checkout/upsell-decision` → session finalization → normalized Commerce Order handoff. Endpoints are defined in `apps/backend/src/modules/shopify/shopify.controller.ts`; browser ordering still needs verification in runtime.

| Step | Customer-facing intent | Backend behavior evidenced | Important boundary |
|---|---|---|---|
| Select variants, quantities, destination and offer | Configure purchase and see total | `ShopifyCodService.bootstrap` resolves mapped variants, destinations, form and offers; `quote` calculates commercial totals | Quote is not an Order |
| Enter phone and review | Start/continue checkout | `preflight` normalizes phone and quotes; `syncCheckoutSession` stores scoped continuation and bounded snapshots | Session sync explicitly does **not** create Order/Inventory/Finance/Confirmation records |
| Order Now | Confirm order intent | `confirmCheckoutIntent` freezes base quote and ordered Upsell sequence; session becomes `ORDER_INTENT_CONFIRMED` | No Order until sequence exhausted |
| Upsell accept/skip | Choose or decline each offer | `decideCheckoutUpsell` validates frozen token, cursor, variant/quantity and records decision | Identical replay allowed; conflicting replay rejected in source/test |
| End of sequence or recovery | Complete request | `finalizeCheckoutSession` is called when last decision is saved; due-session processor handles eligible timeouts | Exact-once and Order result require further transaction/ingestion proof |
| Old client sends direct submit | Attempt legacy submission | `submit()` now immediately throws `Checkout session confirmation is required.` | Legacy implementation is commented out, not active |

**Failure/timeout evidence:** session TTL 30 minutes; due processor batches up to 25 sessions. `COLLECTING` sessions without `orderReady` expire as `EXPIRED_UNFINALIZABLE` without Order; order-ready `COLLECTING` and `ORDER_INTENT_CONFIRMED` sessions take different timeout finalization paths. Processor uses a lease-aware schedule and catches failures. A source test checks Test Product timeout does not create an Order; further test execution and production observation remain pending.

**Merchant-value hypothesis:** supports recovery from interrupted customer checkout and avoids direct order creation before upsell choices. Do not market as “zero lost orders” or “guaranteed exactly once” until persistence/transaction/replay tests and live verification establish those claims.


## MJ-04 — Order handoff and stock effects (2026-10-10)

- When the customer completes the final Upsell decision (or no Upsell exists), Shopify COD finalizes a session and submits a normalized Order using the stable `wossol-cod-session:<sessionId>` identity.
- Commerce checks the Shopify Connection and exact mapped Product/Variant, resolves destination and passes canonical input to Orders.
- Orders creates the canonical record and Commerce mapping within one Serializable transaction; existing mapping returns `ALREADY_IMPORTED` rather than a second Order. Stock allocation happens in this transaction for eligible real orders; `allowWaitingForStock` means insufficient stock may result in a waiting state rather than hard rejection. Test Products bypass reservation.
- Incomplete order-ready sessions can be captured as `INCOMPLETE_CHECKOUT` on timeout; non-order-ready sessions are expired without Order. Completed Orders may be mirrored to Shopify best-effort, after Wossol Order commit.
- **Merchant experience pending:** which Orders list bucket and status labels show incomplete captures, waiting-for-stock and projection failure; whether merchant sees alerts/recovery actions; verify in UI and screenshots.

**Value hypothesis:** cross-system canonical identity and transactional deduplication protect merchant Orders from duplicate imports on retry; Stock waiting can preserve demand without falsely promising available inventory. Requires DB/behavioral verification before marketing claim.


## MJ-04 — Merchant-visible Orders result (2026-10-10, static source)

**Merchant Orders list:** `apps/frontend/src/app/merchant/orders/page.tsx` has a Confirmation filter including **Waiting for Stock** and a separate `checkoutOriginLabel`: `INCOMPLETE_CHECKOUT` displays **Incomplete** until confirmation, then **Recovered from Incomplete**. It also exposes an **Order Captures** navigation button; that route is for messaging captures and must not be conflated with COD incomplete checkouts.

**Merchant Order detail:** `apps/frontend/src/app/merchant/orders/detail/page.tsx` implements the same checkout-origin labeling independent of the lifecycle status. A source-based test `checkout-origin-projection.spec.ts` checks these code markers; it has not been run here.

**Experience inference:** merchant can distinguish recovered COD checkout provenance from the Order's operational status, and find waiting-for-stock work via the Confirmation filter. Exact rendering, status transitions, and recovery actions still require browser/DB verification.


## MJ-01 — Product creation/edit merchant journey, first UI source pass (2026-10-10)

| Stage | Merchant action (code-inferred) | UI/source | Verification gap |
|---|---|---|---|
| Create | Open Product create; enter base fields and select category | `merchant/products/create/page.tsx`; active category fetch | Exact labels, validation and API outcome |
| Options | Add group (color, size, material, gender or custom), enter distinct chips | Same page; `variantCombinations` | Combination limits, generated Variant persistence |
| Images | Select/manage Product images | `ProductImageUploadSection` in create/edit | Storage, order, failed upload and provider handling |
| Edit | Load Product, change fields and Variants | `merchant/products/edit/page.tsx` | Full save lifecycle and rollback |
| Provider sync | Respond to confirmation dialog when new Variant not linked | `ProviderVariantSyncDialog` | Sync failure/retry and merchant-visible result |
| Variant edit | Update name/SKU/price/weight and optional image | `merchant/products/variant-edit/page.tsx` | Provider/storefront propagation |
| Product list | Inspect Product thumbnail, expand Variants, view/edit/delete | `merchant/products/page.tsx` | Live UI, delete guards, cross-store permissions |

**Evidence:** UI source only. Do not claim these flows work end-to-end before inspecting API and runtime.


## MJ-01 — Creation request to persistence (2026-10-10)

1. Merchant selects an active Store; UI rejects absent Store and redirects unauthenticated users to login.
2. Merchant enters Product, category, images and option groups. Browser builds matrix Variants and POSTs `/products/merchant-create` with ordered encoded image references and generated Variants.
3. Backend validates active Store, Merchant permission and active category; a Prisma transaction creates inactive Product and Variants, ProductStore link, taxonomy assignment and audit event.
4. UI refreshes Product list and redirects to Product Detail if matching Store link found; otherwise returns to Products list. Errors display generic `Product creation failed.` message.
5. Provider synchronization and activation are separate. Do not interpret the creation form's wording as automatic Accurate/Mayar provisioning.
6. Product image upload occurs before Product creation, with maximum five images / 2MB per file and Wossol-local storage. Potential abandoned-image lifecycle remains unverified.

**Deletion caveat:** Backend rejects Product deletion with Order history; otherwise it prepares/deletes each Accurate/Mayar provider target before locally archiving Product/Variants in a transaction. Partial provider deletion or local archive failure needs compensation/reconciliation proof.


## MJ-01 — Completion and partial failure (2026-10-10)

- After local inactive Product/Variants creation, backend sequentially links every Variant; all success => local activation transaction and merchant-safe `Product created.` response.
- On first non-success, attempts compensation for newly created external records. Successful compensation => local foundation archived; failed compensation => Product stays inactive with audit evidence. Merchant receives `Product creation did not complete.` rather than false success.
- `retryMerchantVariantProviderSync` permits retry for `FAILED_RETRYABLE` link operations only; `ACTION_REQUIRED` refuses blind retry and calls for review. A stale running claim is classified according to whether an external attempt started.
- Merchant outcome hypothesis: less ambiguity about product readiness and safer retry decisions. **Do not promote until runtime/UX validated**; external provider details remain internal.


## MJ-02 — Merchant-visible Product connection attention (2026-10-10)

1. Products list presents an attention badge when projection identifies an issue; Shopify-specific attention opens Product Detail > Connections.
2. Product Detail warns when Shopify Product is unlinked or partly linked, showing missing Variant count and review action.
3. Connections offers create unpublished Shopify draft, link an existing Shopify Product, inspect exact Variant mappings, link missing Variants manually or sync missing Variants when eligible.
4. Changing the Product link warns that Variant links will be cleared and require review; ending the link warns that the remote Shopify Product and Store connection are retained.
5. Failed status refresh or sync displays contextual message. No claim of guaranteed successful retry or fully automatic publishing.

**Important exclusion:** Products page intentionally does not expose internal provider retry/status controls, as checked by `provider-sync-recovery-ui.spec.ts` (test not executed). Do not market hidden backend recovery as merchant self-service.


## MJ-03 — Daily catalog maintenance (2026-10-10)

- Merchant can search by Product name/code or Variant/SKU, filter by category and Shopify connection state, and sort by newest/oldest/name.
- Product edit saves catalog fields first via `POST /products/merchant-edit`; if payment settings changed, it separately calls `POST /products/merchant-electronic-payment-settings`. On latter failure, UI explicitly says Product changes were saved but payment settings need retry.
- Missing Variant combinations are generated from option groups and existing signatures filtered out. The UI submits each new Variant separately. An attached new Variant image is restricted to one new combination.
- Variant edits use `PATCH /products/merchant-variants`; the dedicated Variant edit screen separately uploads or removes its image. If image operation fails after catalog PATCH, a partial save is possible; recovery UX must be checked.
- Merchant outcome candidates: clearer catalog organization and individual option control. No comparative speed/accuracy claims without user testing.
