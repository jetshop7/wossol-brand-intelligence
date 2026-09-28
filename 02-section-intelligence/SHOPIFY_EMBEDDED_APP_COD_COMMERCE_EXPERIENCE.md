# Shopify Embedded App / COD Commerce Experience — V1.2 Migration Re-audit

## 1. Purpose and historical source state

This supplement began as a targeted verification required by `04-review-history/PRODUCT_ROUTE_BACKEND_COVERAGE_RECONCILIATION_REVIEW_2026-09-26.md`. Sections 1–9 preserve the original Product snapshot `4e26b4369e6416c22c731b8be706d72562a19d5b`; Section 10 preserves the accepted follow-up through `97f9959`. Section 11 is the requested incremental V1.2 re-audit at current Product HEAD. It does not replace the accepted Integrations / Commerce Channels review or claim live Shopify acceptance.

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

## 11. V1.2 Current-Head Migration Re-audit — 2026-09-27

### Source state and delta

- Product: `jetshop7/wossol-platform`, branch `dev/wossol-integration`, commit `2535c07e65b7d6fe047833b881fa2050d474b865`; `origin/dev/wossol-integration` matched HEAD. The working tree was clean at initial inspection; a later final check found an untracked `apps/backend/verify-incomplete-finalization.cjs`. Its contents were not opened or used, it is unrelated to this evidence, and it was not modified. No Product files were modified by this audit.
- Intelligence: `jetshop7/wossol-brand-intelligence`, `main`, synchronized clean at `f9e64bd616abf8819621ff25a193ca5bf3a69573` before work.
- Methodology: `MASTER_INSTRUCTIONS.md` v1.2; competitive baseline V1.
- The current Product commit adds no changes to the Shopify implementation beyond `97f9959`; the intervening `2535c07` changes only an Orders attribution integration spec and execution/change-log docs. The Shopify-relevant committed delta from `97f9959` to current HEAD is in the checkout-session, Offer/Upsell, schema/migration and customer storefront surfaces.

Current inspected areas include App Home/API configuration, Theme App Extension/App Proxy runtime, checkout-session DTO/routes/service/processor, Offers/Upsells configuration and calculation, Checkout Session/Order provenance schema and migrations, Order Projection replay, and focused frontend/backend tests. The canonical Product COD master and accepted Director review `SHOPIFY_COD_INCOMPLETE_CHECKOUT_RECOVERY_REVIEW_2026-09-27.md` were used for contract and cross-domain boundaries. No live Shopify Admin/store, browser session, deployed DB, provider, or production outcomes were available.

### Current Product truth and V1.2 value synthesis

The existing Product-level embedded configuration and Wossol-owned COD storefront remain materially as described in Sections 3–5 and 10: Shopify product-page context enters through a signed App Proxy, exact mapped Wossol Product/Variant and Store scope remain server-derived, base money is fetched/calculated server-side, and one final Commerce ingress creates the canonical Wossol Order. No native Shopify Checkout ingestion or broad catalog/inventory synchronization is established.

Current additions materially deepen the customer workflow:

- Durable Checkout Sessions persist scoped input/commercial/acquisition snapshots and an opaque continuation token; browser storage holds only that token. Revision checks and short leases fence concurrent sync, timeout and Upsell decisions. A background processor recovers due work from the database and performs no provider polling.
- Order intent freezes the base commercial snapshot and ordered Upsell sequence. Customer Accept/Skip decisions advance a persisted cursor; finalization uses a stable session-derived Commerce identity, normal Order ingress, checkout-origin provenance and a best-effort Shopify Order projection. An idempotent retry can return the finalized result/status URL without re-creating the canonical Order.
- Upsells now support fixed prices and current-Shopify-price-based percentage/fixed-amount discounts. The backend validates exact mapped Variants and discount bounds and recalculates amounts; it does not rely on browser-submitted money. Embedded configuration adds scoped deletion/resequencing and requires current Shopify prices before discount-rule activation.

**V1.2 job / consolidation / friction:** for a Shopify COD merchant, the connected product-page Wossol form and persisted continuation can preserve a customer’s product, destination, contact, offer and acquisition context. In principle this can avoid reconstructing an eligible interrupted selection; no prior merchant/staff recovery process was validated, and no work/time reduction was measured. This is built-in workflow/context consolidation, not proof that it replaces Shopify checkout, a provider, spreadsheet or separate recovery tool. It still requires Wossol connection/mapping and merchant configuration. For shoppers, the form remains in Shopify storefront context and no separate Wossol account is required at order time; live adoption/usability is not verified.

**Provenance and downstream chain:** bounded landing/referrer/UTM evidence → scoped Checkout Session → one canonical Order with immutable `COMPLETED_CHECKOUT` or `INCOMPLETE_CHECKOUT` origin → normal Confirmation/Inventory/Finance operations. The Director’s accepted recovery review confirms a separate recovery cohort for checkout-performance metrics while recovered canonical Orders remain included in financial/economic Analytics. This supports operational-to-economic continuity, not Meta Purchase reporting, full ad attribution, recommendation, optimization or learning. A distinct recovered-confirmation metric is evidence of measurement, not proof that recovery causes incremental sales.

