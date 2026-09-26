# Notifications Review — 2026-09-26

## Review metadata
- Section: Notifications
- Reviewed intelligence commit: `5bcbf5d79399dde85dc84b1f2c14dbacbdb1b1c8`
- Product evidence commit: `44c16ede5be6ebf26c35acd429d1183979fe36b5`
- Reviewer: ChatGPT — Strategic Reviewer / Quality Gate
- Decision: **ACCEPT WITH OPEN PRODUCT ISSUES**
- Full re-audit required: No

## What passes

The audit correctly classifies Notifications as a bounded Merchant In-App attention-routing layer, not a second system of record, real-time alert guarantee, omnichannel delivery system or intelligence engine.

Targeted P1 verification confirms the normal projection path is meaningful: selected domain events map through an explicit policy catalog, eligible active Merchant users are checked for active Workspace membership and the policy's domain permission at projection time, recipient-specific rows are persisted, selected high-volume signals are deterministically batched, and targets return the operator to the owner workflow.

The audit correctly identifies a material authorization lifecycle gap. `list`, `unreadCount`, `markRead`, `click` and `markAllRead` re-check active Workspace membership but do not re-evaluate the row's current domain permission. Therefore a user who remains a Workspace member after losing a Finance/Support/etc. grant can retain access to already-projected notification content/targets. This conflicts with the current V1 API-time domain-access intent and materially bounds security/trust claims.

The batching attention gap is real. When a later source event joins an existing deterministic batch, the row's count/title/body/latest-event/target metadata are updated but its existing `readAt` is not reset. A batch that was already read can therefore absorb a new event while remaining absent from unread count. The audit appropriately treats the desired behavior as a Product-contract decision rather than assuming unread reset is always correct.

The pagination issue is also correctly established. Results are ordered by `createdAt DESC, id DESC`, while `nextCursor` serializes only `createdAt` and the following query uses only `createdAt < cursor`. Rows tied at the page-boundary timestamp can be skipped. This is a correctness risk even though its production frequency is unmeasured.

The External Shipping direct-write exception is accurately recorded. The valid-receipt-proof request chooses one active Merchant user and creates a Notification directly, outside normal source-event membership/policy permission fanout and without a target URL. This is not equivalent to the canonical projection path and should not be generalized as normal Notification behavior.

The Email boundary passes. Product Settings stores an Email notification preference and Notifications contains a future-sender gate, but no inspected sender/delivery-attempt writer establishes actual Email delivery. The UI preference therefore cannot support an “Email notifications” operational claim.

The audit correctly separates notification interaction telemetry from intelligence. Source-event membership, read/click state, batching and provenance could become useful evidence, but no current aggregate/outcome loop establishes prioritization intelligence, learning, prevention or decision quality.

Strategically, owner-domain navigation is the most useful current pattern: Notifications can surface selected cross-domain operating changes while keeping the authoritative action in Orders, Inventory, Support, Finance or External Shipping. This supports bounded Clarity/Control, not a hero feature or moat by itself.

Verification discipline passes: the canonical audit records 11/11 backend tests and 5/5 Merchant workflow tests passing, excludes unrelated dirty Product files and does not claim production scheduler/delivery outcomes.

## Open product issues retained

1. **Read-time authorization:** enforce current domain permission on persisted-row list/count/read/click/mark-all or safely hide/revoke inaccessible rows and targets.
2. **Batch attention semantics:** decide and implement whether a new event joining a previously read batch resets unread state, creates a new row or intentionally remains read.
3. **Tie-safe pagination:** use a compound cursor consistent with the `createdAt,id` ordering.
4. **External Shipping direct write:** move receipt-proof attention through canonical event/policy projection or explicitly define and secure the exceptional one-recipient contract.
5. **Email contract:** align Settings/UI/docs with the absence of an operational sender until delivery exists and is verified.
6. **Recipient timing:** decide whether globally receipted events should ever backfill users who become eligible after projection.
7. **Reliability/scale:** establish scheduler lag/failure/backlog observability, retention and scale behavior before reliability claims.
8. **Outcome evidence:** measure delivery-to-read/click/action and operational consequence before claiming reduced misses or faster decisions.
9. **Competitive depth:** no direct competitor Notification comparison establishes differentiation.

## Claim / strategic safety

Safe present territory is a recipient-specific In-App feed for selected cross-domain events, deterministic batching for selected event types and navigation back to owner workflows.

Do not claim complete visibility, real-time delivery, “never miss what matters,” always-current permission security, omnichannel/Email delivery, intelligent prioritization, proactive risk prevention, measured loss reduction or competitive superiority.

## Strategic implication

Notifications strengthens a cross-section pattern that should be tested in master synthesis: Wossol can keep authoritative work in domain-owned systems while creating bounded signals that return an operator to the place where the next action belongs.

This is more strategically useful as **attention routing into operational truth** than as “notifications.” However, the permission, read-batch and pagination gaps prevent elevating this into a broad Trust/Reliability promise today.

## Methodology impact

No methodology change is required.

## Retroactive impact

No prior accepted section requires correction. Support / Internal Chat correctly distinguished event publication from notification delivery. External Shipping's direct notification is now explicitly bounded as an exception. Owner domains retain action authority.

## Acceptance

**Notifications passes the Director Quality Gate and is accepted with the open Product issues above.**

No Codex correction or re-audit is required.

Before Master Synthesis, perform the required final Product route/backend coverage reconciliation against all accepted section audits.
