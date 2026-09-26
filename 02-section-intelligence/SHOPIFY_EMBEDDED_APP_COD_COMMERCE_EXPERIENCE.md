# Shopify Embedded App / COD Commerce Experience — Targeted Source Verification

## 1. Purpose and source state

This bounded verification is required by `04-review-history/PRODUCT_ROUTE_BACKEND_COVERAGE_RECONCILIATION_REVIEW_2026-09-26.md`. It supplements—not replaces or re-audits—the accepted Integrations / Commerce Channels review. Its exact source target is Product commit `4e26b4369e6416c22c731b8be706d72562a19d5b`; findings below are limited to that immutable committed tree.

- Product repository: `jetshop7/wossol-platform`.
- Verification target: commit `4e26b4369e6416c22c731b8be706d72562a19d5b`, branch `dev/wossol-integration` at the time of the Director review.
- Current Product workspace observed at final status check while applying this review: branch `dev/wossol-integration`, HEAD `97f9959bd2c6a05263a96a73b650fdc9a768dbd4`, aligned with upstream. The local working tree had fifteen uncommitted Advertising/Messaging files and no uncommitted Shopify files. Shopify source had advanced in committed changes after target `4e26b436`; those later Shopify changes and all current dirty files are **outside this verification**. No Product files were modified.
- Verification method: source was inspected and focused tests were run from a temporary extraction of commit `4e26b436`, keeping the moving/dirty Product checkout out of the reviewed Shopify files. Backend tests used `ts-node` in transpile-only mode; no typecheck or live Shopify/Admin/browser runtime was performed.
- Intelligence methodology: `MASTER_INSTRUCTIONS.md` v1.1; operating protocol v1.0. Competitive baseline: `WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md` v1.
- Canonical owner: `02-section-intelligence/INTEGRATIONS_COMMERCE_CHANNELS.md`, supplemented by this note.

## 2. Coverage map

| Surface | Status | Inspected evidence |
|---|---|---|
| Embedded app identity, launch and App Home | INSPECTED | App config, raw App Home route, App Bridge token client, Shopify ID-token verifier, launch/link service and focused launch tests. |
| Shopify Product list/detail and readiness | INSPECTED | Embedded Product configuration service, mapping/Store scope, UI source and management tests. |
| COD enablement and package-opening setting | INSPECTED | Embedded Product update path, readiness revalidation, audit write and service tests. |
| Product delivery-pricing controls | INSPECTED | Embedded policy adapter/service, Product/Store scope and UI tests. |
| COD Form and Appearance configuration | INSPECTED | Product-scoped configuration services, API controller, local preview/editor and extension projection. |
| Offers and Upsells | INSPECTED | Embedded editors/API, server validation/calculation, exact mapping, sequence, token and test surfaces. |
| Theme App Extension and storefront order execution | INSPECTED | Block, authored/generated runtime, signed App Proxy, bootstrap/quote/submit service, Commerce/Orders boundary, tests. |
| Schema, current Product contract, connected domains | PARTIALLY INSPECTED | Relevant COD configuration/Offer/Upsell models and migrations; current Shopify COD master consulted. Owner-domain implementation beyond the ingress boundary was not re-audited. |
| Deployed runtime/provider acceptance | NOT INSPECTED | No Shopify Admin session, live store, deployed version, browser run or provider call used. |
| Product changes after `4e26b436` | NOT INSPECTED | Explicitly outside the Director-requested source target. |

## 3. Capability truth at the target commit

The single `/shopify` Next page understates the merchant surface. `shopify.app.toml` configures Wossol as an embedded Shopify app (application URL `/shopify/app`) with an App Proxy and bounded app scopes. `/shopify/app` returns purpose-built HTML; App Bridge precedes Wossol's deferred `shopify-app-home.js`. The client obtains a fresh Shopify ID token and sends it as a bearer token for embedded API calls. The link flow verifies Shopify identity, creates a server-owned launch proof, and uses a Wossol Store-selection/link step. The browser does not select Workspace, Merchant, Store or canonical Product IDs.

