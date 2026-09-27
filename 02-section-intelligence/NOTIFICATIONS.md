# Notifications — Product → Brand Intelligence Audit

## 1. Audit Metadata

| Field | Recorded state |
|---|---|
| Audit date | 2026-09-27 (first canonical section audit; V1.2 methodology applied) |
| Product source | `jetshop7/wossol-platform` — `C:\Users\Global Tech\Documents\wossol-platform` |
| Product branch / commit | `dev/wossol-integration` / `2535c07e65b7d6fe047833b881fa2050d474b865`; local `origin/dev/wossol-integration` tracking ref matched at inspection; no fresh Product fetch was performed |
| Product local state | One unrelated untracked file: `apps/backend/verify-incomplete-finalization.cjs`. No Product files were modified or opened as part of this audit. |
| Intelligence source | `jetshop7/wossol-brand-intelligence`, `main`, synchronized from GitHub to `f4cab503e9494470606e87d1d488d0f7e715ca9a` before repository reads; clean at start |
| Methodology | Master Instructions V1.2; Operating Protocol 1.1; `RR-V12-020` |
| Competitive reference | `WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md` (V1.0, 2026-09-09) |
| Verification performed | Notifications backend unit suite 11/11; Merchant Notifications source/UI contract suite 5/5; backend and frontend typechecks passed. No live authenticated browser, production delivery, database/migration deployment, or service-quality verification. |

**Audit status:** Current source review complete; separate Director Quality Gate remains pending. Production deployment, scheduler operation, actual user delivery, migration state, and merchant outcomes are **NOT VERIFIED**.

This is an **incremental V1.2 migration** of the existing 2026-09-26 V1.1 audit, not a restart. The prior audit's accepted product truth and evidence IDs `EV-NOTIF-001`–`EV-NOTIF-010` retain their original meanings. Its evidence snapshot was Product `44c16ede5be6ebf26c35acd429d1183979fe36b5`; no Notifications service/API/UI/spec/migration path changed between that snapshot and current Product `2535c07`. Product-wide schema changes in this interval belong to Shopify COD sessions, and MerchantShell changes concern Order Capture navigation badges. The current Product worktree has one unrelated untracked verification script; it was preserved and not opened.

## V1.2 Delta Review

- **Prior Product truth retained:** selected durable events become per-recipient In-App signals; owner systems retain authority; Orders/Inventory batching is deterministic; recipient read/click state is separate; Email preference is not delivery; Push, Analytics signals and Local Pickup remain deferred/not found in the active catalog. The three previously identified Product gaps remain: domain-permission revocation is not checked on read, new events added to an already-read batch do not reset its read state, and timestamp-only pagination can skip tied rows. The External Shipping receipt-proof request still has a direct one-recipient Notification write outside canonical projection.
- **Product delta:** no Notifications-owned code or active Notifications contract changed from the previous reviewed snapshot to current HEAD. Unrelated schema/Shell changes do not alter notification truth. Current authorization, scheduler and pagination behavior were independently rechecked.
- **V1.2 value newly extracted:** selected feed signals can qualitatively reduce manual attention discovery and destination-finding across multiple owner screens. This is coordination/friction reduction only; no prior process, time, missed-work rate or user outcome was measured.
- **Connected-domain evidence added:** confirmed the exact owner-route handoff and Home's recent-notification projection; incorporated Support/Chat review's warning that event creation is not delivery. Identified event-consumer failure isolation and cursor-tie recovery as cross-cutting notification reliability concerns.
- **Strategic conclusion:** prior framing remains valid and is sharpened: Notifications is attention/navigation continuity, not a reliable proactive-alert promise or intelligence layer. No section-level Product evolution supports stronger claims.
- **Queue:** `RR-V12-020` is updated with this V1.2 migration; Director Quality Gate remains pending.

## 2. Audit Coverage Map

| Surface | Status | What was inspected |
|---|---|---|
| Merchant Notifications full page | INSPECTED | `apps/frontend/src/app/merchant/notifications/page.tsx`, data client, source/UI workflow assertions |
| MerchantShell quick feed, badge and scope refresh | INSPECTED | `apps/frontend/src/app/merchant/MerchantShell.tsx`, quick feed and unread-count requests, workspace-change path |
| Authenticated API | INSPECTED | `apps/backend/src/modules/notifications/notifications.controller.ts`; list/count/read/click/mark-all routes |
| Policy and recipient projection | INSPECTED | `notifications.service.ts`: active catalog, event aliases, recipient eligibility, permission call, source membership, batch policy, receipts |
| Background processing / write gate | INSPECTED | `notifications.scheduler.ts`, `notifications.module.ts`, application module registration, WriteMode gate usage |
| Persistence | INSPECTED | Notification, source-event, event-receipt and delivery-attempt Prisma models; V1 migration SQL |
| Event producers / owner domains | INSPECTED, representative | Orders/Confirmation lifecycle, Inventory condition/cost, Support, Finance withdrawal, External Shipping event types and policy mappings |
| Error/retry/deduplication | INSPECTED | Receipt ordering, source-event uniqueness, batch uniqueness, retry behavior and service tests |
| Email / Push / Analytics / Local Pickup | INSPECTED | Current policy catalog, Settings preference, approved V1 architecture and active contract |
| Existing cross-domain intelligence | INSPECTED | Home V1.2 notification caveat; Team; Support/Internal Chat audit and authoritative review |
| Current UI/product intent | INSPECTED | `MERCHANT_NOTIFICATIONS_UI_SPEC.md`, `MERCHANT_NOTIFICATIONS_V1.md`; historical general Notifications System marked superseded |
| Production DB migration status, scheduler replicas, delivery latency, uptime/outcomes | BLOCKED / UNAVAILABLE | No live environment, production database, or operator telemetry provided |
| Authenticated browser / deployed UX | BLOCKED / UNAVAILABLE | No authenticated live Merchant browser session provided |
| Competitor-specific notification workflow validation | NOT INSPECTED | Competitive master supplies no sufficiently comparable notification-depth evidence; no uniqueness claim is made |

