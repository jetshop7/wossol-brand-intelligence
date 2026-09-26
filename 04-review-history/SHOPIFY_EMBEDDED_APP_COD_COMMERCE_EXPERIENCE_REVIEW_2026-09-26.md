# Shopify Embedded App / COD Commerce Experience Review — 2026-09-26

## Review metadata
- Section supplement: Shopify Embedded App / COD Commerce Experience
- Reviewed intelligence commit: `524cb28`
- Verified Product snapshot: `4e26b4369e6416c22c731b8be706d72562a19d5b`
- Current committed Product HEAD additionally inspected for delta: `97f9959bd2c6a05263a96a73b650fdc9a768dbd4`
- Reviewer: ChatGPT — Strategic Reviewer / Quality Gate
- Decision on supplement: **ACCEPT WITH OPEN PRODUCT ISSUES**
- Product-wide synthesis readiness: **NEEDS TARGETED SOURCE VERIFICATION**
- Full re-audit required: No

## What passes at the exact verified snapshot

The targeted supplement closes the material coverage gap identified in the route/backend reconciliation review for Product commit `4e26b436...`.

It correctly establishes that Wossol's Shopify presence is not merely a connector. At this snapshot, the committed product contains a real embedded Shopify App Home and a customer-facing Theme App Extension that together form a bounded COD commerce-management and conversion surface.

The supplement appropriately covers:
- embedded Shopify identity/App Home and Shopify ID-token authentication;
- secure Shop → Wossol Store linking without browser authority over Workspace/Merchant/Store identity;
- exact mapped Product/Variant browsing and readiness;
- Product-level COD enablement and package-opening policy;
- Product-specific delivery-pricing controls;
- COD Form configuration and presentation controls;
- Form/Appearance preview behavior;
- Product Offers and ordered Upsells;
- mapped Variant restrictions and server validation;
- server-owned commercial calculation and revalidation;
- customer-facing Theme App Extension/App Proxy execution;
- one canonical Commerce → Orders creation boundary with Orders retaining lifecycle authority.

The strategic interpretation is appropriately bounded. This supports stronger Merchant Agency and commerce-conversion evidence than the original Integrations audit alone, but it does not establish native Shopify Checkout ingestion, autonomous optimization, measured conversion uplift, complete multichannel commerce or Shopify-owned operational truth.

The supplement also correctly distinguishes presentation control from commercial authority. Merchant UI can configure bounded Product-level experience, while the server retains scope, mapping, delivery-pricing, Offer/Upsell and final Order authority.

## Verification caveats accepted

The focused exact-snapshot run reported 165 passed and two failed tests. The supplement records rather than hides the failures:
1. a brittle/stale snapshot-style assertion;
2. an unresolved fixed-Variant presentation expectation.

These failures prevent treating the snapshot as fully green but do not invalidate the core source findings. No live Shopify runtime, backend typecheck or production-provider verification was performed in this targeted pass.

## Open product issues retained at the snapshot

1. Resolve the two focused test failures and determine whether the fixed-Variant presentation expectation reflects stale test intent or a real UI/provider-presentation contract gap.
2. Verify live embedded App behavior, provider permissions, deployed extension/App Proxy versions and merchant runtime before reliability claims.
3. Preserve exact Store/Product/Variant scope and server-side pricing/Order authority as the commercial surface evolves.
4. Do not convert Offers/Upsells into optimization or measured-conversion claims without outcome evidence.
5. Keep native Shopify Checkout ingestion distinct from Wossol COD storefront ingress.

## Current Product HEAD delta — material and in-scope

Product advanced from `4e26b436...` to `97f9959...` in one committed change that modifies the same Shopify surface verified by this supplement.

Director comparison confirms material committed changes in:
- Shopify COD service/controller/DTO;
- Offers/Upsells service and tests;
- embedded App Home;
- storefront runtime/tests;
- current Shopify COD master source of truth.

The delta is not merely cosmetic. It adds at least:
- a read-only authoritative storefront **preflight** before Upsell navigation/final submission;
- canonical customer-phone validation before entering the Upsell flow;
- explicit server rules preventing one canonical target Variant from being configured across multiple Upsells in the same source-Product sequence, including reservation by inactive Upsells;
- corresponding storefront/contract changes.

These changes strengthen/alter the exact customer conversion sequence and configuration authority that are strategically material to the Shopify supplement. Therefore the accepted `4e26b436` supplement cannot be represented as current-source verification for `97f9959`.

## Required resolution

Perform a **targeted source-delta verification from `4e26b436...` to current committed Product HEAD `97f9959bd2c6a05263a96a73b650fdc9a768dbd4` for the Shopify Embedded App / COD Commerce Experience only**.

The verification must:
1. inspect only committed Shopify changes in that delta;
2. verify the new preflight contract, customer validation, Upsell sequence/Variant uniqueness rules, storefront behavior and any changed claim boundaries;
3. run the focused Shopify tests relevant to the delta where possible;
4. update the Shopify supplement with a clearly marked current-head delta;
5. preserve the 15 unrelated uncommitted Advertising/Messaging files untouched and outside evidence;
6. state whether the two prior test failures persist, resolve, or change meaning.

No full Shopify re-audit is required.

## Synthesis readiness

**NOT READY YET.**

The exact-target supplement is accepted, but Master Synthesis should wait until the current committed Shopify delta receives this targeted verification and Director re-review.

## Methodology impact

No methodology change required.

## Retroactive impact

The original Integrations / Commerce Channels acceptance remains valid for its reviewed snapshot. The accepted Shopify supplement extends that evidence through `4e26b436`. A narrow current-head delta verification is now required solely because the same strategically material surface advanced after the exact-target review.