Within Shopify Admin, the merchant can:

- browse the exact mapped Wossol Product set for the connected shop, search by title, filter readiness/COD state, and open a Product detail view;
- see `READY_FOR_COD` versus `NEEDS_SETUP` and safe reasons; incomplete or unavailable Shopify mappings are not silently promoted to orderable status;
- enable/disable Wossol COD for a ready Product and configure its open-package choice;
- inspect/change Product-level delivery-fee policy by provider zone/governorate, including resetting one or all overrides to Store policy;
- configure Product-level COD Form fields, labels, optional fields and field order; and
- configure persisted Appearance values, with a local preview, plus bounded Offer and Upsell editing, activation and ordering.

The Form/Appearance preview is an App Home preview, not a live Shopify theme preview. Saved values are projected by the Theme App Extension on the Product page. This is a Product-centric surface that depends on a pre-existing exact Store connection and Product/Variant mapping. It is not Shopify-originated Product linking, general Shopify catalog management, a native Shopify Checkout order inbox, or a cross-channel manager.

## 4. Authority, scope and workflow

### Identity and Store/Product scope

The backend verifies Shopify ID tokens (signature, configured audience, issuer/shop domain and expiry; the committed API spec includes substitution/rejection cases). Embedded COD APIs derive the connected `CommerceConnection` from the verified shop domain and require an unambiguous active connection. Workspace/Merchant/Store scope is then server-derived from that connection. Product lookup uses a validated human-facing Product code and requires an active Wossol Product linked to that exact Store. COD configuration and Offer/Upsell records carry connection and Product scope; external Shopify Product/Variant IDs must resolve through exact active mappings within that scope. Browser-supplied canonical Wossol IDs and monetary totals are not authority.

Regular Wossol merchant-management routes separately check `merchant.commerce.manage` and Store access. The embedded Shopify route family authenticates Shopify shop/session context and uses the Shopify subject in audit context; this verification did not establish equivalent per-user Wossol role/permission checks for each embedded mutation. Claim-safe language is “authorized Shopify shop context plus server-derived Wossol Store/Product scope,” not “the same Wossol role permission is enforced inside Shopify Admin.” Product authority should confirm whether Shopify staff access is the intended per-operator authorization boundary.

### Merchant configuration versus commercial execution authority

- COD cannot be enabled when current mapping/readiness checks fail. The server rechecks readiness on save and records material configuration changes in audit evidence.
- Product delivery-policy edits are zone-level customer-fee overrides/resets under the exact Product/Store context. Store delivery-pricing rules and server quote logic remain authoritative; an App Home input is not an Order total.
- Offer configuration uses bounded rules (automatic conditions or preset composition, rewards, activation/order). The server validates mapped Variants and deterministically evaluates active rules; this does not prove conversion or profit.
- Upsells are configured Product/Variant choices with bounded fixed-price rules. The server issues and verifies context-bound, expiring HMAC tokens and revalidates the active Upsell and exact external Variant mapping. Accept/Skip is customer-selected; sequence progress does not create intermediate Orders.
- The storefront Theme App Extension submits through the signed App Proxy. Backend bootstrap resolves the exact active shop connection, mapped Product, eligible Variants and canonical destinations. Quote/submit recompute Product/delivery/promotion amounts server-side. The accepted browser payload contains selection/customer evidence (external Product/Variant identity, quantity, destination, customer fields, Offer identity and tokenized Upsell selection), not canonical Wossol IDs or price/total authority.
- A valid final submit enters the existing Commerce → Orders import boundary and creates one canonical Wossol Order. Downstream Order/Inventory/Confirmation ownership is not transferred to the Shopify app. Optional outbound Shopify order projection remains distinct from native Shopify Checkout ingestion.

## 5. Merchant value and strategic boundary

