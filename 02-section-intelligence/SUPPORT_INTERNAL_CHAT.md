# Support / Internal Chat — Section Intelligence

## 1. Audit Metadata

- **Audit date:** 2026-09-26.
- **Methodology:** `00-methodology/MASTER_INSTRUCTIONS.md` v1.1; `CODEX_OPERATING_PROTOCOL.md` v1.0.
- **Competitive reference:** `01-competitive/WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md` v1.
- **Intelligence source:** `jetshop7/wossol-brand-intelligence`, `main`, synchronized clean to `36b805fee34649e779b04872d2fa7694668eeffc` before task interpretation.
- **Product source:** `jetshop7/wossol-platform`, branch `dev/wossol-integration`, commit `020593219001f857cace7bf80ad80fe2930319d4`, equal to upstream. At audit start, three uncommitted Shopify frontend changes were visible; by final inspection, two additional uncommitted Advertising Meta OAuth service/spec changes were also present. These five Product edits were not inspected for findings, staged, modified, or included; product conclusions are scoped to the recorded committed revision.
- **Evidence limitation:** no deployed Support operation, production DB/storage, notification delivery, real agent response time, support outcome, or competitor ticket/chat workflow was verified.

## 2. Audit Coverage Map

| Surface | State | Coverage | Evidence |
|---|---|---|---|
| Merchant Support UI/routes | INSPECTED | Ticket list, category-first intake, linked context, detail, messages, attachments, close/reopen, Chat handoff | EV-SIC-001–002 |
| Merchant Support API and access | INSPECTED | scoped identity, create/list/detail/reply, linked entity validation, status windows, attachment authorization | EV-SIC-003–004 |
| Admin Support operations | INSPECTED | Workspace queue/snapshot, filters, status/priority/department, assignment API, private notes, reply/correction, resolve/close/reopen | EV-SIC-005–006 |
| Shared Confirmation Chat UI/API | INSPECTED | canonical conversation, merchant/worker/Admin surfaces, polling, order links, urgency, read cursors, escalation/handoff | EV-SIC-007–009 |
| Chat authorization | INSPECTED | Merchant relationship, worker/team membership + assignment, Team Lead supervision, workspace permission, bounded linked Orders | EV-SIC-008 |
| Schema, migration, events and notifications | INSPECTED | ticket/message/timeline/note/assignment/resolution evidence, chat/read/alert/attention, domain events, selected notification policy | EV-SIC-010–011 |
| Tests | INSPECTED / PARTIAL | 60 backend Support/Chat tests and 3 frontend Chat polling source checks passed; no Support frontend source specs found in searched app paths | EV-SIC-012 |
| Current design and execution history | INSPECTED | Support ownership and manual internal wait; Chat design addenda/handoff boundary; P0-16 structured resolution facts | EV-SIC-013–014 |
| Deployment, storage durability and service outcomes | NOT VERIFIED | repository only; no live deployment or operational data | §21, §28 |
| Direct competitor equivalence | PARTIALLY INSPECTED | competitive baseline only; no feature-level competitor workflow research | §16 |

## 3. Executive Section Truth

Support is a current two-sided issue workflow: Merchants open category-based, optionally owner-linked Tickets and exchange messages/images with Workspace-authorized Support staff; internal staff triage a concrete-Workspace queue, use private notes, move Tickets through guarded states, and resolve/close with evidence. Support records and coordinates the issue; it does not own or mutate the linked Order, Product, Inventory, Finance, Tracking, or External Shipping truth. Merchant messaging is scoped to their active Merchant/Workspace; internal agent access is permissioned and Workspace-scoped.

“Internal Chat” requires a distinction: the current Chat system is not a private employee-to-employee or Support-agent-only room. It is one canonical, shared Merchant-per-Workspace conversation between Merchant users and operationally authorized Confirmation workers/leads/managers/Admins. It handles pre-dispatch operational communication, optional Confirmation Order context, unread/attention/urgent signals and linked-Order worker attention. Post-dispatch Merchant messages are blocked from that channel and offered as an explicit, unsubmitted handoff into a Delivery Issue Support form. Support Tickets then own the merchant-facing issue conversation; internal coordination is represented by private notes and `WAITING_INTERNAL`, not an internal task/chat-routing system.

The strongest evidence-building extension is P0-16: each authorized Admin resolution can append separately entered, ticket-scoped resolution/root-cause codes, responsible party, preventability and recurrence group, alongside the required human resolution note. This is structured evidence, not yet usable Support intelligence: no code taxonomy is seeded, no aggregate read/report/recommendation flow was found, old Tickets are not backfilled, and operator-entered labels are not independently validated.

## 4. Scope & Architecture Map

Support owns Ticket identity, category, subject, issue-specific Merchant/Support-agent messages and images, private internal notes, status/priority/responsible department, manual assignment, Ticket timeline, current resolution/closure fields and append-only resolution-episode evidence. It accepts bounded context links to specific owner-domain entities after Merchant/Workspace authorization; linked systems retain their own mutation authority. Notification events can route selected Support events to the Merchant notification surface.

