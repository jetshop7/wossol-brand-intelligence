# Messaging / WhatsApp / Messenger / Order Capture V1.2 Migration Review — 2026-09-27

## Review metadata
- Section: Messaging / WhatsApp / Messenger / Order Capture
- Reviewed intelligence commit: `01473b594ab0f685f8f77f8fcc459cdeadbe4e77`
- Product evidence commit: `46716c433de40fbdbeb023d297d167c49909b380`
- Prior authoritative review: `04-review-history/MESSAGING_WHATSAPP_MESSENGER_ORDER_CAPTURE_REVIEW_2026-09-26.md`
- Methodology: V1.2
- Reviewer: ChatGPT — Strategic Reviewer / Quality Gate
- Decision: **ACCEPT WITH OPEN PRODUCT ISSUES**
- Full re-audit required: No
- Retroactive queue: RR-V12-002 completes V1.2 Quality Gate

## Prior accepted truth preserved

The migration preserves the core accepted boundary: Messaging is a narrow merchant-mediated handoff into canonical Orders, not a unified inbox, CRM, chatbot, automatic conversational-ordering system or full conversation archive.

Business-authored provider evidence remains the capture trigger. Customer-originated conversation is not ingested as an Order-capture source by the inspected path.

A Messaging capture remains only a scoped candidate. Orders retains authority over Store, customer/order facts, Product/Variant selection, delivery, payment and lifecycle, and consumes the capture transactionally.

WhatsApp remains materially narrower than Messenger and must not inherit Messenger capabilities by analogy.

## Product truth delta

The important current Product change is a real, bounded Messenger referral-provenance path.

Current Messenger source can:
- accept signature-verified referral events with allowlisted `ADS` / `SHORTLINK` metadata;
- persist immutable `AcquisitionReferralTouch` rows;
- later associate prior same-scope, same-participant referral touches to a merchant-authored Messenger capture;
- carry those references forward when that capture is manually completed into a canonical Order;
- conditionally resolve a single persisted Meta Ad identity against the scoped Advertising graph.

This materially strengthens provenance evidence compared with the prior review, where referral runtime was not established.

It does **not** establish conversation-level attribution, freshness-qualified attribution or causal advertising attribution.

## V1.2 merchant job / friction reduction

The current capability can reduce some re-entry and source-rematching work.

For Messenger:
- bounded captured handoff text can be retained for merchant review;
- selected referral provenance can remain attached instead of being manually reconstructed later;
- the merchant can complete the capture through the normal Order workflow without rebuilding the source handoff from scratch.

For WhatsApp:
- the reduction is much smaller: the authenticated `#WOSSOL` marker can create a reviewable capture candidate, but it carries no participant identity, conversation context or captured order lines.

The merchant still must complete/validate the actual Order. No automatic conversational parsing or customer-message-to-Order workflow is established.

## Tool / process consolidation

Current evidence supports only partial consolidation:
- Meta connection/onboarding can establish Wossol-owned Messaging credentials/connection identity;
- selected Messenger provider evidence can be preserved into a capture;
- the capture can hand off into canonical Orders.

This does not replace Messenger/WhatsApp inboxes, CRM, customer-service tooling, consent systems or advertising attribution platforms.

## Context continuity / provenance

The strongest current Messenger chain is:

**Meta referral event → immutable referral touch → later merchant-authored #WOSSOL capture → capture/referral link → manually completed canonical Order → conditional scoped Ad identity resolution.**

This is valuable because selected source evidence can survive the operational handoff.

However, Director source verification confirms major limits:
- `providerConversationId` remains null;
- association is based on scope + participant + referral timestamp not later than the marker event;
- no freshness/expiry window is enforced in the inspected join;
- all prior matching referral touches can be linked;
- provider `ref` is parsed but not persisted into the current touch record;
- multi-touch evidence does not itself establish a winning cause;
- exact Ad identity, when resolved, is identity evidence rather than causal evidence.

Therefore the capability is **provenance continuity**, not conversation attribution.

## WhatsApp channel boundary

Director source verification reconfirms that WhatsApp currently creates a capture only from authenticated Coexistence message echoes whose text exactly matches `#WOSSOL`.

The resulting capture has:
- no participant ID;
- no conversation ID;
- no captured lines;
- marker-only context.

This is a legitimate authenticated handoff marker, but its merchant value is narrower and must not be presented as WhatsApp conversational ordering, message ingestion or identity continuity.

## Control added

Merchant control remains important because the capture does not silently become an Order. The merchant reviews/completes the capture through normal Orders validation.

Server-side controls preserve:
- provider-signature/authorship boundaries;
- scoped Messaging connection ownership;
- replay/uniqueness identity;
- capture status/consumption;
- transactional Order consumption;
- server-derived provenance rather than trusting browser attribution.

This is meaningful integrity/control evidence.

It does not establish customer consent, marketing permission, customer identity or the correctness of an advertising causal interpretation.

## Advertising relationship / attribution boundary

Messaging and Advertising remain separate owner domains despite shared Meta infrastructure.

The current path can conditionally resolve a Messenger ADS referral external Ad ID against the persisted scoped Advertising graph.

That supports a qualified statement such as:
**“this Order preserved a Messenger referral identity that matched this Wossol-known Meta Ad.”**

It does not support:
- “this Ad caused the Order”;
- multi-touch winner attribution;
- campaign ROI;
- incremental conversion;
- revenue/profit attribution;
- full Meta Ads attribution.

