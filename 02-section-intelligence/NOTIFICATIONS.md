# Notifications — Section Intelligence

## 1. Audit Metadata

- **Audit date:** 2026-09-26.
- **Methodology:** `00-methodology/MASTER_INSTRUCTIONS.md` v1.1; `CODEX_OPERATING_PROTOCOL.md` v1.0.
- **Competitive reference:** `01-competitive/WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md` v1.
- **Intelligence source:** `jetshop7/wossol-brand-intelligence`, `main`, synchronized to `83453ddcbe9b289f741130200a81ff8c7c63bdce` before interpretation; clean and equal to `origin/main` immediately after synchronization.
- **Product source:** `jetshop7/wossol-platform`, `dev/wossol-integration`, committed HEAD `44c16ede5be6ebf26c35acd429d1183979fe36b5`, equal to configured upstream at inspection. Product worktree was dirty in six unrelated Shopify/execution-history paths: `apps/frontend/public/shopify-app-home.js`, `apps/frontend/src/app/shopify/app/route.ts`, `apps/frontend/src/app/shopify/embedded-cod-management.spec.ts`, `apps/frontend/src/app/shopify/shopify-offers-upsells-acceptance.spec.ts`, `docs/PROJECT_EXECUTION_CONTROL.md`, and `docs/engineering/ENGINEERING_CHANGE_LOG.md`. Those edits were preserved; none of the inspected Notifications paths was among the changed paths. No Product files were modified.
- **Evidence limitation:** source, contracts and focused tests establish repository behavior, not deployed scheduler health, production volume, end-user receipt, or business outcomes.

## 2. Audit Coverage Map

| Surface | Coverage | Evidence |
|---|---|---|
| Current architecture and approved V1 contract | Read current authority and Merchant UI spec; separated historical design text | EV-NOTIF-001 |
| Backend event projection and policy catalog | Mapping, recipients, permission gates, idempotency, batching, receipts | EV-NOTIF-002–003 |
| Persistence and schema | Notification/source-event/receipt/delivery-attempt/preference models and migration | EV-NOTIF-004 |
| API, authentication and read state | List/count/read/click/mark-all and workspace/user scoping | EV-NOTIF-005 |
| Merchant page, quick view and Home integration | Feed, filters, pagination, popover, badges, navigation, retry UX | EV-NOTIF-006 |
| Event producers and owner domains | Orders/Tracking, Inventory, Support, Finance, External Shipping; event-registry and direct-write exception | EV-NOTIF-007–008 |
| Settings and channels | Email preference UI/API/gate versus actual sender/channel implementation | EV-NOTIF-009 |
| Tests | Backend service tests 11/11; Merchant workflow source tests 5/5 | EV-NOTIF-010 |
| Competition and deployed operations | Competitive master search; no live accounts, deployed scheduler, mailer or production delivery evidence | PARTIALLY INSPECTED / NOT VERIFIED |

## 3. Executive Section Truth

Notifications is a functioning, merchant-facing **In-App event-awareness feed**, not an alert-analysis, communications, email, or decision-intelligence platform. A periodic backend consumer projects selected durable Domain Events into per-user rows after merchant, active Workspace membership and relevant domain permission checks. The Merchant shell provides unread count/quick view; the page provides filters, read state, click-through, and load-more. In-App is the only evidenced delivery channel.

The strongest current design is the separation between owner-domain events and a bounded notification projection: notifications link back to Orders, Inventory, Support, Finance, or External Shipping rather than owning or mutating those records. Selected Order lifecycle and Inventory condition notifications are grouped per recipient into deterministic two-hour UTC buckets. Source membership and unique keys provide useful replay/concurrency protection.

Important implementation limits qualify the coherent V1 story. Domain permission is checked when projecting but not rechecked by list/count/read/click APIs after a notification is persisted; adding events to an already-read batch does not reset its read state; and timestamp-only pagination can skip rows sharing a timestamp. One External Shipping flow still directly creates a one-recipient, uncategorized/static Notification outside the projection policy. Email preference controls exist, but no sender/delivery worker was found. No outcome, response-time, or notification-effectiveness evidence was found.

## 4. Scope & Architecture Map

