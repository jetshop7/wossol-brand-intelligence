# Notifications V1.2 Migration Review — 2026-09-27

## Review metadata
- Section: Notifications
- Reviewed intelligence commit: `dd5074b9bfedac5077f9ee7e9e9748914494a849`
- Product evidence: current Notifications source challenged against `jetshop7/wossol-platform`; migrated audit records no Notifications-owned committed delta from the previously accepted implementation
- Prior authoritative review: `04-review-history/NOTIFICATIONS_REVIEW_2026-09-26.md`
- Methodology: V1.2
- Reviewer: ChatGPT — Strategic Reviewer / Quality Gate
- Decision: **ACCEPT WITH OPEN PRODUCT ISSUES**
- Full re-audit required: No
- Retroactive queue: RR-V12-020 completes V1.2 Quality Gate

## Prior accepted truth preserved

The migration preserves the V1.1 conclusion: Notifications is a bounded Merchant In-App attention-routing layer. It is not an owner-domain action engine, system of record, guaranteed real-time alert channel, omnichannel delivery service, intelligent prioritization engine or decision system.

The prior authorization, batching, pagination, External Shipping exception and Email boundaries remain intact and are not weakened by V1.2 wording.

## V1.2 merchant job / friction reduction

The strongest defensible work reduction is discovery/navigation effort.

For selected durable operational events, Notifications can:
- avoid requiring a merchant to continuously inspect every owner domain for every selected state change;
- consolidate selected cross-domain signals into one recipient-specific feed;
- preserve a target back to the owner workflow where authoritative work belongs;
- deterministically batch selected high-volume event types to reduce repeated feed rows.

This is qualitative coordination reduction. No evidence establishes fewer missed tasks, faster resolution, lower labor, higher revenue or measured productivity.

Notifications therefore reduces some **attention-search and navigation effort**, not the underlying owner-domain work.

## Tool / process consolidation

Current evidence supports a bounded consolidation of selected in-product signals.

It does not establish replacement of Email, SMS, Push, provider alerts, team chat, task management or operational monitoring tools.

The absence of an operational Email sender is particularly important: a stored preference/future gate is not a delivered channel.

## Context continuity / provenance

The useful chain is:

**owner-domain event → durable DomainEvent → policy/category/permission mapping → eligible recipient projection → recipient feed row/source-event linkage → owner-domain target route → read/click evidence.**

This preserves source identity and returns the merchant to operational truth rather than copying authority into Notifications.

That is meaningful context continuity, but the chain stops at navigation/interaction evidence. A click does not prove that the merchant completed the owner-domain action or improved an outcome.

## Control added

Merchant control is limited:
- recipient can view, mark read and click;
- selected batching reduces presentation noise;
- owner workflow retains actual execution authority.

Notifications itself cannot resolve the underlying Order, Inventory, Finance, Support or External Shipping issue.

Therefore visibility and navigation must not be described as operational execution control.

## Material authorization lifecycle gap

Director source verification reconfirms the prior finding.

Projection checks:
- active Merchant user;
- active Workspace membership;
- policy-specific domain permission.

Current `list`, `unreadCount`, `markRead`, `click` and `markAllRead` re-check active Workspace membership but do not re-evaluate the notification's current domain permission.

Therefore a persisted row can remain visible/actionable to a user who remains in the Workspace after losing the relevant domain grant.

This conflicts with the active V1 permission intent and prevents broad claims of permission-current notification safety.

Required Product resolution remains: define retained-history vs current-permission policy, then enforce it consistently across retrieval/count/read/click/bulk actions and tests.

## Attention-state weakness on updated batches

Director source verification reconfirms that an existing deterministic batch can absorb a later source event and update count/title/body/latest-event/target metadata without resetting `readAt`.

Thus a previously read row can contain newer information while remaining outside unread count.

This does not invalidate batching, but it prevents claiming that unread state always reflects newly arrived attention-worthy information.

Product must explicitly choose whether later events:
- reset unread;
- create a new row;
- or intentionally remain read.

## Pagination correctness gap

Rows are ordered by `createdAt DESC, id DESC`, but the cursor contains only `createdAt` and the next query uses `createdAt < cursor`.

Equal-timestamp rows at a page boundary can therefore be skipped.

The audit correctly treats this as a source-derived correctness risk rather than an observed production incident.

A compound cursor consistent with the ordering is the appropriate Product resolution.

## Event-processing starvation risk

The V1.2 audit adds a legitimate operational-recovery concern.

`projectPending` finds globally unreceipted events oldest-first with a bounded `take`. The event receipt is written only after the event's processing path succeeds (or after the code intentionally classifies it as unsupported/unscoped).

A repeatedly failing/poison event can therefore remain unreceipted and repeatedly occupy the bounded oldest window, potentially delaying later events. No bounded retry/dead-letter/operator recovery contract is established in the inspected path.