**Important eligibility boundary:** code allows a due `COLLECTING` session with `orderReady=true` to finalize as `INCOMPLETE_CHECKOUT` after the 30-minute TTL, although no `Order Now` transition has occurred; an unready session is expired and its customer/acquisition snapshots are cleared. The shopper can enter a valid phone and a currently orderable selection before pressing the final action. The Product COD master source describes “eligible incomplete checkout” recovery but does not define this exact threshold or required notice in its reviewed text. The prior Director review accepts incomplete-checkout recovery semantics but still requires targeted browser acceptance. Merchant/customer notice, consent/intent threshold, denominator semantics, cancellation handling and production behavior for this pre-Order-Now conversion therefore remain explicit verification items; do not call every recovered Order an abandoned purchase recovered or assume incrementality.

**Proof / demo consequence:** show Product mapping/readiness and configuration → Shopify product-page quote → valid contact and Checkout Session → explicit Order Now and persisted Upsell Accept/Skip → one canonical Order, then separately show timeout recovery and its `INCOMPLETE_CHECKOUT` label. Demonstrate that a session may auto-finalize after timeout before explicit Order Now and explain the consequence. Show recovery analytics separate from standard checkout performance and inclusive of real economics. Do not imply native Shopify Checkout, guaranteed customer consent, a delivered order, profitable recovery or ad-platform Purchase events.

### Migration delta review

| Question | V1.2 finding |
|---|---|
| Prior truth retained | Exact mapped Product/Variant and Store scope, server-side quote/order authority, Offer/Upsell validation, one canonical Wossol Order handoff and owner-domain lifecycle remain supported. Prior open issues (shop-domain vs per-person access, fixed-Variant presentation test, live/browser acceptance and outcome proof) are preserved. |
| Product truth changed since last supplement | Durable session persistence, optimistic revision/lease handling, timeout recovery, persisted Upsell decision cursor, percentage/fixed-amount discount rules, Upsell deletion/resequencing, and forward SQL hardening were added after the `97f9959` supplement target. The latest Product commit `23fd265` → `2535c07` itself changes no Shopify source. |
| V1.2 value previously missed | More than configuration/integration: the workflow can preserve in-progress shopper and acquisition context through a resumable, server-authoritative sequence and link eligible recovery into canonical operational/economic outcomes. Its realized value and eligibility/intent boundary remain unverified. |
| Connected evidence added | Checkout Session → Order capture origin → Confirmation/recovery projection and distinct checkout-performance vs inclusive economic populations, per the Director-accepted recovery review at Product `23fd2657`. The most recent Product commit changes no Analytics/Confirmation source. |
| Strategic/marketing conclusion changed | The Shopify COD experience is now a stronger potential context-continuity and recovery-workflow foundation, but still not an established conversion differentiator or learning engine. Use “records eligible incomplete checkout context as a separately identified Wossol Order” only with clear qualification; no lift/sales claim. |
| Queue | RR-V12-019 marked UPDATED; current Director V1.2 Quality Gate pending. |

### Current verification

| Focused check | Result |
|---|---|
| Backend Shopify COD session, COD service, Offers/Upsells, commercial calculator and Order Projection tests | 64/64 passed. |
| `shopify-cod-acceptance.spec.ts` | 3/4 passed; the existing snapshot source-text assertion still expects literal `trustedCommerceCommercialSnapshot ?? null`. This is an unresolved brittle/contract test mismatch, not a demonstrated runtime pricing defect. |
| Shopify App Home / embedded management / Offers-Upsells frontend source tests | 46/46 passed. |
| Storefront CJS + Theme App Extension tests | 46/48 passed. Two failures remain: a test expects the pre-session `fields.requestSubmit()` flow; the current session/intent route does not use that contract. The fixed-Variant presentation expectation remains unresolved and is not dismissed as stale. |
| `pnpm shopify:cod:runtime:check` | Passed; generated runtime matches authored source. |
| Backend `pnpm typecheck` | Passed. |
| Prisma CLI schema validation | NOT RUN: the Prisma executable was unavailable in this workspace. Migration files were read and compared with the schema, but deployed migration state / DB parity was not inspected. |

Across these invocations: **159 passed, 3 failed**. No DB integration or live/browser acceptance. The forward hardening migration adds the previously missing session concurrency fields and scoped foreign-key constraints; deployment/application remains NOT VERIFIED. Continue to treat the checkout-session persistence contract as unverified in a deployed database until migration execution and DB-backed tests are demonstrated.

### V1.2 review disposition and open risks