The intended graph is **owner-domain state change → durable Domain Event → Notifications policy → authorized recipient's In-App row → navigation to owner domain**. Orders/Tracking, Inventory, Support, Finance and External Shipping retain authority for their own state. `EventsService` validates producer/event contracts; `NotificationsService.projectPending` polls unreceipted Domain Events and applies a closed policy catalog. Merchant read state is attached to the individual user's Notification row, not shared across Workspace users.

The Notification table also has channel/status and delivery-attempt schema, and a user-level Email preference exists. These structures do not establish a functioning email channel: inspected code has no delivery sender/attempt writer and describes the email gate as a future sender prerequisite. The active V1 contract makes In-App current, Email deferred pending Settings activation, Push deferred, Analytics signals future-only, and Local Pickup future-only. The current Settings screen can already toggle Email preference, so the setting must not be confused with channel delivery.

## 5. Current Capability Inventory

| Capability | Status | Current behavior / limit |
|---|---|---|
| In-App feed and quick notifications | LIVE (source-backed) | Per-user active-Workspace feed, quick view, unread count, category/read filters, read-all, load more and navigation. |
| Event-to-notification policy | LIVE, bounded | Explicit policies for selected Order lifecycle, Inventory, Support replies/waiting/resolution, Finance withdrawal review/paid and External Shipping lifecycle events. Unknown/unsupported events are receipted without a Merchant row. |
| Per-recipient authorization | LIVE at projection; PARTIAL at read time | Active Merchant users, active Workspace membership and domain permission are checked when a source event is projected. API reads later check Workspace membership and recipient identity, but not current per-category permission. |
| Order/Inventory batching | LIVE | Matching recipient/Merchant/Workspace/type/Store events update a deterministic two-hour UTC batch; each source event has unique membership. |
| Support/Finance/External Shipping granularity | LIVE | Individual rows rather than batch groups, with owner-screen navigation. |
| Read/click state | LIVE | Per-user `readAt`/`clickedAt`; click marks read and returns stored target URL. New source events added to an existing read batch leave it read. |
| Durable consumer receipt | LIVE, bounded | Successful/unsupported processing receives a unique event receipt; failures before receipt can retry on a later poll. Permission-ineligible events are consumed at current projection time and are not backfilled when permission later changes. |
| Email | NOT IMPLEMENTED as delivery | Preference, UI/API, and a gate helper exist; no sender/attempt consumer found. Existing preference defaults to enabled, but that does not mean email is sent. |
| Push, user-defined rules, analytics insights | DEFERRED / NOT FOUND | Active V1 excludes these; no notification rule builder or decision loop found. |
| Direct External Shipping request notice | PARTIAL / legacy seam | One direct create writes a one-recipient, default-category row without target URL/source-event membership; this bypasses normal policy and recipient projection. |

## 6. Workflow & Lifecycle

1. An owner-domain transaction emits a versioned Domain Event through the registry-backed `EventsService` (or, in one External Shipping request path, directly writes a Notification).
2. The Notifications scheduler starts a recurring background cycle when write mode accepts writes. It waits at least 30 seconds (default 60 seconds, configurable), reads up to 100 unreceipted events oldest first, and projects supported events.
3. For each event, policy determines category, required permission, title, batch behavior, and internal target. Active Merchant users are filtered by Workspace membership and permission; recipient-specific row creation/source membership occurs transactionally. Unique constraints and retry handling cover duplicate event membership and batch creation races.
4. In the UI, unread count and recent rows are fetched for the active Workspace. Selecting a row calls click/read, then navigates to the returned target. Mark-all and per-user state are workspace-scoped. Switching Workspace refreshes context; Store selection does not define the notification feed.
5. Polling errors are logged and the recurring scheduler retries later. A successfully receipted event is not reconsidered by this consumer. External email, push, SMS/WhatsApp, or in-app realtime delivery is not established; the UI refresh mechanisms and scheduler are polling-based.

## 7. Value Recipient Map