For a merchant already operating a connected Shopify shop with mapped Wossol Products, this surface brings meaningful COD selling controls into Shopify Admin: readiness is visible; per-Product checkout and delivery presentation can be configured; Offers/Upsells can be arranged; and a customer COD submission hands off to Wossol's canonical Order workflow. This may reduce switching to a separate Wossol editor for these settings while preserving Wossol's operational ownership of Products, destinations, pricing and Orders. No time-saving measurement was found.

Value is conditional on setup and limited to the Wossol COD path. Unmapped Products are not editable here; linking remains a separate Wossol workflow. Offers/Upsells are configuration and execution mechanisms, not evidence of conversion lift. Appearance preview, rules or a persisted Order snapshot do not prove deployment, merchant adoption, successful real Shopify requests at scale, increased average Order value or profit.

Cross-section implications:

- **Products:** canonical Product/Variant state and exact Store links/mappings constrain readiness and selling eligibility.
- **Delivery Pricing:** existing Store policy plus Product overrides informs the customer delivery quote; Shopify presentation does not establish provider economics.
- **Orders:** one final submission crosses the Commerce boundary into the canonical Order; amounts are established server-side, not reconstructed from mutable current Offer settings.
- **Inventory / Confirmation / Tracking / Finance:** downstream workflow stays with those owners. The Shopify surface does not establish stock availability, confirmation success, delivery outcomes, settlement or delivered profit.
- **Advertising / Analytics:** storefront acquisition evidence can travel through the narrower existing contract, but this App Home does not establish campaign performance or an Offer-learning loop.

The competitive master treats commerce integrations broadly as common capability; this verification did not independently compare competitors' embedded Shopify control surfaces. Classify the merchant value as **material for Shopify COD operators** and competitive distinctiveness, adoption, outcomes and durability as **INSUFFICIENT EVIDENCE**. A safe claim describes configurable Shopify Product-level Wossol COD and its canonical Wossol Order handoff—not proven growth, an all-channel commerce OS, or superior Shopify conversion.

## 6. Weaknesses and uncertainty

1. The surface requires Wossol connection and exact mappings. Unmapped Shopify Products are not brought into this control surface; “manage every Shopify Product in Wossol” is unsupported.
2. Embedded ID-token/shop validation establishes shop context, but per-person authorization parity with Wossol's permission system was not found in this route family. Confirm whether Shopify staff access is the intended control.
3. No live browser/store run validated the full sequence from App Home save through Theme App Block rendering and customer submission at this target. The Product COD master reports one earlier base COD Order runtime milestone, but that report does not verify the later embedded configuration/Offer/Upsell depth.
4. Two committed tests expose source/contract mismatches: (a) `shopify-cod-acceptance.spec.ts` expects literal `trustedCommerceCommercialSnapshot ?? null`; committed `OrdersService` conditionally spreads the supplied snapshot when defined. This appears semantically equivalent, making the source-text assertion brittle, but the test still fails and should be aligned/re-run by Product. (b) `wossol-cod-form.spec.js` expects `variantText(selected, currencyCode)` for fixed-Variant Upsells. At this commit the Theme App Extension presents the selected Variant label in its static field and separately shows the configured fixed/reference amount; it does not include that exact full Shopify label/price formatter call. Confirm whether provider price/compare-at presentation is required or the test is stale. No Product change was made.
5. Exact target `4e26b436` is not the current Product HEAD. Subsequent committed Shopify source changes were deliberately excluded by the Director's exact-source request. This supplement cannot be cited as verification of current HEAD.
6. Native Shopify Checkout Order intake remains not found in the accepted Integration search. The Wossol Theme App Extension/App Proxy COD submission is a separate path. General inbound Order synchronization, continuous catalog/inventory sync and channel-wide recovery/reconciliation are not established here.
7. No live competitor comparison, deployment/runtime verification, adoption, conversion/AOV/profit measurement or legal/consent review was performed.

## 7. Verification performed

Tests were run against a temporary extraction of committed source `4e26b436`; this verifies testable implementation contracts, not live Shopify acceptance.