This is a source-derived **potential starvation/backlog risk**, not evidence that production delivery has actually stalled.

It materially limits reliability claims and should be resolved with explicit retry/dead-letter/replay/observability semantics.

## External Shipping exception

The previously accepted direct-write exception remains material.

External Shipping receipt-proof attention can create a Notification directly for one selected Merchant user rather than traversing the canonical event/policy/permission fanout path.

This must remain an explicitly bounded exception and must not be used as evidence that all Notifications share the normal projection guarantees.

## Operational → economic → decision value

Notifications routes attention to owner-domain operational truth. It does not calculate economic meaning, interpret priority, recommend an action, execute the action or measure its business consequence.

Read/click/source-event history can become useful future evidence, but no current outcome join establishes that notification exposure caused faster resolution, avoided loss or improved decisions.

Therefore current depth is:
**durable event → recipient signal → navigation/interaction evidence**, not Decision Intelligence or Learning Intelligence.

## Decision effort reduction

The product can reduce the effort of asking “what selected operational change should I inspect?” and navigating to the relevant owner domain.

It does not currently answer:
- which issue matters most economically;
- what the merchant should do;
- why one signal outranks another;
- what outcome followed the intervention;
- what should change next time.

Decision effort reduction is therefore limited to discovery/navigation, not interpretation or prioritization.

## Cross-domain compound value

Notifications is strategically useful because its value compounds with owner domains without taking over their authority.

Current policies connect selected signals from Orders/Confirmation, Inventory, Support, Finance and External Shipping into a common attention surface, then route back to those domains.

This supports a broader Wossol pattern:
**connected operational truth can be surfaced where attention is needed while execution remains with the authoritative workflow.**

The permission/reliability gaps prevent elevating this into a broad Trust/Reliability promise today.

## Claims strengthened / weakened / unchanged

**Strengthened:** Notifications is legitimate supporting evidence for reduced cross-domain scanning/navigation and context continuity.

**Unchanged:** it is not an action engine, intelligence system, guaranteed real-time alert system, Email/omnichannel delivery platform or hero differentiator.

**Further bounded:** current permission lifecycle, batch unread semantics, pagination and poison-event handling prevent “never miss what matters,” “always secure/current,” or guaranteed-delivery claims.

## Verification assessment

Recorded verification:
- backend Notifications tests: 11/11 passed;
- frontend workflow tests: 5/5 passed;
- backend typecheck passed;
- frontend typecheck passed.

These provide useful source-level regression evidence. They do not establish deployed migration parity, scheduler reliability, event lag, live browser behavior, production delivery or merchant outcomes.

The unrelated Product untracked script was correctly excluded.

## Open Product issues

1. Define and enforce current-domain-permission vs retained-history semantics across list/count/read/click/mark-all.
2. Define unread semantics when a later event joins an already-read batch.
3. Replace timestamp-only pagination with a tie-safe compound cursor.
4. Define poison-event retry/dead-letter/replay/operator-recovery behavior so one failing event cannot indefinitely delay newer events.
5. Add production observability for projection lag, backlog, failures and recovery.
6. Reconcile External Shipping's direct Notification write with canonical projection or explicitly secure/document the exception.
7. Align Email Settings/UI/docs with the absence of an operational sender until delivery exists.
8. Decide whether events should ever backfill users who become eligible after original projection.
9. Verify migration/schema state, scheduler behavior, multi-replica/concurrency and retention in deployment.
10. Measure event → read/click → owner action → outcome before productivity/reliability claims.
11. Competitive differentiation remains unverified.

## Claim / marketing safety

Safe current framing:
**Wossol can turn selected durable operational events into recipient-specific In-App signals that return the merchant to the owner workflow where the underlying work belongs.**

Do not claim complete visibility, real-time/guaranteed delivery, “never miss what matters,” permission-current security, intelligent prioritization, proactive prevention, Email/omnichannel alerts, measured productivity improvement or competitive superiority.

## Strategic / brand implication

Notifications is supporting evidence for **Operational Control + Reduced Merchant Work + Context Continuity**, specifically by reducing some cross-domain scanning and navigation.

Its strongest strategic role is **attention routing into operational truth**, not “notifications” as a hero capability.

A future stronger Trust/Guidance claim would require permission-current access, reliable event processing, stable feed semantics and measured action/outcome evidence.

## Methodology impact

No methodology change required. V1.2 correctly forces the distinction between surfacing information, reducing attention-search effort, enabling action and proving outcomes.

## Retroactive impact

RR-V12-020 has completed its V1.2 Quality Gate.

Home's inherited Notifications authorization concern remains corroborated. Owner-domain sections retain action authority. External Shipping's direct-write exception remains bounded.

No previously accepted section requires correction from this migration.

## Final decision

**ACCEPT WITH OPEN PRODUCT ISSUES.**

Notifications is V1.2-complete for intelligence purposes. No Codex correction or re-audit is required before proceeding to Support / Internal Chat.