| Recipient | Current value | Limit |
|---|---|---|
| Merchant operator | A consolidated place to notice selected owner-domain changes and navigate to their authoritative screens. | No measured reduction in missed events, response time, loss, or workload. |
| Merchant owner | Individual read state and Workspace-aware context, with domain permissions used at projection. | Current permission revocation is not re-evaluated by notification read APIs. |
| Operations / domain teams | A common downstream event projection without transferring domain ownership. | No verified notification delivery SLA, staffing loop, or cross-domain escalation workflow. |
| Wossol product/analytics | Durable event and source-membership structure could support later measurement. | No notification outcome/read-effectiveness analytics or recommendation loop found. |
| Customer | No direct customer-facing notification channel established by this Merchant feature. | Do not conflate merchant notification rows with customer communication or Support delivery. |

## 8. Control & Merchant Agency

The Merchant can filter by read state/category, mark rows read individually by opening or mark all read for the Workspace, and choose whether to follow a safe owner-screen link. They do not define rules, priorities, thresholds, channels, notification content, or alert actions in the active V1 contract. The Email toggle controls a persisted preference but has no evidenced email sender behind it. Notification click-through is navigation, not a direct business command.

## 9. Transparency & Trust

Trust-supporting evidence includes versioned event contracts, event provenance/context fields, recipient and Workspace scoping, domain permission checks at projection, user-isolated read state, source-event uniqueness, transaction-bound batch membership, bounded supported policies, merchant-safe content and navigation-only actions. Unknown events are not surfaced as arbitrary Merchant text.

Limits: API access does not re-check domain permission after projection; user membership alone gates later feed access. The UI receives target URLs from persisted rows; route-level authorization remains necessary. Projection cadence is polling rather than immediate push. Consumer receipts are global per Domain Event and processed events are not reconsidered after access changes. The legacy direct External Shipping row differs from the per-user policy contract. No production scheduler/DB health, notification latency distribution, email delivery, provider receipt, or support outcome was verified.

## 10. Merchant Value Extraction

The defensible value is operational awareness plus contextual navigation: selected status changes need not be found only by reopening each owner screen. A read marker distinguishes new from already reviewed items for that user. Deterministic batching can reduce repeated Order/Inventory rows while preserving source membership and a time/Store-filtered owner-screen route.

This is not evidence that no event is missed, every operationally important event is covered, or an alert leads to corrective action. Batching can itself suppress unread attention when a read row is updated by later events, and older rows can be lost from pagination at timestamp ties. The benefit should therefore be described as a current, bounded In-App convenience rather than reliable proactive risk management.

## 11. Feature Clusters

1. **Owner event → scoped awareness:** durable Domain Event + policy + Merchant/Workspace/permission filter + per-user projection.
2. **Volume → bounded grouping:** two-hour deterministic grouping + unique source-event links + exact status/date/Store navigation for Orders; inventory batches route to inventory filters.
3. **Awareness → human action:** per-user read/click signal + navigation to the owner domain, leaving the underlying business decision to the appropriate workflow.
4. **One shell, multiple domain owners:** Home recent updates, quick notifications and the full Notifications page consume the same feed, but do not replace Orders, Inventory, Support or Finance records.

## 12. Merchant Journey / Old Way vs Wossol Way

| Stage | Plausible alternative (not universal or measured) | Current Wossol evidence |
|---|---|---|
| Notice a change | Revisit different domain screens or receive separate operational messages | Selected events can appear in one Merchant In-App feed. |
| Decide whether new | Rely on memory or manually record review | Per-user `readAt` and unread count distinguish unread rows. |
| Handle event | Search for the affected object | Policy-generated deep link routes to an owner screen/filter. |
| Manage volume | Read repeated one-event rows | Selected Order and Inventory events group within a fixed two-hour bucket. |

No merchant study, baseline, delivery measurement, or quantified time saving supports a stronger Old Way comparison.

## 13. Hidden / Non-Obvious Advantages

- Notification source-event membership persists separately from the batch row, preserving which source events contributed and preventing duplicate membership.
- Domain authority remains downstream: Notifications points to owner systems instead of duplicating workflow actions.
- Per-user grouping prevents one teammate's read state from being shared with another.
- Unsupported/noise events are deliberately excluded from the current user-facing policy catalog.
- Active Workspace filtering is distinct from Store context, helping retain Workspace-level signals while an operator changes Store view.

These are architectural/operational proof points, not evidence of a strategic moat or service outcome.

