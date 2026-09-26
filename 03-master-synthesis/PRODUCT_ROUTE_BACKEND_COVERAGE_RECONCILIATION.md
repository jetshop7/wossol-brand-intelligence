# Product Route / Backend Coverage Reconciliation

**Date:** 2026-09-26  
**Purpose:** Final Product-surface coverage reconciliation requested by the Notifications review before Master Synthesis. This is an inventory and crosswalk, not a section re-audit or a synthesis of positioning claims.  
**Authority:** `04-review-history/NOTIFICATIONS_REVIEW_2026-09-26.md` requires reconciliation against all accepted section audits; `04-review-history/MERCHANT_SURFACE_COVERAGE_REVIEW_2026-09-26.md` supplies the prior surface-gap sequence. The accepted reviews and canonical section files remain the authorities for findings and review status.

## Product source state and limits

- Repository: `jetshop7/wossol-platform`.
- Branch: `dev/wossol-integration`.
- Committed HEAD observed: `4e26b4369e6416c22c731b8be706d72562a19d5b`.
- Upstream: `origin/dev/wossol-integration`; committed HEAD was aligned with upstream at inspection.
- Working tree: **dirty**. Fourteen Shopify-related source, test, and Product-document files were modified at inspection. They were read only and were not treated as part of the committed SHA or as Director-reviewed evidence. No Product files were changed by this task.
- Inventory method: enumerated `apps/frontend/src/app/**/page.{tsx,ts,jsx,js}` and `apps/backend/src/**/*.controller.ts`. Counts describe files/routes, not unique user-visible features, API operations, or runtime/deployment availability.
- Totals: **118 frontend page files** and **52 backend controller files**.

Frontend page-file counts by route root:

| Route root | Page files | Coverage interpretation |
|---|---:|---|
| `/admin` | 56 | Admin operational/configuration surfaces; owner domains below, plus privileged cross-cutting controls. |
| `/merchant` | 46 | Merchant operating surfaces; mapped to the accepted section audits below. |
| `/confirmation-worker` | 4 | Confirmation worker surface; Confirmation. |
| `/tracking-worker` | 4 | Tracking worker surface; Tracking / Delivery. |
| `/warehouse` | 1 | Warehouse receiving/handling surface; covered within External Shipping, not a separate unreviewed product domain. |
| `/track/[token]` | 1 | Public token tracking; Tracking / Delivery. |
| `/shopify` | 1 | Shopify embedded entry/configuration surface; Integrations / Commerce Channels, with Product and Advertising seams. |
| `/business` | 1 | Gated shell/entry context; not a distinct commerce capability. |
| `/foundation` | 1 | Gated foundational access/context; not a separate user-facing capability. |
| `/login` | 1 | Authentication entry; Auth/Team access boundary. |
| `/products` | 1 | Redirect to `/merchant/products`; no separate product surface. |
| `/` | 1 | Redirect to `/login`; no separate product surface. |

Backend controller files group by module as follows: Products 5; Messaging 5; Advertising 3; Tracking 3; Orders 2; Confirmation 2; Finance 2; Inventory 2; Support 2; YouCan 2; one each for health, admin-employees, admin-external-integrations, admin-fees, admin-inventory-product-support, admin-merchants, admin-platform-analytics, Analytics, Auth, Chat, Customers, Data Quality, External Integrations, External Shipping, Local Pickup, Market Center, Merchant Portal, Merchant Settings, Notifications, Product Connection Health, Shopify, System Override, System Settings, and Workspace Payment Configuration. Controller-file counts are not endpoint counts: several files expose multiple handlers, and public/worker/admin routes share domain controllers.

## Accepted-section crosswalk

All 20 planned section records exist under `02-section-intelligence/` and have corresponding Director review records under `04-review-history/`. This crosswalk reconciles route families and controller ownership to those records; it does not upgrade, broaden, or supersede an individual review decision.

