# Shopify Embedded App / COD Commerce Experience — Targeted Source Verification

## 1. Purpose and source state

This bounded verification was required by `04-review-history/PRODUCT_ROUTE_BACKEND_COVERAGE_RECONCILIATION_REVIEW_2026-09-26.md`. It supplements—not replaces or re-audits—the accepted Integrations / Commerce Channels review. Sections 1–9 preserve the exact original source target, Product commit `4e26b4369e6416c22c731b8be706d72562a19d5b`; the separately marked Section 10 records the subsequent targeted delta verification required by `04-review-history/SHOPIFY_EMBEDDED_APP_COD_COMMERCE_EXPERIENCE_REVIEW_2026-09-26.md`.

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

This map describes the original `4e26b436` verification scope only. The targeted later Shopify delta is covered separately in Section 10.

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
5. At the original target, `4e26b436` was not current Product HEAD; subsequent Shopify changes were excluded from that exact-snapshot pass. Section 10 records the separately required committed delta verification through `97f9959` (the current Shopify files at follow-up `fd7157f`). This supplement still does not establish deployed/live behavior.
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
| EV-IC-031 | Product `97f9959bd2c6a05263a96a73b650fdc9a768dbd4`: `shopify-cod.service.ts` / `.spec.ts`, `shopify.controller.ts`, `shopify.dto.ts`, `shopify-cod-offers-upsells.service.ts` / `.spec.ts`, `shopify-app-home.js`, `shopify-offers-upsells-acceptance.spec.ts`, Theme App Extension source/runtime/spec, Shopify storefront contract tests | Read-only preflight, phone validation, customer storefront sequencing, Upsell Variant write-time reservation, and the focused results/caveats in Section 10. No live-provider evidence. |

## 9. Disposition at Original Target `4e26b436`

This verification resolves the **coverage gap for source target `4e26b436`** identified by the Product Route / Backend Coverage review. It is ready to return for Director Quality Gate; it is not itself Director acceptance. Do not cite it as current Product HEAD coverage, verified deployed end-to-end configuration, competitive differentiation or measured conversion outcomes. The test/contract mismatches and shop-domain versus per-user authorization boundary remain explicit Product questions.

## 10. Current-Head Shopify Delta Verification — 2026-09-26

### Scope and source state

This is the narrow committed-source follow-up required by `04-review-history/SHOPIFY_EMBEDDED_APP_COD_COMMERCE_EXPERIENCE_REVIEW_2026-09-26.md`; it is not a full Shopify or Integrations re-audit. The requested comparison was `4e26b4369e6416c22c731b8be706d72562a19d5b` → `97f9959bd2c6a05263a96a73b650fdc9a768dbd4`. At verification, Product `HEAD` was `fd7157fe1a6ee03499e026373f263c3a15c831ef` on `dev/wossol-integration`, aligned with `origin/dev/wossol-integration`; the Shopify source files in this follow-up are unchanged from `97f9959`. The later `97f9959` → `fd7157f` changes are outside this Shopify evidence (Advertising/Messaging and related documentation). Product working tree was clean at inspection. The fifteen Ads/Messaging paths described in the Director review were not changed by this task and are excluded from evidence. No Product files were modified.

### Material committed changes and verified contract

The `4e26b436` → `97f9959` delta changes ten Shopify source/test paths. It adds a read-only customer preflight endpoint and DTO, calls canonical phone validation before returning a newly resolved quote/Upsell projection, and does not ingest an Order. The Theme App Extension now sends the base Product, Variant lines, destination, Offer selection and customer fields to preflight before opening the Upsell sequence. If no Upsells are returned, it proceeds to the one final submission; if customer input changes during the flow, it clears the pending preflight/Upsell state and requires a fresh pass. Submitted accepted-Upsell authority remains tokenized and is revalidated on final submit.

Upsell create/update now checks each mapped target Shopify Variant against other Upsells for the same source Product sequence, including inactive rows; those writes use serializable transactions and the editor filters already reserved Variants. The check is scoped by the exact connection/source Product/target Product query. The inspected `setUpsellActive` path does not perform this duplicate check, so cleanup/activation behavior for any pre-existing duplicate configuration was not established. This is a configuration constraint, not evidence that Upsells improve conversion or commercial outcomes.

The bounded claim boundary remains: preflight is validation plus a fresh server-owned projection, not an Order or downstream operational write; the final customer submission remains one Commerce → Orders ingress. This delta does not establish native Shopify Checkout intake, deployed/live runtime acceptance, conversion improvement, or per-user Wossol permission parity inside Shopify Admin.

### Focused verification at Product source `97f9959` (same Shopify files at `fd7157f`)

| Test group | Result |
|---|---|
| `shopify-cod.service.spec.ts`, `shopify-cod-offers-upsells.service.spec.ts`, `shopify-cod-acceptance.spec.ts` | 44/45 passed. New preflight validation/no-Order coverage and Variant reservation checks passed. The existing Orders snapshot source-text assertion still fails because it requires literal `trustedCommerceCommercialSnapshot ?? null`; Orders conditionally spreads the provided snapshot when defined. The semantic/source-pattern mismatch remains unresolved by this Product-only follow-up. |
| `shopify-offers-upsells-acceptance.spec.ts` | 19/19 passed, including Variant reservation behavior in the editor. |
| `wossol-cod-form.test.cjs` + `wossol-cod-form.spec.js` | 47/48 passed. Preflight transport/flow tests pass. The fixed-Variant assertion still expects `variantText(selected, currencyCode)`; current fixed-Variant UI renders a static Shopify Variant label and separately renders configured fixed/reference pricing. Whether this satisfies the intended presentation contract or omits a required combined provider label/price remains **UNRESOLVED**; the failing test is not dismissed as stale. |
| `pnpm shopify:cod:runtime:check` | Passed; generated storefront runtime matches authored source. |

Focused result: **110 passed, 2 failed** across the three test invocations containing tests (44/45 + 19/19 + 47/48); generated-runtime consistency check passed. No typecheck, Shopify Admin session, live store/browser/provider execution, deployment verification, or production outcome measurement was performed.

### Updated disposition

The two original focused failures **persist with materially the same meaning**: one is a brittle/stale textual test contract whose behavioral equivalence is plausible but not resolved in the Product repository; the other is an unresolved customer-facing fixed-Variant presentation contract and must remain an open issue. The new preflight and sequence-uniqueness paths have focused automated coverage, but these tests do not prove live provider behavior or eliminate the two prior caveats. This targeted source delta closes the specific current-HEAD coverage requirement in the Shopify review. It is **not Director acceptance**; Master Synthesis remains pending Director re-review. Do not make current-source, deployed-reliability, conversion, or comparative-superiority claims beyond the evidence above.