## 14. Data & Intelligence Assets

Persisted evidence includes Notification type/category/title/body/target, recipient, Workspace/Merchant/Store references, source-event identity, batch count/window/key, latest event time, metadata, created/read/click timestamps, event receipt and source-event membership. The schema includes delivery-attempt/channel fields, but no inspected code writes attempts. These records could support latency, reach, batching, engagement, and event-to-action analysis if consumer/permission rules and definitions were governed. No such aggregate or decision loop was found. Read/click is interaction telemetry, not proof a human understood, acted, or improved an outcome.

## 15. Cross-Section Compound Advantages

- Orders/Tracking events can link attention to authoritative Order states; Inventory transitions route to product-stock screens; Finance withdrawal events route to finance; Support events route to the relevant Ticket; External Shipping events route to shipment detail.
- Home and Merchant shell offer fast entry while the full page provides history and filtering.
- The Support / Internal Chat review correctly distinguishes event publication from delivered notification. Notification event creation does not establish merchant attention without this projection path and healthy polling.
- Events emitted by other domains do not constitute unified analytics: each Notification is a bounded deterministic policy mapping.

## 16. Competitive Analysis

The competitive master is a category-level baseline and contains no notification-system comparison with direct competitors. No direct competitor feature was verified for this audit. Therefore competitor capability, parity/superiority, uniqueness, and whitespace remain **INSUFFICIENT EVIDENCE**. A per-user In-App feed and operational event notices are likely expected platform infrastructure, but even that comparative claim requires verification. Do not claim a Wossol notification differentiator from this record.

## 17. Marketing Intelligence

**Supporting proof only:** “Selected Order, Inventory, Support, Finance and External Shipping updates can appear in a Merchant In-App feed and link back to their owner workflows.” Proof: active source-backed catalog, per-user feed/read APIs, Merchant UI and focused tests. Claim strength is bounded to listed events and In-App behavior; production delivery and complete coverage are not verified.

Potential message territory: “Keep key operating updates in view, then continue in the workflow that owns them.” Do not claim real-time, proactive, always-delivered, omnichannel, smart/AI prioritization, complete visibility, guaranteed alerting, measurable loss prevention, or effective email notifications.

## 18. Surprise Findings

- A historical “Final V1” design document itself warns that it is superseded; the concise current V1 authority explicitly narrows the active contract to selected In-App signals. This prevents stale design promises (Insights, five-event batching, Email-active V1, Order-detail action) from being mistaken for product truth.
- The `NotificationDeliveryAttempt` model exists but no sender/attempt writer was found; schema readiness is not channel operation.
- A direct External Shipping notification write bypasses the otherwise event-driven, per-user projection design.

## 19. Potential Category Reframes

Potential reframe, not a positioning conclusion: **awareness that returns the operator to the system that owns the next decision**, rather than a second system of record. Current evidence supports navigation and selected event scope, not unified operational control, complete attention management, or measured decision quality.

## 20. Brand Evidence

Current evidence modestly supports **Clarity, Control, and Accountability**: a bounded policy catalog, recipient-specific read state, explicit event provenance, owner-domain routing and no arbitrary notification action. Weaknesses in permission revalidation, unread batch updates, pagination and delivery proof prevent elevating “never miss what matters,” “reliable alerting,” “intelligent operations,” or a broad Trust promise. No final brand decision is made.

## 21. Weaknesses / Risks / Gaps