| Accepted section | Route/backend families reconciled | Coverage outcome / boundary |
|---|---|---|
| Home | `/merchant` home/summary; notifications and analytics projections; workspace/store context | Cross-domain entry/summary, not a substitute audit for owner workflows. Home review corrections are represented by its canonical section record and authoritative correction record. |
| Products | `/merchant/products/**`; product lifecycle/controllers; Admin inventory/product support; Shopify and Advertising product-mapping seams | Merchant product lifecycle is covered; provider-linked catalog/mapping seams are shared with their owner sections. |
| Inventory | `/merchant/inventory/**`; inventory controllers and Admin inventory support | Inventory and reservation/provider boundaries covered; Warehouse receiving is separately included in External Shipping. |
| Orders | `/merchant/orders/**`; order/import controllers; Shopify COD order creation and capture-consumption seams | Canonical order lifecycle remains Orders-owned; channel capture/provenance and delivery effects remain in owner domains. |
| Confirmation | `/merchant/confirmation/**`, `/confirmation-worker/**`; confirmation and oversight controllers | Merchant and worker confirmation coverage reconciled. Shared chat/support handoff is covered in Support / Internal Chat. |
| Customers | `/merchant/customers/**`; Customers controller | Customer identity/profile and related consent/reputation boundaries covered. |
| Tracking / Delivery | `/merchant/tracking/**`, `/tracking-worker/**`, `/track/[token]`; tracking, simulation, public controllers | Merchant, worker, Admin oversight and public-token tracking are all mapped. |
| Finance | Merchant/Admin finance and fee routes; Finance, carrier-finance, fee controllers | Finance owner surfaces and cross-domain shipping/pickup financial effects are mapped. |
| Analytics / Decision Center | Merchant analytics and Admin platform analytics; Analytics, platform analytics, data-quality controllers | Both merchant and admin projection surfaces are mapped. `data-quality` is a backend controller with no distinct page route in the 118-page inventory; it is not evidence of a separate user-facing feature. |
| Market Center | `/merchant/market-center/**`; Market Center controller | Dedicated merchant surface and backend ownership mapped. |
| Advertising | `/merchant/advertising/**`; OAuth, Merchant Advertising and Meta client-backed controller surfaces | Accepted audit predates the committed One Connect consolidation; see the targeted source-delta note below. |
| Integrations / Commerce Channels | `/merchant/applications/**`, `/merchant/integrations/**`, `/shopify/**`; Shopify, YouCan and external integration controllers | Shopify and YouCan live surfaces are mapped; WooCommerce remains a non-interactive/coming-soon boundary in the accepted audit. Shopify COD-specific dirty-tree delta is unreviewed, below. |
| Stores | `/merchant/stores/**`; Store/workspace connections across owning controllers | Store/workspace scope is covered as a cross-domain authority boundary, not a duplicate integration audit. |
| Team | `/merchant/team/**`; Auth/session and Admin employee surfaces | Merchant access, membership and administrative employee boundaries mapped. |
| Sourcing / Network | Merchant sourcing/network and relevant Admin surfaces; market, inventory and external-shipping seams | Sourcing/network is mapped without reclassifying Local Pickup or External Shipping as sourcing-only capabilities. |
| Messaging / WhatsApp / Messenger / Order Capture | WhatsApp Applications, Meta Messenger Page connection, webhook and capture routes/controllers, Orders handoff | Accepted audit predates One Connect consolidation; targeted delta below. Internal Chat is separate and covered by Support / Internal Chat. |
| External Shipping | Merchant/Admin shipping and `/warehouse`; external-shipping controller | Warehouse queues, receiving, evidence, exceptions, labels and shipment operations are explicitly within this accepted review; no separate Warehouse audit is missing. |
| Local Pickup | Merchant/Admin pickup routes; Local Pickup controller | Dedicated route family and receiving/reconciliation/Finance seams are covered by its accepted audit. |
| Support / Internal Chat | Merchant support and confirmation-chat routes; Support and Chat controllers; Admin support | Ticket lifecycle, cross-domain context, shared internal chat and handoff mapped. |
| Notifications | Merchant notification feed and Admin notification-facing operations; Notifications controller plus event producers | Feed and producer paths mapped; review retains its open permission lifecycle, batch attention, pagination, direct-write, Email, reliability and outcome issues. |

## Cross-cutting and non-equivalent routes

- `/admin/whatsapp` is a session-local configuration preview: current UI text states automatic sending is inactive, Meta approval is required, and settings are not persisted/dispatched. It is not proof of a live WhatsApp sender and does not substitute for the accepted Merchant Notifications or Messaging surfaces.
- `/admin/system-override` is a privileged recovery/control surface spanning provider capability, tracking resync and product mapping correction. Map the underlying actions to their owning domains (Integrations, Tracking, Products); do not count it as a distinct merchant capability or infer that the controls establish successful recovery outcomes.
- `/business`, `/foundation`, `/login`, `/`, and `/products` are gated or redirect entry scaffolding, not standalone operating capabilities.
- Worker and public token routes are not absent merely because they are not nested under `/merchant`; they are included under Confirmation and Tracking / Delivery above.
- Controller-only modules without a unique route page (for example Health, Data Quality, Product Connection Health, and some integration/admin endpoints) are backend surfaces, not proof of a missing merchant screen. Their specific capabilities and permissions remain bounded by their owner-domain audits.

## Changes newer than accepted source snapshots

### Committed Meta One Connect consolidation