## 3. Executive Section Truth

Notifications is a **LIVE-in-code, asynchronous, per-user In-App projection** of selected owner-domain events. It converts a bounded allowlist of durable Orders/Confirmation, Inventory, Support, Finance-withdrawal and External Shipping signals into recipient-specific Workspace feed rows, optionally batches selected high-volume events, and routes clicks back to the owning merchant surface. Per-recipient read/click state is stored separately from source-domain truth.

The defensible value is **cross-domain attention and navigation continuity**, not a new operational system: one place to notice selected events and resume work in Orders, Inventory, Support, Finance or External Shipping. It does not perform the underlying action, guarantee immediate delivery, own business state, interpret analytics, or prove a resolution or economic outcome. Its current active channel is In-App. A Settings email preference is only an eligibility hook; no sender is implemented. Push is deferred.

Important qualification: policy permission is checked when events are projected, but list/count/read/click APIs currently re-check active Workspace membership only, not the per-domain permission used to project the row. Existing notification rows can therefore remain retrievable after that domain grant is removed, for as long as the user retains active Workspace membership. This conflicts with the active architecture/UI contract and the permission-safe claim must be qualified until reconciled.

## 4. Scope & Architecture Map

`Owner-domain transaction/event` → durable `DomainEvent` → `NotificationsScheduler` polling `NotificationsService.projectPending()` → allowlisted event/payload policy → active Merchant users + active Workspace membership + domain permission check → recipient-specific `Notification` and source-event membership → authenticated Merchant API → Quick Notifications or full page → server-returned owner-domain target.

| Layer | Observed role |
|---|---|
| Source truth | Orders, Confirmation, Inventory, Support, Finance and External Shipping own their facts and publish events; Notification is not the source of those facts. |
| Projector | Policy aliases/types select which durable events become merchant-facing signals and define category, generic title, batch behavior and target URL. Unsupported/missing-scope events receive a consumer receipt without creating a notification. |
| Recipient targeting | Active Merchant users are considered; the event Workspace must have an active, non-revoked membership; the corresponding permission is checked before new projection. |
| Persistence | Rows are per recipient and Workspace. Source-event memberships support provenance/idempotency; a unique batch key groups selected events. `readAt` and `clickedAt` are per-recipient signal state. |
| Delivery runtime | A Nest scheduler starts only when write mode accepts writes, then polls at a configurable interval (default 60 seconds, minimum 30 seconds). No WebSocket/push/email sender was found in this active V1 flow. |
| Merchant client | Workspace-level unread badge refreshes on a 60-second interval and after local notification-change events. Quick panel shows at most 15 recent rows; full feed loads 50 at a time. Store selection does not replace Workspace notification scope. |
| Owner action | Click is navigation only; destination APIs/sections retain their own access controls. A notification click is not an Order, Inventory, Support, Finance, or shipment mutation. |

## 5. Current Capability Inventory

| Capability | Status | Control depth | Merchant consequence |
|---|---|---:|---|
| Durable-event to In-App projection for active catalog | LIVE in source | 1 — Visibility | Selected source-domain changes can appear in one feed after asynchronous processing. Production delivery is not verified. |
| Recipient-specific Workspace projection and initial domain-permission filter | LIVE in source, with read-side gap | 2 — Configuration/eligibility | New rows are limited at projection; retrieval after permission revocation is not revalidated. |
| Per-user read/click state and Workspace unread count | LIVE in source | 2 — Personal signal control | Each recipient can distinguish seen/opened status without mutating source facts. |
| Selected Order and Inventory batching | LIVE in source | 1 | Reduces repeated rows; batch has deterministic two-hour UTC bucket, type, Store, Merchant, Workspace and recipient scope. |
| Server-computed deep links to owner routes | LIVE in source | 2 — Navigation | Reduces the work of finding a relevant owner screen; no direct action authority. |
| Full feed filters, pagination and mark-all-read | LIVE in source | 2 | User can narrow feed and clear read state; read state is Workspace-wide for mark-all. |
| Quick Notifications panel and count badge | LIVE in source | 1 | Recent attention is available without leaving the current Merchant page; badge refresh is periodic, not demonstrated real-time. |
| Event projection retries / source dedupe | PARTIAL | — | Durable receipts and uniqueness support retry safety, but per-event isolation and operational lag/failure visibility are not established. |
| Email | NOT ACTIVE / future gate only | — | User preference exists; no delivery sender in inspected current path. |
| Push | APPROVED FUTURE / deferred by V1 intent | — | No active Push dispatch found. |
| Analytics/Decision Center notifications | NOT FOUND AFTER SEARCH in active catalog | — | No current policy for recommendation/anomaly events; do not claim unified Decision Center alerting. |
| Local Pickup notifications | NOT FOUND AFTER SEARCH in active catalog | — | Current V1 design calls it future-contract-only; alert ownership remains in its source domain. |