1. **Permission revocation gap (high):** `list`, `unreadCount`, `markRead`, `click`, and `markAllRead` verify active Workspace membership and recipient identity/scope, but do not re-check each row's category permission. A user who retains Workspace membership after losing Finance/Support/etc. permission can continue to read persisted rows and receive old target URLs. This conflicts with the current authority's stated API-time domain-access check.
2. **Read batch attention gap:** when a new event joins an existing batch, `batchCount`, title/body and latest time update, but `readAt` is retained. If the user already read the row, later events in the bucket stay marked read and do not raise unread count.
3. **Pagination tie gap:** rows sort by `(createdAt DESC, id DESC)`, while `nextCursor` and the next query use only `createdAt < cursor`. More than one page sharing a timestamp can omit equal-timestamp rows after the boundary.
4. **Direct-write exception:** External Shipping valid-receipt-proof request picks one active Merchant user and creates a default-category Notification directly, without event-source membership/normal permission fanout/target URL. It surfaces as a static row and will not reach other eligible users through this path.
5. **Email product-contract mismatch:** Settings exposes an Email Notifications On/Off control and persists a preference; active authority says Email waits until Settings activates that preference. Yet no sender exists and the preference defaults enabled; only an unused helper gate was found. Do not interpret the toggle as delivery capability.
6. **Event coverage and delayed attention:** explicit catalog is narrow and policy excludes many events by design. Polling is at least 30 seconds/default 60 seconds, not real-time. Polling interval, backlog, worker health, delivery latency and user outcomes were not verified.
7. **Global receipt semantics:** an event is receipted after processing even if no recipient currently passes membership/permission; later access does not backfill it. Whether recipients should be selected at event-time or eventual projection-time is an unresolved contract decision.
8. **Competitor evidence absent:** no feature-level direct competitor verification.
9. **Scale not load-tested:** the poller reads all existing receipt IDs to exclude already-processed events on every cycle; long-run receipt growth and query/backlog behavior were not tested. This is a potential scaling risk, not a demonstrated production failure.

## 22. Future Strategic Potential

- **Current foundation:** source-event and per-user read/click data, scoped policies, batching and owner links.
- **Approved future direction:** Push deferred; Analytics may later publish actionable anomaly events; Local Pickup future-contract-only; Email awaits channel implementation/preferences.
- **Inferred potential:** reliability/latency observability, access-aware historical visibility, digest preference, priority and event-to-owner-outcome loops.
- **Guardrail:** future delivery channels, risk ranking or learning require implementation, governance, user control and measured operation before becoming current claims.

## 23. Claim Safety

| Claim | Safety |
|---|---|
| “A Merchant In-App feed for selected cross-domain operating updates” | Safe, qualify selected supported event types. |
| “Personal read state and quick navigation to supported owner screens” | Safe in the inspected implementation; note current authorization gap if describing access security. |
| “Smart batching” | Qualify to deterministic two-hour grouping of selected Orders/Inventory events; do not imply relevance learning or suppression optimization. |
| “Email notifications” | Do not claim delivered email; preference exists, sender not found. |
| “Never miss an important event,” real-time, proactive intelligence, analytics insights, push/omnichannel, competitor superiority | DO NOT CLAIM. |

## 24. Commercial Magnitude

- In-App feed and owner navigation: **FOUNDATIONAL** workflow convenience.
- Permission-aware projection and per-user read isolation: **FOUNDATIONAL** trust/security properties; the current read-time gap requires resolution.
- Batching, event provenance and Home/quick-view surfaces: **SUPPORTING** operating proof.
- Read/click/batch data as future analytics: **FUTURE POTENTIAL**, not current decision intelligence.

## 25. Strategic Classification

| Asset | Classification | Basis |
|---|---|---|
| Selected In-App event feed | TABLE STAKES / foundational | Useful cross-domain awareness; no verified category uniqueness. |
| Scoped event projection and owner-system links | POTENTIAL DIFFERENTIATOR as system design | A coherent ownership pattern, but no competitor or outcome evidence and current permission/read gaps. |
| Deterministic Order/Inventory batching | PARITY / implementation quality | Practical noise control for a narrow catalog, not intelligent prioritization. |
| Email preference/data model | WHITESPACE / incomplete channel | Preference/UI exists; no sender found. |
| Notification interaction data | POTENTIAL FUTURE ASSET | Stored but not analyzed into decisions or outcomes. |

## 26. Action Register

