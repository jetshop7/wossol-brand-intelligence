# Merchant Surface Coverage Review — 2026-09-26

## Purpose

Final Director coverage check before cross-section synthesis. This review compares the current merchant-facing Product route inventory at Product commit `16223bb5e5bd9cdde0d3e4be3f4f87a4075aa48b` against accepted Section Intelligence audits.

## Applications coverage

The current Applications hub exposes:
- WhatsApp — Workspace-scoped; covered by accepted Messaging / WhatsApp / Messenger / Order Capture audit.
- Shopify — Store-scoped; covered by accepted Integrations / Commerce Channels audit, with Shopify-specific Product/COD evidence also appearing in Products and Messaging/Advertising seams where relevant.
- YouCan — Store-scoped; covered by accepted Integrations / Commerce Channels audit.
- WooCommerce — Coming soon / non-interactive; no live capability audit required beyond the accepted Integrations boundary.
- Messenger — not an Applications card; connection management is consolidated under Advertising → Meta One Connect, while Messaging owns the connection/webhook/capture domain. Covered jointly by accepted Advertising and Messaging audits.

Advertising / Meta has its own accepted audit and includes OAuth/connection lifecycle, canonical provider hierarchy, reporting sync, provider-native Results, exact Wossol acquisition evidence, URL-tag health, explicit Product/Variant mappings, and bounded Meta CAPI Purchase dispatch. It is not merely a connection audit.

## Additional merchant-facing surfaces found outside the original audit sequence

The route inventory shows material merchant-facing surfaces not represented by a dedicated accepted section audit:

1. **External Shipping**
   - Merchant list/create/detail/edit routes exist.
   - This is a distinct operational workflow and should receive a dedicated audit before synthesis.

2. **Local Pickup**
   - Merchant list/create/detail/edit routes and Admin operational surfaces exist.
   - Sourcing / Network inspected Local Pickup deeply enough to distinguish it from sourcing, but Local Pickup is itself a substantial operating workflow with receiving, reconciliation, alerts and Finance effects.
   - A dedicated Local Pickup audit is warranted before final synthesis so its merchant value is not represented only as an adjacent negative boundary.

3. **Support**
   - Merchant ticket list/create/detail routes exist and link to Orders, Products, Inventory, External Shipping, Finance and account issues.
   - This is a cross-domain accountability/support workflow and should be audited for escalation, context linkage, evidence, permissions and lifecycle.

4. **Internal Chat / Confirmation Chat**
   - Merchant Confirmation Chat route exists and uses the shared Chat workspace, with a support handoff into ticket creation.
   - Confirmation audit covered staffing/confirmation operations but did not establish a dedicated Chat product audit.
   - Chat should be audited together with Support if the shared backend/domain shows they form one operational support/communication system; otherwise split after source inspection.

5. **Notifications**
   - Merchant Workspace-scoped notifications route exists with read/unread/category filters, deep-link targets, batching counts and mark-all-read behavior.
   - It may be a smaller cross-cutting surface, but it should be source-verified before synthesis because notification generation/attention routing can compound Home, Orders, Tracking, Inventory and Support value.

## Coverage conclusion

The original section sequence plus the added Messaging audit is not yet sufficient to declare Product-to-Brand section coverage complete.

Before master synthesis, the Director requires targeted section coverage of:
1. External Shipping
2. Local Pickup
3. Support / Internal Chat
4. Notifications (targeted audit; may be folded into Support/Attention Routing only if source evidence justifies one coherent domain)

No repeat audit is required for Advertising, Shopify, YouCan, WhatsApp or Messenger at this stage.

## Sequence

Recommended next sequence:
External Shipping → Local Pickup → Support / Internal Chat → Notifications → final route/backend coverage reconciliation → Master Synthesis.