## 6. Workflow & Lifecycle

1. An owner-domain workflow persists a `DomainEvent` (often transactionally with the underlying write).
2. The scheduled projector selects up to 100 unreceipted events in creation order. It resolves aliases and a hard-coded policy. Unrecognized or missing Merchant/Workspace events are receipted without projection.
3. For supported events it finds active Merchant users, checks active Workspace membership and the relevant permission, then writes a generic, merchant-safe row and source-event membership. Selected event types aggregate into a deterministic two-hour bucket.
4. The scheduler writes an event receipt after processing candidates. A failed event can be retried because it has no receipt; source-event/batch uniqueness is intended to prevent duplicate effects.
5. MerchantShell requests the active Workspace unread count periodically. Opening the Quick panel or full page lists recent rows; filters and pagination are server-side. Mark-all and click update only recipient notification state.
6. A row click obtains the stored server target and navigates to the owning section, which independently enforces access and owns the eventual work.

## 7. Value Recipient Map

| Recipient | Current value / boundary |
|---|---|
| Merchant owner/operator | One Workspace-scoped feed for selected operational signals and a path back to owner workflows. Still must inspect and act in those workflows. |
| Authorized Staff | Can receive only events whose permission was granted at projection time; subsequent revocation is not applied to existing feed reads in current code. |
| Operations / Inventory / Finance users | Potentially less repeated manual checking of selected high-level changes; coverage is intentionally incomplete and not an SLA. |
| Support-facing Merchant | Can resume a supported Ticket reply/resolution context; internal notes and non-merchant operational noise are not in the active notification catalog. |
| Wossol operations | Domain event and projection separation avoids making a generic notification row the owner of operational truth; system staffing/service outcomes remain unmeasured. |
| End customer / provider | No direct notification value established; the audited channel is the Merchant In-App surface. |

## 8. Control & Merchant Agency

Control is deliberately narrow: users can filter, mark signals read, and navigate to a separately authorized owner workflow. The system does not expose user-defined notification rules, direct actions, channel-level controls, per-type preferences, snooze/escalation, or recipient assignment. This restraint keeps signals from becoming duplicate operational commands, but the permission-revocation gap weakens the intended ongoing access boundary.

## 9. Transparency & Trust

**Trust-supporting mechanisms:** event-to-notification source membership; recipient and Workspace scoping; domain permission filter at projection; separate personal read/click state; policy allowlist; generic safe content; server-returned navigation target; deterministic selective batching; unsupported/noisy events excluded.

**Limits:** notification rows are projections, not the detailed event history or current owner status. Batches summarize signals rather than mutate them. The feed does not show delivery latency, source processing errors, stale target status, actor/reason evidence, or a resolution outcome. Historical category values without a UI chip can appear as “Other update.” The API returns the entire Prisma row rather than an explicit merchant DTO, while the current client consumes only a subset; this unnecessarily broad response shape should be reviewed for data minimization even though the active policy populates mostly generic content.

## 10. Merchant Value Extraction (V1.2 Second Pass)

| V1.2 lens | Finding | Boundary |
|---|---|---|
| Merchant job removed/reduced | Reduces repeated manual checking of several owner surfaces for the subset of events that has a notification policy; reduces locating a destination after noticing an event. | Does not remove the need to inspect/resolve the case in its owner system; no measured time or missed-event reduction. |
| Tool/system consolidation | A single in-product feed consolidates selected attention signals from multiple owner domains. | It is a projection/convenience layer, not a replacement for Orders, Inventory, Support, Finance, shipment tools, email, or provider systems. |
| Friction/steps | Quick panel and safe deep link can remove a manual route/search step; category/read filters and batching reduce feed noise. | No tested end-to-end step count, latency, or action completion rate. |
| Context continuity | Event identity and target survive from source event through recipient-specific row into an owner-domain route. | No conversation/session/acquisition context graph or return path is established; batch rows do not expose individual event previews in the current spec. |
| Provenance/truth | Source event IDs and source-membership rows link projected signals back to event identity; owner domains remain authoritative. | Merchant UI does not expose a forensic provenance timeline; stored row does not prove current event state or user action completion. |
| Decision effort | Helps answer “what changed / where should I look?” for explicitly supported event types. | No explanation, prioritization across domains, recommendation, diagnosis, consequence calculation, or decision-effort evidence. This is attention/navigation, not Decision Intelligence. |
| Connected value chain | Order/Inventory/Support/Finance/Shipping event → scoped signal → owner workflow; Notifications also contributes recent context to Home. | No demonstrated connection to delivered profit, customer quality, marketing attribution, Analytics recommendations, or outcome learning. |
| Proof/demo consequence | Demo can show one supported source event projected, batch/read state, and click-through to the owner route. | Without live event generation and an authenticated deployed session, this is a source-based demo proposal, not completed runtime proof. |
| Future compounding | Consistent event identity and source memberships could support delivery/outcome diagnostics or preferences later. | Opportunity only; current history is not analyzed into intelligence and delivery attempt model has no proven active channel sender. |