The accepted Advertising audit was inspected at `8600a4cbd1a894579a057b3476db35465289c670`; the accepted Messaging audit at `16223bb5e5bd9cdde0d3e4be3f4f87a4075aa48b`. At committed Product HEAD `4e26b4369e6416c22c731b8be706d72562a19d5b`, Meta One Connect changes consolidate Messenger Page onboarding into Advertising → Meta while retaining Messaging ownership of Page connection credentials, webhook/capture lifecycle, and exact permission checks. The former Applications → Messenger route redirects to Advertising Meta; the Advertising projection presents scoped Page connection status. The change does not establish Instagram messaging runtime or live Meta authorization/provider acceptance.

This is a material cross-domain source delta, not silently covered by the earlier acceptances. The relevant Advertising and Messaging section files now carry targeted source-delta addenda. This reconciliation is not a Director re-review; preserve the source-date distinction when using the flow in Master Synthesis.

### Uncommitted Shopify COD changes

At the same inspection, 14 Product working-tree paths were modified: `apps/backend/src/modules/shopify/shopify-cod-offers-upsells.service.ts`, its spec, `shopify-cod.service.ts` and its spec, `shopify.controller.ts`, `shopify.dto.ts`, `apps/frontend/public/shopify-app-home.js`, `apps/frontend/src/app/shopify/shopify-offers-upsells-acceptance.spec.ts`, `extensions/wossol-cod-form/assets/wossol-cod-form-runtime.js`, `shopify/theme-extension-source/wossol-cod-form/wossol-cod-form.js`, `tests/shopify/wossol-cod-form.test.cjs`, and three Product documentation files (`docs/PROJECT_EXECUTION_CONTROL.md`, `docs/engineering/ENGINEERING_CHANGE_LOG.md`, `docs/integrations/commerce/WOSSOL_SHOPIFY_APPLICATION_COD_MASTER_SOURCE_OF_TRUTH_V1.md`). The visible source delta adds a storefront preflight stage before the final submission/upsell flow and enforces additional Product-sequence upsell assignment rules. This may affect Shopify merchant configuration, storefront conversion flow and canonical Order creation seams owned jointly by Integrations, Products and Orders.

Because these changes are uncommitted and no accepted review examines them, this record does **not** claim their correctness, test status, runtime behavior, or coverage by prior Director decisions. Keep them outside accepted product claims until their source state is stabilized and reviewed through the normal owner-domain correction/review process. Do not misstate `4e26b436` as the complete inspected Product state.

### Targeted verification outcome — 2026-09-26

The Director's follow-up review required deeper committed-source coverage of the embedded Shopify App at `4e26b436`; this is now documented in `02-section-intelligence/SHOPIFY_EMBEDDED_APP_COD_COMMERCE_EXPERIENCE.md` and linked from `INTEGRATIONS_COMMERCE_CHANNELS.md`. It confirms material App Home capabilities beyond the route count and records exact-source tests, boundaries and open verification issues. The supplement is ready for its own Director Quality Gate; it is not an acceptance.

Product has since advanced to committed HEAD `97f9959bd2c6a05263a96a73b650fdc9a768dbd4` (upstream-aligned at final status check), with fifteen local uncommitted Advertising/Messaging files and no uncommitted Shopify files. Shopify files changed in committed history after target `4e26b436`; they were not expanded into scope because the Director specified the exact earlier SHA. Consequently this supplements coverage for `4e26b436` only and does not establish coverage of current Product HEAD.

## Reconciliation result

The route inventory has no remaining unexplained merchant, worker, public-tracking, Warehouse, or Admin route family: each is mapped to an accepted section or explicitly classified as scaffolding/cross-cutting backend control. The Director's previously requested missing audits (External Shipping, Local Pickup, Support / Internal Chat, Notifications) are now present and accepted.

**Coverage inventory: reconciled. Synthesis readiness: conditional.** The embedded Shopify App gap at Product target `4e26b436` now has a targeted source-verification supplement ready for Director review, but is not yet newly accepted. Current Product HEAD `97f9959` contains later Shopify changes that were not reviewed by that exact-target supplement; do not represent the old snapshot as current-source verification. Master Synthesis should wait for Director Quality Gate of the supplement and must not imply that One Connect received a new Director review or that the dirty Shopify source described at the original reconciliation was accepted. The product-wide route count is not evidence of capability completeness, reliability, production deployment, user adoption, or outcomes.

## Evidence and review provenance

- `04-review-history/NOTIFICATIONS_REVIEW_2026-09-26.md` — authoritative request for final route/backend reconciliation.
- `04-review-history/MERCHANT_SURFACE_COVERAGE_REVIEW_2026-09-26.md` — prior route gap inventory and acceptance sequence.
- Accepted section evidence and review records listed in `02-section-intelligence/` and `04-review-history/`; this reconciliation changes no prior Director decision.