Chat is separately owned: one canonical conversation per Merchant/Workspace, immutable messages/images, optional linked Order, per-user read cursor, shared needs-reply state, bounded urgency, alerts and worker-specific linked-Order attention. Confirmation/Team owns the operational workforce context; Chat access derives from active workspace/merchant membership, active Merchant Confirmation team membership, active Order assignment, effective Team Lead scope or explicit Chat permission. The Chat message is not an Order mutation. The communication eligibility service and persisted dispatch evidence divide pre-dispatch Confirmation Chat from post-dispatch Support.

## 5. Current Capability Inventory

| Capability | Status | Current behavior |
|---|---|---|
| Merchant Ticket intake | LIVE | Eleven categories; required subject/message; up to five validated private images; idempotent creation. General categories work without an Order. |
| Linked context | LIVE, BOUNDED | Category-specific Order, Product/Variant, External Shipment, Finance cycle, or partial withdrawal lookup; server revalidates ownership and category relationship. |
| Merchant Ticket conversation | LIVE | Merchant and Support Agent messages, paginated Merchant history, images, safe timeline and controlled reply/close/reopen windows. |
| Admin queue | LIVE | Workspace-scoped counters, page/search/category/status/priority/department/assignee/entity filters, unread-reply and overdue filters; selected controls are not all exposed in the current queue UI. |
| Internal notes | LIVE | Separate append-only notes, actor snapshots, permission-gated retrieval, excluded from Merchant DTOs. `WAITING_INTERNAL` remains a manual state, not a task/routing engine. |
| Assignment | PARTIAL | Permissioned agent lookup and assign/unassign API/history exist; current Admin ticket workspace/queue UI does not expose assignment controls per current UI spec. No workload balancing/auto-routing. |
| Structured resolution episode | LIVE CODE PATH | Admin resolution requires human note and atomically creates append-only optional structured facts plus status/timeline/Audit/Event. No aggregate analysis/read surface found. |
| Shared operational Chat | LIVE | Canonical Merchant/Workspace Chat, scoped authorized participants, paginated history, text/images, per-user read cursor, visible-tab polling, urgency and attention indicators. |
| Post-dispatch handoff | LIVE | Server rejects dispatch-eligible linked Chat sends; Merchant can choose Continue to Support, carrying Order and text to an unsubmitted Support draft; images must be reselected. |
| Private staff-only Chat | NOT FOUND AFTER SEARCH | Chat sender/context and model support Merchant plus Confirmation roles/Admin, not a separate private internal chat room. Internal Support coordination uses notes/manual wait state. |
| AI support automation / SLA intelligence | NOT FOUND AFTER SEARCH | No AI classification/reply, automatic routing, internal task inbox, advanced SLA dashboard or support outcome analytics established. |

## 6. Workflow & Lifecycle

1. The authenticated Merchant enters Support in one active Workspace. The backend resolves the active Merchant relationship rather than trusting UI-supplied Merchant scope. A category-first form optionally discovers bounded owner-domain context; creation revalidates links, accepts subject/message/images, creates the Ticket plus first Merchant message/timeline/Audit/Domain Event transactionally, and uses an idempotency key to avoid duplicate creation.
2. Delivery Issues require an eligible Order with authoritative post-Confirmation dispatch/delivery evidence. Orders still in Confirmation are rejected for Support and directed to Confirmation Chat. Product/Inventory, External Shipping, Finance and Withdrawal links are similarly scoped to the active Merchant/Workspace and constrained to the matching category; Withdrawal linking is only for partial withdrawal requests. Tickets in other categories can be created without an entity selector.
3. Merchant and Admin/support-agent messages are separate persisted Support messages. Image files are validated by signature/type/dimensions and stored under private authenticated retrieval; storage is currently local `private-uploads` in inspected source. Message content is not embedded into the ordinary timeline. Merchant can close a Ticket; only a resolved Ticket may be explicitly reopened by the Merchant, and only within 24 hours of resolution.
4. Admin Support requires active internal identity, a concrete Workspace and relevant permission. It offers queue counters/search/filters; agents can reply, add private notes, change priority/department/status, resolve/close/reopen and correct only an eligible latest Support reply. Status transitions are guarded. `WAITING_MERCHANT` gets a deadline (default 48 hours); overdue is derived at query time, not an automatic transition. `WAITING_INTERNAL` and responsible department are manual coordination markers.
5. Resolution atomically sets mutable current Ticket state, requires a human resolution note, optionally appends a `SupportResolutionIntelligence` row with actor/scope snapshots and structured facts, and adds timeline/Audit/Domain Event evidence. Reopen clears current resolution fields but does not erase prior P0-16 episodes. No P0-16 read/aggregation consumer was found.
6. Confirmation Chat opens/reuses the unique Merchant/Workspace conversation. Active merchant/team/order/Team Lead/explicit Workspace access determines who sees it; the UI uses visible-only 20-second thread polling and 30-second worker attention polling. Messages can carry optional eligible pre-dispatch Order context, validated image(s), idempotency and rate limits. Merchant urgency uses a required reason and drives Chat alert/attention only, not Order priority/status.
7. If an Order has dispatch evidence, linked Chat send is blocked server-side, audited, and surfaced as an explicit Merchant routing dialog. Continue preserves text and Order in an unsubmitted Support form; Cancel leaves the Chat draft; images require reselection. No Ticket is created until explicit submission. Neither Chat nor Support performs provider communication or owner-domain state mutation through the message itself.

## 7. Value Recipient Map