| Committed test group | Result |
|---|---|
| App Home launch/continuation, embedded management and Offer/Upsell frontend source tests | 40/40 passed. |
| COD Product configuration, Offers/Upsells, COD service and commercial-calculator backend unit tests | 76/76 passed with `ts-node` transpile-only. |
| `tests/shopify/wossol-cod-form.test.cjs` | 22/22 passed. |
| `extensions/wossol-cod-form/assets/wossol-cod-form.spec.js` | 24/25 passed; the fixed-Variant presentation assertion above failed. |
| `shopify-cod-acceptance.spec.ts` | 3/4 passed; the immutable snapshot source-text assertion above failed. |

Selected-test total: **165 passed, 2 failed**. No backend typecheck, database integration test, Shopify Admin session, live OAuth, storefront browser acceptance, provider call or production runtime was performed.

## 8. Evidence register

| ID | Evidence locations at Product commit `4e26b436...` | Evidence and limitation |
|---|---|---|
| EV-IC-023 | `shopify.app.toml`; `apps/frontend/src/app/shopify/app/route.ts`; `apps/frontend/public/shopify-app-home.js`; `apps/backend/src/modules/shopify/shopify-api.service.ts`, `shopify.service.ts`, `shopify.controller.ts`; App Home specs | Embedded app config, App Bridge ID-token flow, shop/session verification, launch and embedded routes. No production install/runtime proof. |
| EV-IC-024 | `shopify-cod-product-configuration.service.ts`; controller; `embedded-cod-management.spec.ts`; service specs | Exact connection/Store/Product/mapping readiness; COD/open-package configuration and audit behavior. Synthetic/unit/source tests. |
| EV-IC-025 | `shopify-embedded-product-delivery-policy.service.ts`; shared Delivery Pricing service; controller; embedded management specs | Product-governorate override/reset under derived Store/Product scope; shared service remains pricing owner. |
| EV-IC-026 | `shopify-embedded-product-form-configuration.service.ts`; appearance configuration/service; App Home script; extension block/runtime; `embedded-cod-management.spec.ts` | Product form settings/persisted Appearance and local-preview vs storefront-projection distinction. No live theme preview. |
| EV-IC-027 | `shopify-cod-offers-upsells.service.ts`; `shopify-cod-commercial-calculator.ts`; Offer/Upsell controller methods and specs | Exact mapping, server validation/calculation, fixed Upsell pricing/token flow, audit writes and ordering. No conversion/outcome evidence. |
| EV-IC-028 | `shopify-cod.service.ts`; App Proxy controller; theme extension source; `extensions/wossol-cod-form/**`; `tests/shopify/wossol-cod-form.test.cjs`; Commerce/Orders services | Storefront bootstrap/quote/final-submit authority and canonical Order boundary. No native Shopify Checkout import claim. |
| EV-IC-029 | `app-home-continuation.spec.ts`; `embedded-cod-management.spec.ts`; `shopify-offers-upsells-acceptance.spec.ts`; focused backend/storefront tests in Section 7 | Exact-target automated results: 165 pass, 2 source/contract assertions fail; no typecheck/live execution. |
| EV-IC-030 | `docs/integrations/commerce/WOSSOL_SHOPIFY_APPLICATION_COD_MASTER_SOURCE_OF_TRUTH_V1.md` §§1, 4–9, 19, 28, 31, 38, 43–47 | Current Product contract and reported base runtime milestone are P3/context; implementation plans and vision are not runtime proof. |

## 9. Disposition

This verification resolves the **coverage gap for source target `4e26b436`** identified by the Product Route / Backend Coverage review. It is ready to return for Director Quality Gate; it is not itself Director acceptance. Do not cite it as current Product HEAD coverage, verified deployed end-to-end configuration, competitive differentiation or measured conversion outcomes. The test/contract mismatches and shop-domain versus per-user authorization boundary remain explicit Product questions.