## 11. Feature Clusters & Cross-Section Compound Advantages

### A. Durable signal → personalized feed → safe continuation

**Participants:** DomainEvent, supported policy, recipient/Workspace checks, Notification row, read/click state, owner deep link.

**Combined effect:** lets an authorized user see selected lifecycle updates and continue in the owner domain without transferring business authority to the feed. This is a small but real workflow-continuity benefit.

**Compound weakness:** the Product currently checks domain permission before creating the notification but only Workspace membership when reading it. Thus provenance/attention continuity is better implemented than ongoing authorization continuity.

### B. Selective batching → bounded attention noise

Order lifecycle and Inventory LOW/OUT_OF_STOCK transitions use a deterministic two-hour UTC bucket and retain source-event memberships. Batching reduces duplicate rows, but a batch remains summary/navigation rather than a complete source-event preview. No measured reduction in alert fatigue is available.

### C. Home ↔ Notifications

Home composes at most three recent Workspace notifications and isolates notification failure from its other panels. This can reduce first-look navigation effort. Home's V1.2 audit separately notes that its projection inherits Notification API permission/data-minimization concerns. Home must not be described as a stronger authorization boundary than the Notifications owner service.

### D. Support / Internal Chat boundary

The accepted Support/Chat review establishes operational Chat and Support as distinct channels and explicitly warns that published events do not prove notification delivery or coherent attention routing. Notifications can carry selected Support events, but it does not establish staffing, first response, resolution, or cross-team routing.

## 12. Merchant Journey / Old Way vs Wossol Way

| Journey point | Without this projection (reasonable alternative, not measured) | With current Notifications | Remaining work / risk |
|---|---|---|---|
| Monitor | Revisit owner pages or rely on another communication/checking habit for events those systems expose. | Periodically refreshed in-product unread badge and feed for the allowlisted subset. | No active email/push; scheduler/delivery timing and missed-event rate unverified. |
| Find context | Navigate to a relevant owner page and locate the object/filter. | Open a stored deep link, often with status/date/Store filter or entity ID. | Destination freshness, route authorization and actual task resolution remain in owner domain. |
| Triage bursts | Scan repeated event rows. | Selected Order/Inventory events batch. | No batch preview; no user threshold or per-type preferences. |
| Recover / improve | Manually compare what was noticed against later outcomes. | No Notification-owned outcome or learning workflow. | Outcome reconciliation and service-quality monitoring remain absent/not established. |

This supports a qualitative reduction in attention-discovery friction only. It does not prove a replacement for a user's prior tool, a measured productivity gain, or reliable alerting.

## 13. Hidden / Non-Obvious Advantages

- Event-sourced projection preserves the separation between signal and operational authority.
- Source-event membership and unique batch identities provide a foundation for duplicate-resistant projection and traceability.
- Eligibility is scoped by recipient, Merchant event context, Workspace and domain permission at projection time.
- Generic policy content avoids copying operational/provider payload wholesale into the feed.
- Historical support aliasing allows selected legacy event names to resolve into the active policy catalog.
- Notification batching is selectively applied; noisy worker/attempt/ledger/analytics events are excluded rather than indiscriminately surfaced.

These are source-level safeguards/foundations, not proof of production reliability or a competitive moat.

## 14. Data & Intelligence Assets

**Captured:** recipient User, Workspace/Merchant, type/category, Store, source event identity, target identity/path, generic title/body, batch count/window/latest event time, creation/update, read/click times, source-event membership and receipt. A delivery-attempt model exists in schema.

**Connected:** event → recipient signal, with some type/Store/time navigation context. No current aggregate consumes these records to measure notice, action completion, unresolved duration, economic impact, or learning.

**Intelligence depth:** attention visibility only. The schema's delivery-attempt table does not establish Email/Push dispatch; no sender/worker or active delivery-attempt use was found in the current Notifications flow. Merchant/Market/Decision/Learning Intelligence are not established.

## 15. Competitive Analysis

The competitive master contains no verified direct-competitor evidence at comparable recipient, event, batching, authorization, history, and delivery depth. Classification: **INSUFFICIENT EVIDENCE** for parity, superiority, uniqueness, or differentiation. A cross-domain in-product signal feed is useful but common enough that feature presence alone is not a defensible claim.

## 16. Marketing Intelligence

| Audience / pain | Current truth and proof | Safe angle / eligibility |
|---|---|---|
| Merchant operator loses time finding where a selected update belongs | Source policy maps supported event types to scoped owner routes; backend tests cover mapping and UI presents Quick/full feeds. | “Keep selected operational updates and their next destination together.” **YELLOW / SUPPORTING PROOF ONLY** until live/browser and permissions are verified. |
| Merchant wants fewer repeated feed rows during bursts | Deterministic two-hour batching for selected Orders and Inventory events with source membership. | “Group selected repeated updates.” Avoid “smart alerts,” universal noise reduction, or alert-fatigue outcomes. **YELLOW** |
| Merchant expects reliable proactive alerts across every domain/channel | Active catalog is bounded and In-App only; no SLO/latency, Email sender, Push or Analytics/Local Pickup policy established. | **DO NOT CLAIM** real-time, guaranteed, all-channel, proactive intelligence, full-domain coverage, or successful follow-through. |