| Priority | Action | Reason |
|---|---|---|
| MUST FIX / MUST MATCH | Re-check each row's current domain permission for list/count/read/click/mark-all, or safely remove inaccessible rows and avoid returning their targets. | Enforce current V1 API authorization contract after permission revocation. |
| MUST FIX | Define whether new source events added to a read batch should reset unread state; implement and test the selected attention contract. | Avoid silently burying later events in a previously read row. |
| MUST FIX | Use a compound pagination cursor (`createdAt`, `id`) matching the sort tuple. | Avoid omissions at timestamp ties. |
| MUST MATCH | Route External Shipping proof-request signal through the canonical event/policy/per-user permission path, or explicitly document the distinct contract. | Current one-recipient direct row diverges from current V1. |
| MUST MATCH | Align Settings, V1 docs and runtime for Email; until sender exists, describe it as a saved preference only or defer/disable the control. | Avoid implying a nonfunctional channel. |
| POST-LAUNCH | Instrument consumer lag/failure, eligible/skipped recipients and event-to-read/click/outcome measures. | Establish actual service quality before reliability/intelligence claims. |
| DO NOT COPY | Do not turn event storage or click/read telemetry alone into “AI” or decision intelligence. | No consuming analytical/action loop exists. |

## 27. Evidence Register

**EV-NOTIF-001 — Current authority and UI specification.** **Type:** P3. **Repository:** Product, `44c16ede5be6ebf26c35acd429d1183979fe36b5`. **Paths:** `docs/wossol-system-design/01-system-design/core-systems/MERCHANT_NOTIFICATIONS_V1.md`; `docs/ui/merchant/MERCHANT_NOTIFICATIONS_UI_SPEC.md`; `docs/wossol-system-design/01-system-design/core-systems/Notifications System.md`. **Observed:** current authority supersedes historical design and describes selected per-user In-App signals, two-hour batching, owner navigation, Email preference gating and future boundaries. **Confidence:** High for intended contract; P1 code takes precedence for actual truth.

**EV-NOTIF-002 — Event policy and projection.** **Type:** P1. **Path:** `apps/backend/src/modules/notifications/notifications.service.ts`; symbols `policies`, `policyAliases`, `projectPending`, `projectForRecipient`. **Observed:** policy types/permissions/targets, eligible recipient enumeration, time-bucket key, transaction-bound rows/source membership, unknown-event receipts. **Confidence:** High.

**EV-NOTIF-003 — Scheduler, API and module.** **Type:** P1. **Paths:** `apps/backend/src/modules/notifications/notifications.scheduler.ts`; `notifications.controller.ts`; `notifications.module.ts`. **Observed:** minimum 30-second/default 60-second recurring background poll, write-mode gate/error log; authenticated merchant API endpoints. **Confidence:** High.

**EV-NOTIF-004 — Persistence.** **Type:** P1. **Paths:** `apps/backend/prisma/schema.prisma` (`Notification`, `NotificationSourceEvent`, `NotificationEventReceipt`, `NotificationDeliveryAttempt`, `UserNotificationPreference`); `apps/backend/prisma/migrations/20260817_merchant_notifications_v1/migration.sql`. **Observed:** unique source membership/batch/receipt constraints, read/click fields, unused delivery-attempt schema in inspected code. **Confidence:** High.

**EV-NOTIF-005 — Authorization gap.** **Type:** P1. **Path:** `apps/backend/src/modules/notifications/notifications.service.ts`; symbols `list`, `unreadCount`, `markRead`, `click`, `markAllRead`, `assertWorkspaceMembership`. **Observed:** list/read operations scope by `recipientUserId` and Workspace membership but do not resolve current notification category permission. **Confidence:** High; exact security impact depends on deployment data and permission-revocation policy.

**EV-NOTIF-006 — Merchant surfaces.** **Type:** P1/P2. **Paths:** `apps/frontend/src/app/merchant/notifications/page.tsx`; `notifications-data.ts`; `MerchantShell.tsx`; `merchant/page.tsx`; `merchant/notifications/merchant-notifications-workflow.spec.ts`. **Observed:** category/read filters, quick feed, Home updates, workspace unread badge, URL navigation, error/retry handling, timestamp cursor. **Confidence:** High source; no live browser session test.

**EV-NOTIF-007 — Owner event contracts.** **Type:** P1. **Paths:** `apps/backend/src/modules/events/events.service.ts`; `domain-event-contract-registry.ts`; selected producer paths in Orders/Tracking, Inventory, Support, Finance and External Shipping. **Observed:** contract validation and selected owner event emission; not every owner event maps into notification policy. **Confidence:** High for inspected paths, not an exhaustive guarantee of every deployed event.

