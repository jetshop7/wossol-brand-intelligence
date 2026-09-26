# Product Route / Backend Coverage Reconciliation Review — 2026-09-26

## Review metadata
- Artifact: Product Route / Backend Coverage Reconciliation
- Reviewed intelligence commit: `4755ae7`
- Product committed HEAD inspected by reconciliation: `4e26b4369e6416c22c731b8be706d72562a19d5b`
- Reviewer: ChatGPT — Strategic Reviewer / Quality Gate
- Decision: **NEEDS TARGETED SOURCE VERIFICATION**
- Full re-audit required: No

## What passes

The route/controller inventory is useful and materially closes the previously identified route-family gaps. The crosswalk accounts for the enumerated 118 frontend page files and 52 backend controller files, preserves Warehouse within External Shipping, distinguishes scaffolding/cross-cutting controls, and correctly refuses to upgrade the newer committed Meta One Connect delta into a fresh Director acceptance.

The reconciliation also correctly excludes fourteen dirty Shopify files from the committed Product snapshot and from accepted claims. Uncommitted source must not be treated as canonical current capability.

## Material finding — Embedded Wossol Shopify App is not sufficiently covered by route mapping alone

The reconciliation maps the single `/shopify` page route to the accepted Integrations / Commerce Channels audit, but route counting understates the capability surface because the embedded App Home is primarily implemented through a route response plus `apps/frontend/public/shopify-app-home.js`, Shopify extension assets and backend Shopify services rather than many Next page files.

Targeted inspection of the **committed** Product HEAD `4e26b436...` — independently of the fourteen dirty files — establishes material Shopify App capability that is not represented with sufficient depth in the accepted Integrations audit:

- `shopify.app.toml` defines Wossol as an embedded Shopify app with App Home `/shopify/app`, App Proxy and Shopify scopes.
- The committed embedded-management test establishes Product management inside Shopify using Shopify ID-token scope without exposing browser Wossol IDs.
- The committed App surface includes Product search/readiness/COD state, Product detail, COD enablement and package-opening configuration.
- It includes Product-specific delivery-pricing controls.
- It includes embedded COD Form configuration and Appearance/live-preview behavior.
- It includes Offer and Upsell editing, activation/reordering and bounded Shopify Variant selection.
- Committed backend `shopify-cod-offers-upsells.service.ts` contains scoped Offer/Upsell commercial configuration, mapping validation, provider presentation lookup, fixed Upsell pricing requirements and audit recording.
- The committed storefront/theme-extension surface consumes bounded Offer configuration and participates in the customer COD experience.

The accepted `INTEGRATIONS_COMMERCE_CHANNELS.md` at its reviewed snapshot primarily characterizes Shopify as connection + exact mapping + Wossol COD storefront-to-canonical-Order bridge. It does not audit this later committed embedded commerce-management surface deeply enough to support synthesis conclusions about Merchant Agency, conversion tooling, merchandising, pricing/presentation control or the strategic significance of operating Wossol from inside Shopify Admin.

This is not cured by saying the route family is “mapped.” Coverage reconciliation must test material capability, not only file ownership.

## Dirty-tree distinction

The fourteen uncommitted Shopify files remain excluded. Their described preflight and Product-sequence changes require no review until committed/stabilized.

However, the committed embedded App capability above already exists at `4e26b436...`. Therefore synthesis cannot simply fall back to the old accepted Integrations snapshot and ignore it. A bounded committed-source verification is required.

## Required resolution

Perform a **targeted source verification of the committed Wossol Embedded Shopify App / COD Commerce Experience at Product commit `4e26b4369e6416c22c731b8be706d72562a19d5b`**.

The verification should:
1. inventory the committed embedded App Home, App Bridge/auth/linking, Product readiness/configuration, delivery pricing, Form/Appearance, Offers, Upsells and theme-extension/storefront behavior;
2. trace server authority, Store/Product/Variant scope and canonical Order boundary;
3. distinguish merchant presentation controls from commercial authority and customer-facing execution;
4. verify relevant committed tests/specs and current/final Product contract;
5. identify claim-safe merchant value, weaknesses, open Product issues and cross-section implications for Products, Orders, Delivery and Integrations;
6. explicitly exclude all uncommitted Shopify working-tree changes from findings.

Update the canonical Integrations / Commerce Channels intelligence (or create a tightly scoped Shopify embedded-app supplement linked to it, if methodology makes that cleaner) and return it for Director Quality Gate before Master Synthesis.

## Synthesis readiness

**NOT YET READY.**

The route/backend inventory itself is substantially reconciled, but material committed Shopify capability remains insufficiently audited. Master Synthesis should wait for this targeted source verification and Director acceptance.

## Methodology impact

No methodology change is required. This is an application of the existing small-feature/material-capability rule: route counts are an inventory aid, not proof that strategically material capability embedded in scripts/extensions/services was actually audited.

## Retroactive impact

No existing accepted section is revoked. The prior Integrations / Commerce Channels decision remains valid for its reviewed Product snapshot and claims. It is simply insufficient to cover the later committed embedded Shopify App capability for a current Product-wide synthesis.