**Demo moment candidate:** emit a known supported owner event in a controlled environment; show recipient/Workspace projection, batching/read state, then click through to the owner route. Prove authorization with both allowed and revoked Staff states before demonstrating broad access safety.

## 17. Surprise Findings

The strongest non-obvious finding is the duality of the design: Notifications is appropriately subordinate to owner domains and source-event-backed, but its API read-side no longer enforces the domain permission used to create the signal. The second is that scheduler polling and persisted rows create an asynchronous attention pipeline, not an instantaneous alert channel. Both facts matter more than the visible badge itself.

## 18. Potential Category Reframes

**Potential only:** “one operational attention layer that routes users back to systems of record” could challenge a fragmented multi-page monitoring habit. Evidence currently supports selected signal consolidation, not an integrated command center, omnichannel communications product, proactive operating system, or superior service reliability.

## 19. Brand Evidence

- **Brand truth today:** bounded continuity, separation of signal from source truth, and some recipient-specific operational visibility.
- **Emerging truth:** one user experience can bridge selected operational domains without making the bridge authoritative.
- **Brand ambition:** dependable, permission-safe attention and action continuity across Wossol.
- **Unsupported territory:** always-on reliability, full visibility, intelligent prioritization, unified inbox, faster issue resolution, guaranteed alerts, or system-wide learning.

## 20. Weaknesses / Risks / Gaps

1. **Read-side authorization gap (high priority):** `list`, `unreadCount`, `markRead`, `click`, and `markAllRead` call `assertWorkspaceMembership`; they do not repeat the category/domain permission check or active Merchant membership check. If domain access is revoked while Workspace membership remains active, old rows remain available through these APIs. Click destination permissions still protect the destination, but the notification itself can reveal its generic type/title and target identifiers. This conflicts with active V1 architecture/UI statements that re-check relevant domain access. Resolve policy and enforce it on every read/mutation path or explicitly define safe revocation behavior.
2. **Poison-event starvation risk:** `projectPending` handles events in oldest-first order and has no per-event catch/dead-letter progression; one repeatable projection/permission/database error aborts the call before its receipt and before later events. The scheduler logs a run-level error and retries on its next poll. A persistently failing oldest event can therefore be selected again and hold later work behind it. No backlog/lag/poison-event metric or operator recovery control was found.
3. **Unbounded receipt exclusion query:** each projection loads all receipt IDs before selecting up to 100 pending events. The receipt table grows with processed event volume; bounded pending batch size does not bound the receipt scan. No production-scale performance test was run.
4. **Pagination tie boundary:** feed order uses `(createdAt desc, id desc)` but cursor filtering uses only `createdAt < cursor`. Rows sharing the final page timestamp can be skipped on the next page. Current UI tests assert URL/filter contract, not this data-boundary case.
5. **Broad API projection:** list returns raw Prisma rows; a dedicated merchant-safe DTO would reduce accidental exposure of source/merchant/workspace/target metadata.
6. **Bounded event/channel coverage:** no active Analytics or Local Pickup policy, no general alert/rule authoring, Email sender or Push sender. Event catalog is selected, not exhaustive.
7. **Trust/service outcomes unknown:** no live scheduler, database migration, runtime authorization, deliverability/latency, suppression, notification read/action rate, support resolution, or merchant outcome was verified.
8. **Quick panel error handling:** full page exposes load/action errors and retry; Quick panel promise chains were inspected but no equivalent explicit visible load/open error state was evident. Authenticated live interaction was unavailable; treat usability impact as a source-level concern requiring browser verification.
9. **Historical docs can overstate current scope:** the general Notifications System document is explicitly superseded. Its Insights, broad critical alerts, >5 batching, Email-active-V1, and order-detail click descriptions are not current Product truth.
10. **Read-batch freshness:** adding a later source event to an existing batch updates count/title/body/latest time but does not clear the row's existing `readAt`; new activity can remain excluded from unread count after the user read the earlier event.
11. **Canonical projection exception:** External Shipping valid receipt-proof requests still directly create one static Notification for a single selected Merchant user, without normal event membership, recipient fanout, policy permission check, category or target URL. This is a P1 source-level exception, not a claim of observed data leakage.

## 21. Future Strategic Potential

- **Current foundation:** durable source event projection, bounded policy, recipient rows, selected batching, read/click state and routes.
- **Approved direction:** active V1 architecture defines per-user scoped In-App signals; Push and richer preferences are deferred. Email remains gated on Settings preference activation.
- **Inferred opportunity:** permission-safe attention center with latency/failure observability and verified action outcomes; later connect signal → owner action → operational/economic result.
- **Not current:** recommendation engine, cross-domain prioritization, notification delivery optimization, escalation, learning, or unified communications.

## 22. Claim Safety