| Recipient | Current value |
|---|---|
| Merchant / owner | One general intake surface; can attach eligible operational context/images, follow Ticket status/history, reply within bounded windows and explicitly route delivery issues to Support. |
| Support agent | Workspace-level searchable triage queue, context links, private notes, status/priority/department decisions and resolution/closure evidence. |
| Confirmation worker/team lead/manager | Operational conversation context, unread/urgent/linked-Order attention inside authorized Merchant/assignment scope; not full support case management. |
| Owner departments | Can be named as responsible department or contacted manually while Support waits; no automatic internal task dispatch is implied. |
| Product/Operations leadership | Structured resolution episode facts may later support cause/recurrence analysis if codes and downstream access are governed. Current analysis surface not found. |
| End customer / provider | No direct Chat/Ticket access, customer messaging, or provider chat established by these modules. |

## 8. Control & Merchant Agency

Merchant control is Level 3 for issue submission and conversation participation: choose issue category, provide context and evidence, reply, close, and explicitly signal unresolved status during the post-resolution window. Backend eligibility gates the communication channel based on dispatch evidence. Merchant cannot choose support-agent assignment, change internal department/priority/responsibility, see private notes, set root-cause facts, or mutate owner-domain records through Support/Chat.

Internal control is permission-granular: queue read, reply, note, status, priority, department, resolve, close, assign, linked summary and message correction permissions are separable. Shared Chat Admin access is separately permissioned; worker and team-lead access is relationship/scope-derived. This is accountability infrastructure, not proof of adequate staffing, successful resolution, or a live SLA.

## 9. Transparency & Trust

Ticket evidence includes actor/name/role snapshots, message timestamps, attachment metadata, append-only timeline, Audit/Domain Events, separate private notes, assignment history, controlled status transitions, explicit resolution/closure information and Merchant-safe projections. Admin can correct an eligible latest Support reply without deleting its original stored body; however, the Merchant messages query omits `correctedBody`, and the Merchant UI renders the original `body`, so the correction is not projected to the Merchant. Chat captures sender context snapshots, stable conversation identity, message idempotency, per-user last-read cursor, merchant-facing sent/read projection, linked Order identity and urgency-resolution evidence. Backend scope guards are material because both surfaces contain customer/order and operational context.

Limits: a Ticket's Admin resolution note is not projected in the Merchant Ticket detail/messages API; the Merchant sees generic resolution timeline/status and any separately sent Support reply. The “unread Merchant replies” Support queue metric/filter is not a true per-agent read cursor or latest-message-unanswered test: P1 currently counts any Merchant message in the last seven days for OPEN/IN_PROGRESS Tickets, even if a later Support message already answered it. Chat is more explicit with individual read cursors, but team reply need is shared. Both chats update by polling, not WebSockets. Private upload code resolves files under local process `private-uploads`; cross-instance persistence, retention, malware scanning, backups and production authorization were not verified.

## 10. Merchant Value Extraction

The functional value is safe routing and continuity: the Merchant can report an issue once with relevant authorized context, while lifecycle decisions remain with the owning Product/Order/Finance/Tracking/Shipping domain. Confirmation Chat offers a lower-friction pre-dispatch operational exchange; when lifecycle changes, the system prevents the same linked message from entering the wrong channel and requires an explicit handoff. The issue conversation, internal note and owner action remain distinct.

Potential practical benefit is less context reconstruction and fewer misrouted communications than fragmented calls/messages/forms, but no baseline or measured resolution-time/reopen/merchant-satisfaction effect was found. No Wossol service guarantee or response-time outcome should be inferred from a queue counter or waiting deadline.

## 11. Feature Clusters

1. **Category → scoped context → issue record:** category-first intake + authorized bounded selectors + explicit submit + idempotent Ticket/message creation reduces wrong-entity linkage and duplicate retries.
2. **Shared Chat → dispatch-aware handoff → Support case:** one operational conversation remains useful before dispatch; after authoritative dispatch, a blocked send and explicit draft conversion preserve context while keeping routing and Ticket creation under Merchant control.
3. **Internal handling → separate Merchant truth:** private notes, manual WAITING_INTERNAL, merchant-safe timelines, permissioned replies and owner-system boundary allow internal coordination without exposing staff deliberation or granting Support unrelated mutation authority.
4. **Resolution episode → future root-cause evidence:** required human explanation plus append-only structured codes/party/preventability/recurrence grouping can preserve facts across reopen/re-resolution cycles; utility depends on consistent data and a yet-unobserved analysis loop.

## 12. Merchant Journey / Old Way vs Wossol Way

| Stage | Plausible manual alternative (not universal/verified) | Current Wossol evidence |
|---|---|---|
| Ask for help | WhatsApp, calls, informal form or separate issue tracker | Workspace-scoped category-based Ticket intake; no merchant baseline measured |
| Explain context | Copy identifiers or screenshots into a message | bounded authorized Order/Product/Shipment/Finance/Withdrawal selectors and validated images |
| Clarify pre-dispatch Order | move between Order page and staff contact | Confirmation Chat with optional Order link/attention |
| Report post-dispatch issue | continue in wrong channel or start over | blocked Chat send plus explicit Support draft carrying Order/text |
| Coordinate internally | side conversations and lose context | private note, department marker, WAITING_INTERNAL and timeline; still manual follow-up |
| Understand resolution | reopen conversation or infer from status | resolved status/generic event and 24-hour response window; Admin note itself is not exposed in Merchant detail |