The missing freshness window is especially material. An old referral touch can remain eligible for linking to a later marker as long as scope/participant/time ordering matches.

## Operational → economic → decision value

Messaging itself stops at provenance/capture and Order handoff.

Once a canonical Order exists, downstream Confirmation, Tracking/Delivery, Finance and Analytics can produce operational/economic facts. But Messaging does not itself interpret those outcomes or decide the value of a referral touch.

No current chain establishes:
- causal acquisition effectiveness;
- recommended campaign/channel action;
- automated messaging decision;
- outcome-learning loop.

Therefore current depth is **captured provenance → connected Order identity**, not Decision Intelligence or Learning Intelligence.

## Decision effort reduction

The capability can reduce the merchant's effort to answer:
- which bounded Messenger handoff this Order came from;
- whether selected prior referral evidence was preserved;
- whether one exact Ad identity can be resolved from that evidence.

It does not reduce the harder decision work of determining:
- which referral actually caused the purchase;
- whether the customer intended the purchase;
- which campaign deserves budget;
- what messaging action should happen next.

## Architecture / contract status

The migration appropriately records partial architecture reconciliation. Current architecture documentation now recognizes post-E1 webhook/capture/referral behavior, reducing the prior direct E1-only contradiction.

However, internal future-scope language and runtime/deployment uncertainty remain. Documentation alignment does not replace live provider verification.

## Verification assessment

Recorded verification:
- backend typecheck passed;
- selected backend tests: 93 passed / 1 failed.

The lone failure is an onboarding spec whose test mock lacks the now-required `acquisitionReferralTouch.findMany` delegate. This is consistent with a stale test harness relative to current source dependencies and is not, by itself, evidence of a runtime Messaging defect.

It remains an open regression-maintenance issue and should be repaired.

No DB-backed referral replay test, live Meta provider acceptance, browser acceptance or production behavior is verified.

The Product workspace's final local status could not be rechecked because of Git dubious-ownership protection. The reviewed canonical Product commit is nevertheless directly readable in GitHub and sufficient for this Director source challenge.

## Claims strengthened / weakened / unchanged

**Strengthened:** Messenger now provides real bounded referral-provenance continuity into a manually completed canonical Order.

**Strengthened:** server-side source integrity is stronger because browser attribution is not trusted for capture-based Orders.

**Unchanged:** the core product remains merchant-mediated, not automatic conversational commerce or a unified inbox.

**Unchanged:** WhatsApp remains marker-only.

**Newly bounded:** “messaging attribution” remains unsafe as a broad phrase because the current join lacks conversation identity/freshness and exact Ad resolution is not causality.

## Open Product issues

1. Define a freshness/eligibility window for referral-touch → later capture association.
2. Decide whether conversation/thread identity must be captured to support stronger continuity or attribution semantics.
3. Decide whether provider `ref` should be preserved and how SHORTLINK evidence should be interpreted downstream.
4. Define multi-touch attribution policy; current evidence preservation must not silently become a winner-selection rule.
5. Establish consent/contact/PII retention and downstream communication governance.
6. Define WhatsApp's intended context contract if marker-only capture is insufficient.
7. Repair the stale Messenger onboarding test mock and retain regression coverage for referral-touch dependencies.
8. Verify DB migrations/replay behavior, live Meta permissions/webhooks/subscriptions/retries and merchant usage.
9. Instagram runtime remains unestablished.
10. Conversation archive/unified inbox remains unestablished.
11. Advertising causality, incrementality and economic attribution remain unestablished.

## Claim / marketing safety

Safe current framing:
**Wossol can preserve selected authenticated Messenger referral and merchant-authored handoff evidence into a canonical Order without trusting browser-supplied attribution. WhatsApp currently provides a much narrower authenticated marker handoff.**

A safe demo may show:
Messenger referral evidence → later #WOSSOL merchant-authored capture → merchant review/completion → canonical Order → exact-or-unresolved Ad identity evidence.

The demo must explicitly state that the path does not prove the Ad caused the Order and does not preserve a full conversation.

Do not claim conversational commerce automation, customer-message ingestion, unified inbox, WhatsApp customer identity, consent-managed messaging, full/multi-touch attribution, causal ad conversion, revenue/profit attribution, AI message understanding, Instagram runtime or measured conversion lift.

## Strategic / brand implication

Messaging strengthens the working hypothesis around:
- Market/Commerce Access;
- Reduced Merchant Work;
- Context Continuity;
- Connected Commercial Truth.

The strongest present value is not “WhatsApp integration” or “Messenger attribution.” It is that **selected provider-origin evidence can survive a controlled merchant-mediated handoff into canonical operations**.

That is useful supporting proof for a broader connected-system story. It is not yet a hero attribution or conversational-commerce proposition.

## Methodology impact

No methodology change required. V1.2 correctly forces separation of source identity, preserved provenance, attribution, causality and economic outcome.

## Retroactive impact

RR-V12-002 has completed its V1.2 Quality Gate.

Orders remains canonical creation/lifecycle authority. Advertising remains the owner of persisted Ad identity/performance evidence and must preserve the difference between identity resolution and causal attribution. Customers remains authoritative for identity/consent boundaries.

No prior accepted section requires correction.

## Final decision

**ACCEPT WITH OPEN PRODUCT ISSUES.**

Messaging / WhatsApp / Messenger / Order Capture is V1.2-complete for intelligence purposes. No Codex correction or re-audit is required before proceeding to Analytics / Decision Center.
