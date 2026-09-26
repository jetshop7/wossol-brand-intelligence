# Support / Internal Chat Review — 2026-09-26

## Review metadata
- Section: Support / Internal Chat
- Reviewed intelligence commit: `cc216bdf35ac86451db9ae8ac83c26b001182c5c`
- Product evidence commit: `020593219001f857cace7bf80ad80fe2930319d4`
- Reviewer: ChatGPT — Strategic Reviewer / Quality Gate
- Decision: **ACCEPT WITH OPEN PRODUCT ISSUES**
- Full re-audit required: No
- Handoff note: the reported short SHA `cc216bd` resolves correctly; the handoff's expanded SHA omitted the `f` after `cc216bd`. GitHub canonical commit is the SHA above.

## What passes

The audit correctly separates two current systems rather than inflating them into one unified communications layer.

**Support Tickets** are a scoped case-management workflow with merchant intake, category-constrained owner-domain links, merchant/admin messages, authenticated image access, internal notes, lifecycle/status control, permissions, optional assignment API, timeline/audit evidence, response-window/reopen semantics and structured resolution episodes.

**Shared Chat** is a distinct Merchant/Confirmation/Admin operational conversation with its own authorization, message/read/attention model, urgency, Order linking and bounded operational inbox. It is not a private Support-staff room and is also distinct from the Meta Messaging/WhatsApp/Messenger capture domain.

The dispatch-aware channel boundary is meaningful and correctly represented. A linked Order that has crossed the authoritative communication eligibility boundary cannot continue through Shared Chat; the attempted send is blocked and the Merchant is directed toward Support. The UI handoff remains explicit/unsubmitted rather than silently auto-converting a Chat message into a Support Ticket.

The Support contextual-linking model is appropriately bounded. Category-specific links can carry eligible Order, Product/Variant, External Shipping and Finance context into a case without granting Support authority to mutate those owner domains.

The privacy distinction passes. Merchant-visible Support messages and authenticated attachments are separate from Admin internal notes. The audit does not market private operational notes as merchant communication.

The current “unread Merchant replies” label materially overstates its executable predicate and the audit correctly flags it. Targeted P1 verification confirms the count/filter merely asks whether an OPEN/IN_PROGRESS Ticket has any Merchant-authored message in the previous seven days. It does not compare the latest Merchant message with the latest agent reply and does not use an agent read cursor. It is therefore a recent-message heuristic, not unread truth.

The message-correction gap is also real. Admin Support selects and displays `correctedBody` / correction metadata, while Merchant Support's message projection selects the original `body` and omits correction fields. A corrected agent reply therefore remains the original text in the current Merchant projection. This is a communication-integrity Product issue, not an intelligence-audit defect.

The resolution-note boundary is correctly preserved. Admin resolution requires a human note and stores it with audit/timeline context, but resolving a Ticket does not itself create a Merchant-visible Support message and the Merchant projection does not expose that resolution note. “Recorded resolution explanation” must not become “resolution explanation communicated to Merchant.”

The structured `SupportResolutionIntelligence` record is a credible data foundation but not current intelligence. It records append-only resolution episodes with fields such as resolution/root-cause codes, responsible party, preventability and recurrence grouping. No inspected governed taxonomy quality, aggregate/read-side, outcome linkage, recurrence analysis or decision loop establishes learning or root-cause intelligence today.

Manual assignment is accurately classified as partial/backend capability. API, permission, agent lookup and assignment history exist, while the current Admin UI contract intentionally does not expose assignment as a normal V1 operator control. This must not be marketed as a surfaced workflow.

Strategically, Support is mainly foundational Trust/Accountability infrastructure. The more interesting cross-domain evidence is the explicit channel ownership and handoff: pre-dispatch operational discussion can remain in Shared Chat, while post-dispatch Delivery Issues move into scoped case management. That is useful control design, but competitive distinctiveness and service outcomes are not established.

Verification discipline passes: the canonical audit records 60/60 focused backend Support/Chat tests and 3/3 Chat polling checks passing. It appropriately excludes unrelated dirty Shopify/Meta Product changes and does not claim production storage, staffing, notification delivery or SLA outcomes.

## Open product issues retained

1. **Unread semantics:** replace the seven-day recent-Merchant-message heuristic with real read/latest-unanswered semantics or rename the UI/API concept.
2. **Correction visibility:** make corrected Support replies merchant-visible while preserving original/correction audit history, or remove/redefine the correction contract.
3. **Resolution communication:** decide whether the required human resolution note is internal evidence or Merchant communication and align projection/UI accordingly.
4. **Assignment contract:** reconcile the permissioned backend assignment workflow with its intentionally omitted current Admin UI.
5. **Resolution-data governance:** define controlled taxonomies, permissions/privacy, quality controls, read-side aggregation and outcome validation before treating resolution episodes as intelligence.
6. **Internal follow-up:** `WAITING_INTERNAL` and notes do not establish accountable cross-department task routing; decide whether such a workflow is required.
7. **Attachment/storage operations:** verify production shared storage, access hardening, retention, backup/recovery and abuse controls.
8. **Notifications/service quality:** verify delivery, staffing, first-response/resolution/reopen performance and merchant outcomes before reliability/SLA claims.
9. **Competitive depth:** no direct competitor workflow research establishes uniqueness or superiority of the Support/Chat design.

## Claim / strategic safety

Safe present territory:
- scoped Support Tickets with eligible operational/financial context;
- separate Merchant-visible messages and private internal notes;
- permissioned Admin case operations;
- shared Merchant/Confirmation operational Chat;
- explicit dispatch-aware Chat-to-Support boundary;
- append-only structured resolution evidence as a future data foundation.

Do not claim true Support unread tracking, corrected-message delivery to Merchants, automatic communication of resolution notes, automated internal routing, unified omnichannel inbox, AI/root-cause intelligence, proactive prevention, guaranteed SLA, measured service quality or complete workforce assignment.

## Strategic implication

This section reinforces a potentially important system pattern: Wossol does not merely expose multiple communication channels; it can encode **which operational channel owns an issue at a given lifecycle stage**, preserve linked business context and move the Merchant toward the appropriate workflow instead of allowing all communication to collapse into one generic chat.

That is stronger evidence for Control/Clarity/Accountability than “we have chat and support.” It is not yet proof of superior service because response quality, staffing and outcomes remain unverified.

The structured resolution episode is strategically interesting for the future because it could connect operational problems to causes and recurrence, but today it is data capture only. It must not be counted as Learning/Intelligence in master synthesis without a consuming analytical/outcome loop.

## Methodology impact

No methodology change is required. Preserve the audit's useful distinction between internal recording, Merchant communication and successful owner-domain outcome as three separate proofs.

## Retroactive impact

No prior accepted section requires correction. Orders/Confirmation communication ownership remains consistent. Messaging/WhatsApp remains a separate acquisition/capture seam rather than this operational Chat. Notifications should be audited next because Support/Chat publish events that may feed merchant attention routing, but event creation does not itself prove notification delivery or a coherent attention system.

## Acceptance

**Support / Internal Chat passes the Director Quality Gate and is accepted with the open Product issues above.**

No Codex correction or re-audit is required.

The next required coverage section is **Notifications**.