Alternative workflows are illustrative, not universal. Savings, response speed and resolution quality were not measured.

## 13. Hidden / Non-Obvious Advantages

- The Ticket code path rejects Confirmation Orders before writing a Support Ticket; linked dispatch evidence—not a status label alone—governs post-Confirmation eligibility.
- The Chat-to-Support handoff is not an automatic conversion: it preserves a draft until Merchant explicitly continues and submits, preventing accidental Ticket creation or message loss on Cancel.
- P0-16 keeps resolution episodes even when the mutable Ticket is reopened and current resolution fields are cleared; a composite FK keeps each fact bound to the exact Ticket/Workspace/Merchant.
- Structured `UNKNOWN` remains distinct from `false` preventability; free-text notes/categories do not silently generate cause/responsibility codes.
- Chat's manager/team-lead model avoids treating all confirmation employees as readers: Team Lead access is bounded by active effective supervision scopes and worker relationships.
- Support owner links are context pointers. Creating/replying/resolving a Ticket does not mutate the linked owner system.

## 14. Data & Intelligence Assets

Support accumulates category, priority, lifecycle, responsible department, assignment episodes, Merchant/agent message metadata, subject and linked entity identity, timestamps, required resolution note, close reason, structured resolution/root-cause codes, responsible party, preventability, recurrence group and actor snapshots. This is potentially valuable operational evidence. However, message text and free-text resolution notes are not structured truth; resolution codes are optional operator-entered uppercase keys with no taxonomy; legacy Tickets lack backfill; no read-side aggregation, benchmark, SLA outcome, recurrence report or recommendation use of the P0-16 table was found. It is a data foundation, not current support intelligence.

Chat data includes Merchant/Workspace conversation identity, message/sender/role, optional Order, urgency reason, read cursor, attention resolution, alerts, attachments and timestamps. It can support scoped communication accountability and future workload/response analysis. It is not conversation search, Customer graph, automatic triage, knowledge base, or support learning; text search across history and AI are explicitly deferred.

## 15. Cross-Section Compound Advantages

| Connection | Compound effect | Boundary |
|---|---|---|
| Orders + Confirmation Chat | pre-dispatch Order context can be discussed in the operational channel without Chat owning status/assignment | human conversation is not Order lifecycle control |
| Orders + Support | authoritative dispatch evidence routes eligible Delivery Issues away from Confirmation Chat | no provider messaging or guaranteed resolution |
| Finance / Withdrawal + Support | Merchant may attach scoped cycle or partial-withdrawal context to report a problem | Support does not approve, adjust or settle funds |
| External Shipping + Support | Merchant can create shipment-linked issue context | External Shipping retains shipment/warehouse authority |
| Team + Chat | membership/assignment/effective Team Lead scope constrain who sees operational conversation | not unified workforce or company-wide private Chat |
| Support + Domain Events/Notifications | selected status/reply/resolution events may surface in the Merchant notification system | event/notification does not establish timely human action |
| Support + future analytics | append-only resolution facts could connect issue causes to owner-domain outcomes | current analysis/recommendation loop absent |

## 16. Competitive Analysis

The competitive master treats customer service, human confirmation and operating teams as common parts of the broader market-access/COD stack. That supports Support/communications as operational foundations; it does not verify equivalent product depth in any named competitor. No direct competitor Ticket or internal Chat account/workflow review was performed. Wossol can accurately show its own dispatch-aware channel boundary, scoped shared Confirmation conversation and structured case-resolution evidence, but cannot claim unique Support/Chat capability, faster resolution, superior agent control or a moat based on the current baseline.

## 17. Marketing Intelligence

**Safe present-tense description:** “Give each support issue a Workspace-scoped Ticket with relevant order or operational context, a Merchant conversation, and a separate internal timeline. Keep pre-dispatch Confirmation conversations in Chat and route post-dispatch delivery issues through an explicit Support handoff.” For internal Chat: “Share one scoped Merchant/Confirmation conversation with order context and attention signals.” Qualify that urgent signals are not Order priority, linked posts after dispatch are blocked, and responses depend on human operations.

Potential future-support-data language is not eligible today: do not claim AI support, root-cause analytics, smart routing, SLA automation, internal collaboration chat, support quality benchmarks, guaranteed response, or resolution intelligence unless an analysis/operational outcome path is established.

## 18. Surprise Findings

- Support's most strategic near-term behavior may be negative capability: it refuses the wrong channel and leaves Order/Finance/Tracking authority with those owner systems.
- P0-16 adds materially better evidence granularity than a generic “resolved” state, but its novelty should not be confused with usable intelligence without taxonomy/read-side application.
- The Admin queue's “unread” label is notably weaker than Chat's actual per-user read cursor and can overcount already answered Merchant messages.
- The assignment backend is ahead of the current Admin UI: the permissioned API exists, while the published Admin UI contract keeps assignment out of the V1 workspace.

## 19. Potential Category Reframes

Candidate for later validation: **evidence-linked operational support** or **communication that follows the work stage**—operational Chat before dispatch, case ownership after dispatch, and owner systems retaining mutation authority. This is an architecture/workflow observation, not proof of a unique category or final positioning.

