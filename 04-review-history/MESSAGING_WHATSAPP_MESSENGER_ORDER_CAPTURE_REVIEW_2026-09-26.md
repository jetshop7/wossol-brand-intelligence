# Messaging / WhatsApp / Messenger / Order Capture Review — 2026-09-26

## Review metadata
- Section: Messaging / WhatsApp / Messenger / Order Capture
- Reviewed intelligence commit: `f36ad91449971ece9609616816ffd10c5242d572`
- Product evidence commit: `16223bb5e5bd9cdde0d3e4be3f4f87a4075aa48b`
- Reviewer: ChatGPT — Strategic Reviewer / Quality Gate
- Decision: **ACCEPT WITH OPEN PRODUCT ISSUES**
- Full re-audit required: No

## What passes

The audit correctly identifies the current capability as a narrow, merchant-mediated handoff rather than a unified inbox, chatbot, CRM, conversational-commerce engine or automatic order-capture system.

Targeted Product verification confirms the critical authorship boundary. WhatsApp and Messenger capture paths accept authenticated provider events only under tightly bounded conditions and specifically evaluate business-authored echoes. Customer-originated messages are not treated as order captures by this path. This is a substantive safety/product boundary, not merely wording.

The audit correctly preserves the distinction between a Messaging capture and a canonical Order. A capture is a scoped, replay-safe candidate. Orders retains authority over Store selection, customer/order facts, Products/Variants, delivery, payment and lifecycle. The optional `messagingCaptureId` is validated inside the normal Order creation path and consumed transactionally, preventing one capture from silently bypassing Order rules or being reused as multiple Orders.

Channel asymmetry is accurately recorded. Messenger can carry bounded handoff text and limited display context, while WhatsApp currently creates a marker-only capture without participant/conversation context. The audit therefore does not over-generalize one channel's behavior to the other.

The connection/onboarding layer is also correctly bounded. Meta authorization and provider identity can provision Messaging-owned connections/credentials, but shared Meta infrastructure does not merge Messaging with Advertising authority, attribution or outcomes.

The audit correctly refuses unsupported identity and consent inference. A display name, Page echo or message marker is not a canonical Customer identity, marketing permission, communication consent or acquisition attribution. Prior Customers consent findings remain applicable.

The absence of a Wossol conversation archive/unified inbox is handled appropriately. No inspected source establishes thread history, two-way conversation synchronization, customer-message ingestion or a recoverable thread-level operating surface. Opening a generic provider inbox is not equivalent to preserving Wossol conversation continuity.

Instagram is correctly treated as non-runtime despite enum/filter vocabulary. Vocabulary or UI affordance alone is not evidence of a live provider path.

The E1 architecture conflict is materially preserved. P3 states an E1-only foundation with no public webhook ingestion or Capture-to-Order consumption, while P1 now implements both. Executable source establishes current behavior but does not itself prove the approved architecture was formally superseded. Product authority must reconcile/version the governing document.

Verification discipline passes: the canonical audit records 50 focused Messaging tests and 3 Orders capture-contract tests passing. It appropriately does not convert those into production Meta acceptance, webhook delivery, account eligibility or merchant-usage evidence.

## Open product issues retained

1. **Architecture status:** reconcile/version the E1-only Messaging master architecture against current webhook and Capture-to-Order implementation.
2. **WhatsApp handoff usefulness:** define the intended information/context contract for marker-only captures without inventing customer identity from missing evidence.
3. **Consent/contact governance:** establish capture, retention, lawful/consent basis and any downstream contact/marketing gates before using Messaging evidence for communication claims.
4. **Conversation continuity:** decide whether thread-level return/context is required; current generic provider inbox access is not equivalent to a Wossol conversation model.
5. **Operational scalability:** queue pagination, retention, alerting/monitoring and retry/failure behavior are not yet established as production-operable at scale.
6. **Instagram:** runtime onboarding/webhook support is not established and should remain explicitly unsupported until implemented.
7. **Referral attribution:** `AcquisitionReferralTouch` remains architecture/future evidence until a verified runtime producer/consumer path is established.
8. **Production Meta evidence:** live permissions, provider review, webhook delivery, coexistence behavior, retries and actual merchant usage remain unverified.

These issues bound product and marketing claims but do not invalidate the Messaging intelligence audit.

## Claim / strategic safety

Safe present territory is a verified, business-authored Meta handoff that can create a reviewable Messaging capture, with Messenger carrying bounded text and WhatsApp currently marker-only, followed by merchant-mediated canonical Order creation.

Do not claim customer-message ingestion, unified inbox, automatic conversational ordering, AI message understanding, inferred customer identity, consent-managed communication, Instagram runtime, messaging attribution, complete social-commerce automation or measured conversion uplift.

## Strategic implication

The strongest current compound value is not “WhatsApp integration” by itself. It is a controlled seam from provider-authenticated operator intent into canonical Wossol operations while preserving Order authority and replay safety. That may become strategically important when combined with Orders, Confirmation, Tracking and Finance, but current breadth and outcome evidence are too narrow to make it a hero claim by itself.

## Methodology impact

No methodology change is required.

## Retroactive impact

No prior accepted section requires correction. Advertising remains separate from Messaging despite shared Meta infrastructure. Customers consent/identity boundaries remain controlling. Orders remains the canonical authority for creation and downstream lifecycle.

## Acceptance

**Messaging / WhatsApp / Messenger / Order Capture passes the Director Quality Gate and is accepted with the open Product issues above.**

No Codex correction or re-audit is required.

Before declaring section coverage complete, the Director should perform a final Product route/application inventory to identify any material merchant-facing surfaces not yet represented by an accepted section audit.