| Claim | Safety | Boundary |
|---|---|---|
| “Selected In-App operational updates route to their owner workflows.” | YELLOW | Current source/policies; verify deployment, active permissions and live route behavior. |
| “Order and selected Inventory notifications can be grouped.” | YELLOW | Only specific policy types and deterministic two-hour buckets; not universal or measured noise reduction. |
| “Each authorized user sees only notifications for domains they can currently access.” | RED / contradicted by current read path | Projection-time permission is present; API-time domain revalidation is not. |
| “Real-time/reliable/guaranteed alerts across Wossol.” | RED | Polling-based code, no SLO or runtime evidence; limited catalog/channels. |
| “Notifications improves decisions, predicts risk, learns, or closes the loop.” | BLUE / future only | No current interpretation/outcome loop. |
| “Email and Push notifications are available.” | RED | Preference gate only for Email, no active sender; Push deferred. |

## 23. Commercial Magnitude

**SUPPORTING / FOUNDATIONAL UX.** It can help users notice and resume selected operational work, but it neither executes that work nor establishes the reliability/outcome required for a high-leverage promise.

## 24. Strategic Classification

| Area | Classification |
|---|---|
| Workspace-scoped in-product event history/navigation | TABLE STAKES / useful foundation |
| Event-source membership and selected deterministic batching | POTENTIAL DIFFERENTIATOR at implementation-depth level; no market evidence |
| Ongoing authorization, durable monitored delivery, action/outcome intelligence | WHITESPACE / unresolved product and proof gap |
| Current competitive advantage or moat | INSUFFICIENT EVIDENCE |

## 25. Action Register

| Priority | Action | Rationale |
|---|---|---|
| MUST FIX / CLARIFY | Apply current category/domain authorization to list, count, read, click and mark-all operations, or define and document why already-created signals remain visible after access revocation. | Align implementation with active V1 permission contract and prevent stale access. |
| MUST FIX | Isolate projection failures by event, record bounded error/retry/dead-letter state, and ensure a poison event cannot starve later events. | Durable event processing should make recoverability and operator action explicit. |
| MUST FIX / VERIFY | Replace timestamp-only pagination with a stable compound cursor `(createdAt, id)` and add equal-timestamp boundary tests. | Avoid silent missing feed rows at page boundaries. |
| MUST FIX / CLARIFY | Define how new events added to a previously read batch affect unread state; reset unread, start a new batch, or document intentional behavior and test it. | Prevent new events from inheriting a stale “already read” state. |
| MUST MATCH | Route the External Shipping proof-request signal through canonical event/policy/per-user permission projection, or document and test its distinct recipient/category/target contract. | Current direct one-recipient Notification write diverges from accepted V1 architecture. |
| WORTH ADOPTING | Return an explicit merchant-safe DTO and validate notification target against allowed relative routes/types. | Reduce accidental data exposure and stale/unsafe navigation risk. |
| MUST VERIFY | Validate deployed migration/schema parity, scheduler operation across replicas, polling/lag/failure metrics, database-scale receipt performance and actual recipient isolation. | Source tests do not prove service operation. |
| POST-LAUNCH | Measure notification usefulness through permission-safe read/click and owner-action outcomes before tuning policies or claiming productivity/reliability. | Avoid equating stored signals with attention or resolution. |
| DO NOT COPY | Do not expand the feed to every worker, provider, ledger or analytics event without a noise/recipient/action contract. | Existing explicit exclusions protect merchant signal quality. |

## 26. Evidence Register