## 20. Brand Evidence

| Candidate evidence | Strength | Qualification |
|---|---|---|
| Wrong-channel linked Order messaging is blocked and handoff requires Merchant confirmation | STRONG code/test-level | Does not prove timely human response or customer outcome |
| Owner-system boundaries and scoped entity references | STRONG source-level | Production permission/service operation not verified |
| Internal-only notes stay distinct from Merchant conversation | STRONG source/schema/UI-level | Attachment storage/deployment privacy not production verified |
| Resolution episodes preserve separately structured facts after reopen | STRONG schema/service/test-level | Optional operator input; no aggregation or validated taxonomy |
| Shared Chat provides per-user read state and visible polling | STRONG implementation-level | Polling is not instantaneous delivery; no measured response performance |
| Reliable, fast or high-quality Support outcomes | UNSUPPORTED | No operational cohort or SLA result reviewed |

## 21. Weaknesses / Risks / Gaps

- The Support queue’s “unread Merchant replies” filter/count uses any Merchant message within seven days on OPEN/IN_PROGRESS rather than checking latest responder or an agent read cursor; it can count an already answered message and should not be called true unread state.
- Merchant receives no `resolutionNote` field in Ticket detail and no resolution message is created by the resolve action; only generic status/timeline appears unless an agent separately replied. This may leave resolution explanation invisible despite being required internally.
- Admin can correct the latest eligible Support reply, but the Merchant API projection omits `correctedBody` and Merchant UI renders only original `body`; the correction is not communicated to the Merchant through this Ticket conversation.
- P0-16 fields are optional and unstandardized beyond uppercase syntax. Recurrence keys can fragment, responsibility/cause can be inconsistently assigned, and there is no analytics/report/decision layer. Legacy Tickets are not backfilled.
- Assignment endpoint and agent lookup are not exposed in current Admin UI; Admin UI contract says assignment is deferred, while API/permission/schema/history support it. This is a partial/contract alignment, not a full assignment engine.
- Queue and internal wait states do not create department tasks or automatic routing; `WAITING_INTERNAL` requires out-of-band coordination. Default 48-hour merchant waiting deadline is a query-time overdue marker, not an auto-reminder or SLA guarantee.
- Chat is polling-based; no WebSockets, message-history search, per-Order conversations, private worker chats, or direct provider channel were found. Merchant urgent flag is attention only.
- Private images are stored on local private-uploads path in inspected code. Shared/durable storage, retention/backup, malware scanning and operational recovery were not verified.
- No authenticated production browser or live service account/database test was performed. Five unrelated dirty Product edits existed, and final working-tree state is dirty.

## 22. Future Strategic Potential

If Support standardizes codes responsibly, links resolution episodes to authoritative owner-domain outcomes, builds a scoped read model, validates privacy and sample sizes, and measures recurrence/resolution quality, it could improve issue prevention and owner-team feedback. A real internal follow-up/task loop could connect `WAITING_INTERNAL` to accountable department action. Neither capability is current. Current evidence supports support workflow infrastructure and a structured data seed, not support intelligence or autonomous resolution.

## 23. Claim Safety

| Claim | Safety |
|---|---|
| “Create scoped Support Tickets with linked eligible Order/Finance/product/shipment context” | GREEN, source/test-level |
| “Route pre-dispatch Order discussion to shared Confirmation Chat and post-dispatch Delivery Issue to Support” | GREEN with dispatch-evidence and explicit-submission qualification |
| “Keep private notes separate from Merchant-visible conversation” | GREEN, code/schema-level |
| “Record structured facts for each Admin resolution episode” | GREEN with optional/operator-entered qualification |
| “Identify recurring root causes or prevent future issues” | RED / no current read-side or outcome loop |
| “Automated internal routing / instant response / SLA guarantee” | RED / not established |
| “Internal Chat for Support staff” | RED as a separate private room; current Chat is shared Merchant/Confirmation/Admin |
| “Unread Support reply tracking” | YELLOW/QUALIFY: current heuristic is recent merchant-message presence, not true user-specific unread |
| “Support resolution explanation is shown to Merchant” | RED for resolution note itself; Admin must separately send a reply |

## 24. Commercial Magnitude

**FOUNDATIONAL TRUST / OPERATIONAL FOUNDATION.** Issues around Orders, customer delivery, payouts, stock, account access and products have material merchant consequences. Correct channeling, context and privacy are useful foundations. Actual response SLA, resolution quality, satisfaction, cost-to-serve, recurrence prevention and retention effect are unknown; no quantified commercial outcome is supported.

## 25. Strategic Classification

| Area | Classification |
|---|---|
| Support Ticket intake/conversation and Admin queue | TABLE STAKES / FOUNDATIONAL |
| Dispatch-aware Confirmation Chat → Support handoff | POTENTIAL DIFFERENTIATOR in channel ownership/control design; competitor parity unverified |
| Scoped shared Chat with per-user read cursor and work attention | OPERATIONAL FOUNDATION; depth useful but competitive status insufficiently verified |
| Append-only structured Support resolution episode | POTENTIAL DATA FOUNDATION, not current intelligence |
| AI, proactive root-cause learning, automated internal routing | WHITESPACE / NOT FOUND AFTER SEARCH |