**EV-NOTIF-008 — Direct External Shipping write.** **Type:** P1. **Path:** `apps/backend/src/modules/external-shipping/external-shipping.service.ts`, valid receipt-proof request flow near `tx.notification.create`. **Observed:** selects one active Merchant user and writes a Notification directly with no target URL/source-event membership. **Confidence:** High.

**EV-NOTIF-009 — Email preference without delivery.** **Type:** P1. **Paths:** `apps/backend/src/modules/merchant-settings/merchant-settings.service.ts`; `apps/frontend/src/app/merchant/settings/page.tsx`; Notifications service `isEmailDeliveryEnabled`. **Observed:** preference read/update and UI toggle; Notifications helper comment states a future sender must call the gate. Repository search found no email sender or delivery-attempt writer. **Confidence:** High within searched backend notification/settings paths; actual external deployment wiring not verified.

**EV-NOTIF-010 — Focused tests.** **Type:** P2. **Paths:** Notifications backend service spec and Merchant notifications workflow spec. **Observed:** 11 backend tests passed; 5 frontend workflow source tests passed. They cover filtering, recipient projection, batching, click/read isolation, event mapping, permissions at projection and UI contracts. No test found for permission revocation after projection, adding an event to an already-read batch, or timestamp-tie pagination. **Confidence:** High for the commands/results recorded.

## 28. Contradictions & Uncertainty

1. **Read-time permission contract mismatch:** current V1 doc says API access re-checks domain access; implementation only checks Workspace membership for API reads. The P1 implementation qualifies the intended security claim and requires review.
2. **Email authority/runtime mismatch:** current authority says Email waits until Settings preference activation and Settings exposes a toggle, but no sender exists. Interpret the toggle as preference storage, not operational delivery.
3. **Canonical projection exception:** one External Shipping path directly creates a static one-user Notification rather than publishing into normal policy projection.
4. **Attention semantics:** active authority specifies durable batching and read state but does not specify how a later event changes a previously read batch; current behavior retains read state. User expectation/product intent unclear.
5. **Pagination behavior:** code establishes timestamp tie risk; real frequency of identical timestamps and impact at page boundaries are not measured.
6. **Recipient timing:** event receipt is global even when no currently eligible recipient is found; intended semantics for later membership/permission changes are unclear.
7. **Reliability/competition:** production scheduler uptime, lag, worker multiplicity/configuration, actual row delivery to users and direct competitor depth remain not verified.

## 29. Open Questions

1. Should permission revocation immediately remove/hide existing notifications in that domain? Current active specification implies yes.
2. Should new events added to a read batch reset it to unread, create a new row, or remain read? Which contract best preserves attention without noise?
3. Should the External Shipping receipt-proof request be visible to every eligible Merchant user or intentionally only one? Which category and target should it use?
4. Is Email preference meant to activate an imminent sender, or should the user-facing toggle be removed/disabled until delivery exists?
5. Are there explicit event-recipient guarantees, delivery-latency objectives or notification-retention rules outside inspected code?
6. What competitive workflow evidence would establish this as more than standard SaaS feed infrastructure?

## 30. Methodology Learnings

No reusable methodology change identified. This audit reinforces a section-specific check: for persisted user-facing signals, distinguish **permission at creation** from **permission at read**, and inspect how aggregation interacts with read state and pagination—not just the producer, event, or notification row.

## 31. Retroactive Review Impact

No prior section requires retroactive re-audit. The latest Support / Internal Chat review explicitly named Notifications next and correctly cautioned that event creation does not prove delivery. This audit links that distinction to a real but bounded In-App projection path; it does not retroactively alter Support conclusions. No queue entry is warranted because no methodology change is made.

## 32. Canonical Section Takeaway

Wossol has a genuine, scoped Merchant In-App feed that turns selected owner-domain events into recipient-specific, navigable signals, with durable event membership and deterministic batching for selected Order/Inventory volume. Its defensible role today is basic operational awareness—not complete, real-time or omnichannel alerting, analytics, or intelligence. Before stronger trust or reliability claims, close the API-time permission gap, clarify read behavior for updated batches, fix tie-safe pagination, reconcile the External Shipping direct-write exception, and align the Email preference UI with the absence of a sender. No competitive differentiator or measured outcome is established.