| Evidence ID | Evidence / finding | Type, location, observed behavior and caveat |
|---|---|---|
| EV-NOTIF-001 | Current authority and UI specification (V1.1 baseline) | P3. Product snapshot `44c16ede`. `MERCHANT_NOTIFICATIONS_V1.md`, `MERCHANT_NOTIFICATIONS_UI_SPEC.md`, and historical `Notifications System.md`; active authority supersedes stale historical scope and sets selected In-App signals/navigation, batching and future channel boundaries. |
| EV-NOTIF-002 | Event policy and projection (V1.1 baseline) | P1. Product snapshot `44c16ede`; `notifications.service.ts` (`policies`, aliases, `projectPending`, `projectForRecipient`). Supported types, recipient fanout, source-event membership, batch keys and unsupported-event receipts. Rechecked at current HEAD; no Notifications service delta. |
| EV-NOTIF-003 | Scheduler, API and module (V1.1 baseline) | P1. Product snapshot `44c16ede`; `notifications.scheduler.ts`, controller and module. Write-gated polling and authenticated Merchant API. Rechecked at current HEAD; no relevant delta. |
| EV-NOTIF-004 | Persistence and migration (V1.1 baseline) | P1. Product snapshot `44c16ede`; Prisma Notification/source/receipt/delivery-attempt models and `20260817_merchant_notifications_v1` migration. Schema parity and deployment were not verified then or now. |
| EV-NOTIF-005 | API-time authorization gap (V1.1 baseline) | P1. Product snapshot `44c16ede`; list/count/read/click/mark-all checked Workspace membership but did not re-check current category permission. Reconfirmed at current HEAD; exact code path remains. |
| EV-NOTIF-006 | Merchant surfaces (V1.1 baseline) | P1/P2. Full page, `notifications-data.ts`, `MerchantShell.tsx`, Home and focused source tests. Workspace feed, quick view, unread badge, filters and route navigation. Current changed Shell code only affects Order Capture badge, not Notifications. |
| EV-NOTIF-007 | Owner event contracts/producers (V1.1 baseline) | P1. Event registry/service and representative Orders/Tracking, Inventory, Support, Finance and External Shipping writers. The policy catalog is narrower than all owner events. |
| EV-NOTIF-008 | Direct External Shipping notice (V1.1 baseline) | P1. `external-shipping.service.ts` valid receipt-proof request path directly creates one static one-recipient Notification without source-event membership, normal policy fanout or target URL. Reconfirmed at current Product HEAD. |
| EV-NOTIF-009 | Email preference without delivery (V1.1 baseline) | P1. Merchant Settings preference/UI/API plus `isEmailDeliveryEnabled` future-sender gate; no active sender or delivery-attempt writer found in Notifications flow. |
| EV-NOTIF-010 | Focused baseline tests | P2. Product snapshot `44c16ede`: backend service tests 11/11 and Merchant notification workflow source tests 5/5; reviewed gap cases included permission revocation, read-batch updates and timestamp-tie pagination. Re-run at current HEAD with same pass counts; backend/frontend typechecks also pass. |
| EV-NOTIF-011 | Current source state | P1. Product `2535c07e65b7d6fe047833b881fa2050d474b865`; `dev/wossol-integration`; local branch tracks same-named origin ref; one unrelated untracked verification script. Intelligence synced to `f4cab50`. |
| EV-NOTIF-012 | Policy catalog and aliases | P1. `apps/backend/src/modules/notifications/notifications.service.ts` (`policies`, `policyAliases`). Selected Order/Confirmation, Inventory, Support, Finance withdrawal, External Shipping events; generic title/body and owner target; Analytics/Local Pickup absent from active map. |
| EV-NOTIF-013 | Projection authorization | P1/P2. Same service `projectPending`; active Merchant-user query, active/non-revoked Workspace membership and `permissions.can` using category permission before projecting new row. Tests cover both grant and deny for representative domains. |
| EV-NOTIF-014 | API-time authorization gap | P1. Same service `assertWorkspaceMembership`, called from `list`, `unreadCount`, `markRead`, `click`, `markAllRead`; helper checks Workspace membership only. Contradicts V1 intent's ongoing domain-access check; destination API remains independently authorized. |
| EV-NOTIF-015 | Async scheduler | P1. `notifications.scheduler.ts` and module/app registration. Write-mode-gated timer, default 60 seconds/minimum 30 seconds, logs run failure; no provider channel sender or per-event error/dead-letter handling observed in inspected path. |
| EV-NOTIF-016 | Idempotency/batching | P1/P2. `notifications.service.ts`, `NotificationSourceEvent` and batch unique index in schema/migration; two-hour UTC bucket separated by recipient/Merchant/Workspace/type/Store; 11 backend tests cover policy, batching, scope and key conflict behavior. Does not prove distributed production behavior. |
| EV-NOTIF-017 | Feed persistence and API | P1. `apps/backend/prisma/schema.prisma` models `Notification`, `NotificationSourceEvent`, `NotificationEventReceipt`, `NotificationDeliveryAttempt`; V1 SQL migration. API list returns rows newest first, take 51 to return 50; cursor filters timestamp only despite ID tiebreak sort. Migration application status not verified. |
| EV-NOTIF-018 | Full Merchant page and data client | P1/P2. `apps/frontend/src/app/merchant/notifications/{page.tsx,../notifications-data.ts}`; filters, retryable states, load more, mark-all and server-target click; five source/UI contract tests pass. No authenticated browser acceptance in this audit. |
| EV-NOTIF-019 | Quick view and unread badge | P1. `apps/frontend/src/app/merchant/MerchantShell.tsx`; up to 15 items, active Workspace scope, periodic 60-second unread-count refresh, event-based local refresh, close/navigation behavior. Does not prove real-time delivery. |
| EV-NOTIF-020 | Notification producers | P1. Representative Order/Confirmation, Inventory reservation/cost, Support, Finance withdrawal and External Shipping event writers under `apps/backend/src/modules/`; policy tests validate current consumer mapping. Not every producer transaction was exhaustively tested here. |
| EV-NOTIF-021 | Current contract and stale intent | P2/P3. `docs/ui/merchant/MERCHANT_NOTIFICATIONS_UI_SPEC.md`, `docs/wossol-system-design/01-system-design/core-systems/MERCHANT_NOTIFICATIONS_V1.md`; active V1 is In-App/navigation-only with selected batching, Analytics/Local Pickup future. `Notifications System.md` explicitly defers authority to the newer V1 document and calls itself historical. |
| EV-NOTIF-022 | Cross-domain reviewer boundary | P2. `04-review-history/SUPPORT_INTERNAL_CHAT_REVIEW_2026-09-26.md` says Support/Chat events may feed attention routing but do not prove delivery or coherent attention; review directs Notifications audit next. Home V1.2 records inherited permission/data-projection concerns. |
| EV-NOTIF-023 | Verification | P2. Product backend focused Notifications service suite 11/11; frontend Notifications source/UI suite 5/5; backend and frontend `pnpm typecheck` passed. No DB integration, Prisma migration status, browser/deployed or production test. |