## 26. Action Register

| Priority | Action | Rationale |
|---|---|---|
| MUST FIX / CLARIFY | Replace the Support “unread Merchant replies” heuristic with an actual latest-unanswered or agent read-state contract, or rename it to “recent Merchant replies.” | Current label can misrepresent queue work. |
| MUST CLARIFY | Decide whether Merchant should receive the human resolution note as a Support message/merchant-safe field; align current UI/API and approved communication contract. | Required internal resolution note is not visible in current Merchant projection. |
| MUST MATCH | Align assignment capability contract: either expose the existing permissioned manual assignment API in Admin UI or deprecate/gate it to match the UI spec. | API/schema/permission and V1 UI scope diverge. |
| MUST VERIFY | Validate shared private image storage, access, retention, backup and recovery in deployed topology. | Local process path is not evidence of production durability/privacy. |
| WORTH ADOPTING | Define a governed P0-16 code set and authorized scoped aggregate/reporting before using resolution evidence as intelligence; preserve UNKNOWN and provenance. | Data fields alone do not create learning. |
| MUST VERIFY | Measure queue age, first response, resolution/reopen rate and issue outcomes from production event evidence before service claims. | No performance/outcome evidence reviewed. |
| MUST BEAT | Perform account/workflow-level competitor Support and operational-chat comparison if a differentiation claim is needed. | Current competitive master is category-level, not feature-level. |

## 27. Evidence Register

**EV-SIC-001 — Merchant Support UI and contextual entry points.** **Type:** P1/P2. **Paths:** `apps/frontend/src/app/merchant/support/{page.tsx,new/page.tsx,[ticketId]/page.tsx}`, `support-data.ts`, `apps/frontend/src/app/merchant/orders/detail/page.tsx`; current `docs/ui/merchant/MERCHANT_SUPPORT_UI_SPEC.md`. **Observed:** category-first Ticket intake, bounded selectors, safe details/timeline, close/reopen affordances, dispatch-aware Order entry, unsubmitted Chat handoff. **Confidence:** High source-level; authenticated production UX not verified.

**EV-SIC-002 — Admin Support UI.** **Type:** P1/P2. **Paths:** `apps/frontend/src/app/admin/support/{page.tsx,tickets/[ticketId]/page.tsx,support-data.ts}`; `docs/ui/admin/ADMIN_SUPPORT_OPERATIONS_UI_SPEC.md`. **Observed:** queue counters/filters and detail conversation/private notes/status/priority/department/resolve/close/reopen/correction; current visible queue exposes status/category/priority/unread/overdue and search. No current assignment controls found in rendered pages. **Confidence:** High source/spec inspection.

**EV-SIC-003 — Merchant Support APIs and authorization.** **Type:** P1. **Paths:** `apps/backend/src/modules/support/{support.controller.ts,support-access.service.ts,support.service.ts}`. **Observed:** active authenticated Merchant, unique active Merchant/Workspace relationship, category/link validation, scoped list/detail/reply, idempotent creation/replies, response windows and close/reopen. **Confidence:** High source and focused service tests.

**EV-SIC-004 — Ticket links, attachments and owner boundary.** **Type:** P1/P2. **Paths:** `support.service.ts` (`validateLinks`, `attachment`), `support-image.service.ts`, `order-communication-eligibility.service.ts`. **Observed:** category-constrained Order/Product/Variant/Shipment/FinancialCycle/partial Withdrawal context; authoritative dispatch eligibility; image signature/size/dimension validation and private authenticated retrieval; Ticket actions do not mutate owner domain. **Confidence:** High source/tests; live storage not verified.

**EV-SIC-005 — Admin Support APIs, operations and permission.** **Type:** P1. **Paths:** `apps/backend/src/modules/support/{admin-support.controller.ts,admin-support-access.service.ts,admin-support.service.ts}`, `permission-catalog.ts`. **Observed:** concrete Workspace + active Internal Employee/permission; queue/snapshot, filters, agents, manual assignment API, notes, transitions, resolve/close/reopen and message correction. Assignment API exists while current UI omits it. **Confidence:** High source/tests.

**EV-SIC-006 — Status, note privacy and resolution evidence.** **Type:** P1/P2. **Paths:** `admin-support.service.ts`; schema `SupportTicket`, `SupportInternalNote`, `SupportTicketTimelineEvent`, `SupportTicketAssignment`, `SupportResolutionIntelligence`; migration `20261012_p0_16_support_resolution_intelligence/migration.sql`; `docs/.../SUPPORT_ROOT_CAUSE_RESOLUTION_INTELLIGENCE_P0_16.md`. **Observed:** 48-hour default WAITING_MERCHANT due marker; explicit status transitions; private note; required human resolution note; append-only per-resolution structured evidence; reopen clears mutable state but does not delete episode facts. No P0-16 consumer/aggregate read path found. **Confidence:** High source/schema/test; approved resolution semantics are document intent.

**EV-SIC-007 — Shared Chat routes/UI.** **Type:** P1/P2. **Paths:** `apps/frontend/src/app/chat/{ChatWorkspace.tsx,chat-data.ts,ChatImage.tsx}`, Merchant/confirmation-worker/Admin Chat pages. **Observed:** one Merchant Chat view and operational inbox, per-Order navigation/context, urgency, image composition, visible polling, unread/read projection, retry and dispatch handoff. **Confidence:** High source; not live session tested.