1. Retain all earlier open issues, especially embedded staff authorization semantics, fixed-Variant shopper presentation, no native Shopify Checkout intake, and no live end-to-end evidence.
2. Preserve the earlier open question that `setUpsellActive` does not enforce the configured target-Variant exclusivity invariant for pre-existing duplicate Upsells; create/update checks are not a global guarantee.
3. Verify whether a still-open `COLLECTING/orderReady` session should create an Order after timeout without explicit Order Now, including customer notice/consent, duplicate/recovery behavior, cancellation and merchant-facing semantics.
4. Reconcile discount Upsell display with acceptance: available prices are calculated at projection time, while acceptance re-fetches current Shopify Variant price. A price change between display and Accept may produce a different charged line; freeze the accepted/displayed amount or refresh and disclose it if required by product policy.
5. Validate both current failing storefront assertions and the Orders snapshot assertion against the intended current contract; do not mark them resolved by inference.
6. Validate initial + hardening migrations, generated Prisma schema, scope constraints, cleanup/retention of customer PII and retry behavior in a real DB/deployed configuration.
7. Keep separate checkout recovery and standard performance populations; recovered canonical Orders remain economically real, but recovered-confirmed/captured is not incremental sales or ad attribution.
8. No current direct competitor depth check, conversion/AOV/profit measure, live install or production behavior establishes competitive advantage or a moat.

**Disposition:** V1.2 migration analysis is documented; Product truth remains source-backed, with the above product/runtime blockers visible. This is not Director acceptance. Current Director Quality Gate remains pending before synthesis may treat this migration as reviewed.

### V1.2 evidence register additions

| ID | Current source at Product `2535c07e` | Supports | Limitation |
|---|---|---|---|
| EV-IC-032 | `apps/backend/src/modules/shopify/shopify-cod.service.ts` (`syncCheckoutSession`, `confirmCheckoutIntent`, `decideCheckoutUpsell`, `processDueCheckoutSessions`, `finalizeCheckoutSession`); `shopify-cod-checkout-session.processor.ts`; session model and initial/hardening migrations | Opaque-token continuation; scoped session snapshots; 30-minute expiry; persisted intent/decision cursor; lease/CAS-protected recovery and one canonical Order boundary | Unit/source tests do not establish consent policy, deployed DB parity, retention, or live behavior. |
| EV-IC-033 | `shopify-cod-offers-upsells.service.ts` (`parseUpsellPriceRule`, `resolveUpsellUnitPrice`, `assertActivatableUpsellPriceRule`, `deleteUpsell`); embedded Upsell API/editor and tests | Fixed and percentage/fixed-amount discounts, server-side price calculation, mapped Variant validation, deletion/resequencing | No measured conversion/profit; shopper-visible price can be based on earlier Shopify price than the acceptance re-fetch. Existing activation path does not establish sequence-wide exclusivity for prior duplicate rows. |
| EV-IC-034 | `extensions/wossol-cod-form/assets/wossol-cod-form-runtime.js` and `.spec.js`; `tests/shopify/wossol-cod-form.test.cjs`; `scripts/build-shopify-cod-runtime.cjs` | Checkout session transport, customer-visible Upsell flow, generated-runtime consistency | Storefront focused specs 46/48 pass; failures retain pre-session submit-contract and fixed-Variant presentation questions. No browser acceptance. |
| EV-IC-035 | `apps/backend/src/modules/analytics/merchant-analytics.service.ts`; `apps/backend/src/modules/analytics/analytics.service.ts`; `04-review-history/SHOPIFY_COD_INCOMPLETE_CHECKOUT_RECOVERY_REVIEW_2026-09-27.md` (Director accepted at Product `23fd2657`) | Distinguishes incomplete recovery from standard checkout performance while preserving canonical recovered Orders in economic truth | Downstream Analytics was not changed by Product `2535c07`; no campaign attribution or learning loop. |

## 12. Current Product Capability Coverage Closure — 2026-09-28

**Source state:** Product `jetshop7/wossol-platform`, branch `dev/wossol-integration`, HEAD `007d317522d6e1eaa8e2e01d4a6d0608da812ce6`, aligned with `origin/dev/wossol-integration` at inspection. Four local uncommitted paths were present in Product; they were preserved and excluded from all conclusions. The intervening committed Product delta since the Director's `fd2b03f` coverage snapshot updates the Shopify App Home cache key to `shopify-test-product-badge-20260928-01`; the embedded management and quantity-editor spec changes visible in the local tree are uncommitted and excluded. The committed `fd2b` delta already added the read-only Test Product projection/badge, classification-drift `CHECKOUT_RESTART_REQUIRED` contract and safe stale-session token clearing/restart/finalization behavior documented in the preceding accepted V1.2 source analysis. This is a narrow source-state confirmation, not a Shopify re-audit; existing migration caveats remain.

The current Product capability closure and under-audited cross-cutting findings are recorded in `03-master-synthesis/PRODUCT_ROUTE_BACKEND_COVERAGE_RECONCILIATION.md` (2026-09-28 closure addendum), triggered by `04-review-history/CURRENT_PRODUCT_CAPABILITY_COVERAGE_REVALIDATION_2026-09-28.md` and `04-review-history/FINAL_PRODUCT_CAPABILITY_COVERAGE_GATE_2026-09-28.md`. New targeted supplements cover Settings/Delivery Pricing, Global Search, Growth Profile, Canonical Geography/Order Provenance, Workspace Payment Configuration and Workspace Operating Schedule. None upgrades backend data foundations into current intelligence or establishes deployed/live Shopify outcomes. Director Coverage Quality Gate remains pending; Final Master Synthesis has not been modified.