## 27. Contradictions & Uncertainty

1. **CONTRADICTION-NOTIF-001 — Permission contract vs API behavior.** **Source A (P2/P3):** active Merchant Notifications V1 architecture and UI spec state that Merchant, Workspace and relevant domain permissions are checked for feed/API access. **Source B (P1):** the projector checks active Merchant user, Workspace membership and domain permission, while current list/count/read/click/mark-all service methods check only active Workspace membership. **Nature:** permission is projection-time only, not enforced on retrieval/action against existing rows. **Evidence strength:** high for inspected service source and tests; docs state intended contract. **Working conclusion:** current UI/API authorization claim is not established; existing notification rows can outlive domain grant. **Remaining uncertainty:** deployed code may differ; no production access. **Required verification:** add tests for permission revocation and enforce chosen contract across every endpoint.
2. **CONTRADICTION-NOTIF-002 — General Notifications System vs active V1.** Historical `Notifications System.md` describes Insights, broader critical alerts, Email-as-V1 and order-detail routing. Its own header states that `MERCHANT_NOTIFICATIONS_V1.md` governs current behavior and marks those older concepts superseded. **Working conclusion:** follow P1 and active V1; historical feature statements are not present truth.
3. **UNCERTAINTY-NOTIF-003 — Delivery reliability and freshness.** Source shows polling, but no live deployment, queue/backlog telemetry, production DB, actual event-to-row timing, service uptime, recipient read/action behavior or notification outcome measurements. **Working conclusion:** delivery reliability and merchant benefit magnitude are NOT VERIFIED.
4. **UNCERTAINTY-NOTIF-004 — Migration/schema deployment.** Model and migration are present in repository; database application/parity not verified. No Prisma/database integration test was run.
5. **UNCERTAINTY-NOTIF-005 — Cross-replica processing / scale.** Scheduler uses process-local running state, and code reads all receipt IDs before each bounded event query. Production replica count, concurrency behavior and scale are unknown.
6. **UNCERTAINTY-NOTIF-006 — Source event errors.** Receipt has an `errorMessage` field, but inspected projection path only creates a receipt after processing; no bounded retry count/dead-letter or operator recovery was found. Runtime consequences are source-derived risk, not observed production incident.
7. **UNCERTAINTY-NOTIF-007 — Feed cursor boundary.** Equal-timestamp page-boundary loss is implied by timestamp-only cursor predicate and `(createdAt,id)` ordering; not covered by focused tests or reproduced against PostgreSQL in this audit.

## 28. Open Questions

1. Should permission revocation immediately hide old signals, or is projection-time authorization an intentional retained-history rule? Align policy, docs and tests.
2. How are poison events surfaced, retried, dead-lettered and replayed without starving later event projection?
3. Is the Notifications migration deployed and Prisma/database state aligned in the actual environment?
4. How many scheduler instances run, and what production controls/metrics establish event lag and recovery?
5. Do merchant teams expect current per-recipient domain permission to apply to list/count/read/click, including Home's notification projection?
6. Which older categories/types are actually present in deployed data and should remain visible under “Other update”?
7. What event-to-owner-action outcomes should be measured before claiming that notifications reduce missed work or improve resolution?

## 29. Methodology Learnings

No general methodology change required. Section-specific reminder: audit both **projection-time eligibility** and **later retrieval-time authorization**; recipient filtering at creation does not prove continuing access safety. Also treat a durable event consumer's receipt/poison-event policy as part of operational recovery, not merely infrastructure detail.

## 30. Retroactive Review Impact

`RR-V12-020` is updated by this V1.2 audit; it remains subject to the current Director Quality Gate and is not marked Director-accepted. No methodology change or additional retroactive queue item is justified. The Support/Internal Chat review's explicit next-step request is satisfied. Home's inherited Notification authorization/projection concern is corroborated by direct current Product inspection; the Home document need not be edited absent a separate correction request.

## 31. Self-Critique

- The strongest positive conclusion is intentionally modest: a code-backed, selected event-to-feed-to-owner-route continuity layer. Focused source tests/typechecks support implementation contracts, not deployment.
- Competitive significance is **insufficiently evidenced**; no competitor silence has been treated as absence.
- Merchant-work reduction is qualitative and limited to discovery/navigation; no time, revenue, fewer missed tasks, service quality, or resolution improvement is claimed.
- Future Email/Push, Analytics insight and Local Pickup behavior remain outside current truth.
- Authorization and poison-event risks derive from direct P1 control flow; deployed impact is unknown.
- The Product repository's unrelated untracked file was preserved; no application files were edited.

## 32. Canonical Section Takeaway

Notifications is a bounded In-App signal and navigation layer that connects selected durable operational events to recipient-specific Workspace feeds and owner workflows. Its strongest V1.2 value is attention/context continuity with selected noise control—not action, intelligence or guaranteed communication. To make the trust promise defensible, close the API-time permission gap, prevent poison events from starving the projection, fix stable feed pagination, and verify actual deployment and delivery before making reliability or productivity claims.