**EV-SIC-008 — Chat APIs, data model and authorization.** **Type:** P1. **Paths:** `apps/backend/src/modules/chat/{chat.controller.ts,chat.service.ts,chat-access.service.ts}`, `apps/backend/prisma/schema.prisma` Chat models/enums. **Observed:** unique `(workspaceId, merchantId)` canonical conversation; role/relationship-scoped readers, message sender snapshots/idempotency, read cursors, urgency resolution, alerts, worker attention and time-bounded Team Lead supervision. **Confidence:** High source and focused tests.

**EV-SIC-009 — Chat/Support channel handoff.** **Type:** P1/P2. **Paths:** `ChatService.send`, `ChatWorkspace.tsx`, `SupportService.validateLinks`, `apps/backend/src/modules/orders/order-communication-eligibility.service.ts`, Chat and Merchant Support specs; Chat design addendum dated 2026-08. **Observed:** qualifying post-dispatch linked Chat send is audited and blocked; Merchant chooses to continue, retaining text/Order as an unsubmitted Support form draft; images must be reselected. **Confidence:** High implementation/test-source; no live interaction test.

**EV-SIC-010 — Schema and persistence.** **Type:** P1. **Path:** `apps/backend/prisma/schema.prisma`, migrations `20260807_shared_chat_v1`, `20260822_merchant_order_support_v1`, `20260828_admin_support_operations_v1`, and `20261012_p0_16_support_resolution_intelligence`. **Observed:** distinct Support Tickets/messages/notes/timeline/assignment/resolution episodes and shared Chat conversation/messages/read/attention/alerts/scopes. **Confidence:** High schema/migration inspection.

**EV-SIC-011 — Domain Events and notifications.** **Type:** P1/P2. **Paths:** `admin-support.service.ts`, `support.service.ts`, `chat.service.ts`, `apps/backend/src/modules/events/domain-event-contract-registry.ts`, `notifications.service.ts` and notification specs. **Observed:** meaningful Support/Chat actions publish scoped events; selected Support reply/WAITING_MERCHANT/resolved event types have Merchant notification mappings. No guarantee of delivery/read/response time. **Confidence:** High source/tests; runtime delivery not verified.

**EV-SIC-012 — Focused verification.** **Type:** P2. **Observed:** selected Support Merchant/Admin service specs plus Chat service/access/image specs passed 60/60; frontend `chat-polling.source.spec.ts` passed 3/3. Searched frontend Support surfaces contained no dedicated Support UI spec; no app-wide typecheck, database integration, production browser, or storage test was run. **Confidence:** High for commands run in the recorded workspace.

**EV-SIC-013 — Current source-of-truth documents.** **Type:** P2/P3. **Paths:** `docs/ui/merchant/MERCHANT_SUPPORT_UI_SPEC.md`, `docs/ui/admin/ADMIN_SUPPORT_OPERATIONS_UI_SPEC.md`, `docs/wossol-system-design/01-system-design/admin-platform/support/**`, `docs/wossol-system-design/01-system-design/core-systems/Chat System Design.md`. **Observed:** current Merchant handoff/response-window contract and Chat addendum; Admin UI assignment explicitly deferred; older Support Operations design contains stale “not implemented” alignment note. **Confidence:** High for document content, not equal authority to P1 behavior.

**EV-SIC-014 — Cross-section and competitive context.** **Type:** P1/P3. **Sources:** intelligence `02-section-intelligence/{ORDERS,CONFIRMATION,CUSTOMERS,TRACKING_DELIVERY,FINANCE,TEAM,EXTERNAL_SHIPPING,MESSAGING_WHATSAPP_MESSENGER_ORDER_CAPTURE}.md`, `01-competitive/WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md`; current Product execution history around Shared Chat V1 acceptance and support handoff. **Observed:** Orders dispatch boundary, Confirmation ownership, Finance and Tracking authority, separate Messaging capture model, and category-level competitor support/service context. **Confidence:** High for repository sources; competitor feature parity not researched directly.

## 28. Contradictions & Uncertainty

1. **CONTRADICTION-SIC-001 — Support Operations implementation status.** **Source A (P3/stale alignment note):** `Support Operations Workspace.md` says the internal Admin workspace/agents/queues/notes are not part of Merchant Support V1 and are not created by the implemented foundation. **Source B (P1):** current Admin Support controller/service/schema/UI implement the Workspace queue, agents, notes, message replies and lifecycle controls. **Nature:** old implementation-alignment text describes an earlier phase. **Working conclusion:** current repository demonstrates an Admin Support Operations implementation; do not repeat the old absence statement as current truth. Current Admin UI spec and P1 supersede the stale note for present capability, while unimplemented routing/task functions remain absent.
2. **CONTRADICTION-SIC-002 — Manual assignment scope.** **Source A (P2):** current Admin Support UI spec says manual assignment remains backend-compatible but is not exposed in V1 queue/detail. **Source B (P1):** API, agent lookup, permission and assignment history exist. **Nature:** API capability is not a surfaced operator workflow. **Working conclusion:** classify as partial/backend-only; don't claim normal Admin UI supports assignment until contract is reconciled.
3. **CONTRADICTION-SIC-003 — Resolution note communication.** **Source A (older P3 Support Operations design):** resolution summary/message should be communicated to Merchant. **Source B (P1):** Admin resolve writes `resolutionNote` and event/timeline evidence but creates no SupportMessage; Merchant detail projection omits `resolutionNote`. **Nature:** recorded human explanation is not delivered through the Merchant Ticket conversation by this action. **Working conclusion:** current Merchant view receives resolved state/generic timeline, and an agent must separately reply for a visible explanation; product contract should decide if this is intended.
4. **CONTRADICTION-SIC-004 — Message correction visibility.** **Source A (P1/Admin UI):** Support can correct the latest eligible Support reply while retaining its original record; Admin UI displays `correctedBody`. **Source B (P1/Merchant API/UI):** Merchant `messageSelect` omits correction fields, and Merchant rendering uses `body`. **Nature:** Admin's corrected reply is not surfaced to its intended Merchant recipient. **Working conclusion:** the source retains both original and correction for Admin but the Merchant continues to see the original; align the projection/UI with the correction contract.
5. **CONTRADICTION-SIC-005 — “Unread Merchant Replies” semantics.** **Source A (P1 UI labels/API fields):** queue card/filter is named unread Merchant replies. **Source B (P1 query):** predicate is any Merchant-authored message from the last seven days for OPEN/IN_PROGRESS Ticket; no last-reply comparison or read state is tested. **Nature:** label overstates actual query predicate and may retain answered messages. **Working conclusion:** call it a recent-Merchant-message heuristic, not unread truth, until corrected.
6. **CONTRADICTION-SIC-006 — Chat scope description.** **Source A (older Chat design sections):** historical V1 limits describe no standalone operational inbox/no urgent support and post-dispatch auto-conversion. **Source B (P1 and explicit 2026 design addendum):** expanded bounded inbox/urgency and explicit unsubmitted handoff replace older wording. **Nature:** appended approval supersedes conflicting earlier paragraphs. **Working conclusion:** use current P1 and explicit addendum; do not describe auto-conversion or unrestricted Workspace inbox.
7. **UNCERTAINTY-SIC-007 — Data/storage/notifications and service quality.** Local file storage path is evident, but deployed shared storage, access hardening, retention, backups, real notification delivery, agent staffing, response/resolution time, satisfaction, recurrence prevention and economic effect were not verified.
8. **UNCERTAINTY-SIC-008 — Resolution evidence quality.** P0-16 records optional free-choice stable-looking codes, but no controlled dictionary, cross-operator inter-rater quality, historical backfill, data report, linkage to independent outcome, or privacy-governed aggregate is evidenced.
9. **UNCERTAINTY-SIC-009 — Competition.** Competitive master covers category-level customer service/operational teams; direct Support ticket workflow and Chat access/depth equivalence remain unverified.

## 29. Open Questions

1. Should the resolved human note be sent to the Merchant or stay internal, and what correction/reopen semantics apply if it changes?
2. How should a corrected Support reply become visible to the Merchant while preserving its original and correction history?
3. Should assignment remain API-only or be surfaced in the current Admin workspace? Which permissions govern assignment reads and writes in production?
4. Does Support need real agent read cursors/latest-unanswered semantics, or should current “unread” surfaces be renamed to match the seven-day heuristic?
5. What image storage, retention, backup, malware/abuse controls and disaster recovery are deployed?
6. Are Support agent notification events delivered reliably, and what response-time/service policy is actually staffed?
7. Which governed root-cause/resolution taxonomy and scoped read-side are approved for P0-16? Who can query it, and how are recurrence claims validated?
8. Is an internal Support-to-owner-department task/workflow expected, or are manual notes and WAITING_INTERNAL the intended supported process?
9. Which named competitors have verified comparable case-management and operational chat workflows?

## 30. Methodology Learnings

No general methodology change identified. Section-specific: for any “unread” queue measure, inspect its persisted read cursor or equivalent latest-response predicate rather than relying on a UI label. For issue resolution, distinguish (a) information recorded internally, (b) information sent to the reporter, and (c) a successful owner-domain outcome; they are separate proofs.

## 31. Retroactive Review Impact

No methodology change and no retroactive queue entry. The audit applies the Orders and Confirmation dispatch/channel boundary without changing those accepted section audits; it treats Chat as distinct from the Messaging/WhatsApp acquisition/capture model and preserves the Finance, Tracking, External Shipping, Products and Inventory ownership boundaries. The synchronized Local Pickup review identifies this as the next section and requires no Local Pickup correction. No Support/Internal Chat-specific Director review existed at synchronization.

## 32. Canonical Section Takeaway

Support currently gives Merchants a scoped Ticket-and-message workflow and internal agents a permissioned Workspace queue, private notes, manual coordination states and auditable resolution/closure. Shared Chat is a separate Merchant/Confirmation operational conversation, not private Support staff chat; an explicit dispatch-aware handoff prevents post-dispatch Order issues from remaining in the wrong channel. The append-only structured resolution episode is a credible future evidence asset, but not yet an intelligence system. Correct the “unread reply” heuristic/label, align corrected replies and resolution explanations with Merchant-visible communication, reconcile API-only assignment with the UI contract, and verify production storage, notifications and service outcomes before stronger reliability or intelligence claims.
