# Confirmation — Section Intelligence

## 1. Audit Metadata

- Audit date: 2026-09-25
- Section: Confirmation
- Intelligence repository: `jetshop7/wossol-brand-intelligence`, branch `main`, commit `789f937634f3ed28f3d0fad29241ad0403352705` at audit start; clean, fetched from `origin` before interpretation.
- Product repository: `jetshop7/wossol-platform`, branch `dev/wossol-integration`, commit `e3912a967827bde06450d3510228e5a5ca9e78a7`; clean.
- Active methodology: `00-methodology/MASTER_INSTRUCTIONS.md`, v1.1; operating protocol: `00-methodology/CODEX_OPERATING_PROTOCOL.md`.
- Evidence boundary: source code/schema and focused passing tests are P1 product evidence; tests are explicitly distinguished as observed executions. UI/system specifications are P3 intent unless corroborated by current source. Competitive framing uses `01-competitive/WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md`; no fresh competitor verification was part of this code audit.
- Scope excludes final external review, production behavior/quality claims, real-world service levels, customer/merchant outcomes, workforce performance claims, and any modification of the Product repository.

## 2. Audit Coverage Map

| Area | Coverage | Basis / boundary |
|---|---|---|
| Merchant Confirmation experience | INSPECTED | Merchant overview/team routes, API client/types, backend endpoints and domain services; chat route exists, shared chat internals not re-audited. |
| Confirmation Worker workflow | INSPECTED | Worker routes, backend queue/order actions, role/assignment authorization and record model. |
| Admin / team-lead operations | INSPECTED | Backend controller/service/oversight, operational queue, workers, overflow, dispatch queues; an Admin navigation/data module retains disconnected dashboard placeholders. |
| Intake eligibility and order provenance | INSPECTED | Order contract fields, order mode provenance, assignment preconditions and Orders review carry-forward. |
| Assignment / capacity / overflow | INSPECTED | Automatic/manual assignment, merchant team config, reservations, temporary support, overflow, worker capacity/availability. |
| Contact attempts / retry / postpone | INSPECTED | Call attempt, postponement and action history; retry schedule, processors, tests. |
| Customer outcomes / cancellation | INSPECTED | Status transitions, structured cancellation, outcomes, inventory release, finance event hooks; Orders cancellation-contract ambiguity remains open. |
| Dispatch and recovery | INSPECTED | Shared dispatch engine integration, eligibility, idempotency, known vs ambiguous errors, verification and safe retry tests. |
| Merchant team control / lifecycle | INSPECTED | Team overview, config, replacement/reorder, support, disable/reactivate, effective dates, authorization/audit. |
| Upsell / order editing | INSPECTED | Pre-dispatch edits, scoped catalog, inventory reservation, order-line and audit/timeline effects. |
| Data / audit / intelligence readiness | INSPECTED | Schema and event/action evidence, selected projections, metric denominator code. No production-quality or intelligence outcome inferred. |
| Documentation intent | PARTIAL | Merchant spec, Worker spec, assignment/call-engine designs, provenance contract read as intent; they do not override P1. Not every historical Confirmation design document was individually reconciled. |
| External telephony, call recordings, AI | NOT RELEVANT to current P1 truth | No current code evidence inspected that establishes automated calling, call recording or AI confirmation; these are not claimed. |
| Live production deployment/configuration | BLOCKED / OUT OF SCOPE | Repository evidence does not establish deployment, actual provider availability, populated teams, or live operating performance. |
| Competitor live-state validation | PARTIAL | Existing Intelligence competitive baseline only; not freshly re-verified against competitor products. |

## 3. Executive Section Truth

Confirmation is a substantial, multi-actor operational workflow that takes eligible Wossol-managed Orders from pending customer contact through staffed assignment, recorded interaction outcomes, follow-up/retry, and a controlled handoff to Dispatch. It combines a merchant-visible service/team relationship with an internal workforce execution system. It is not merely a “call button” or a static queue.

Current implementation includes distinct Merchant, Worker, and Admin/team-lead surfaces; workspace/merchant-scoped team and workload data; capacity-aware assignment and overflow handling; configurable working-time retry scheduling; structured call/postpone/outcome evidence; pre-dispatch customer/order correction and upsell; and guarded dispatch, verification, and retry flows. Product code—not UI copy—sets the boundaries.

The primary current product value is operational execution and controlled handoff, with bounded merchant visibility and some team-composition agency. This audit does not establish that Confirmation is faster, more successful, more profitable, more reliable, or more trusted in production. Confirmation itself is table stakes among the competitors described in the existing competitive baseline; the more distinctive candidate is the combination of merchant-visible team composition, workload responsibility, structured outcome provenance, inventory-aware changes, and adjacent Order/Dispatch controls, subject to validation and operational maturity.

## 4. Scope & Architecture Map

```text
Eligible Order (Orders / Inventory gates)
  → Confirmation workload and assignment (Confirmation)
  → Worker contact and structured outcome (Confirmation)
       ├─ follow-up: no-answer retry / postponed due scheduling
       ├─ terminal: cancelled / wrong number / confirmed
       └─ pre-dispatch correction / scoped upsell
  → confirmed Order enters guarded Dispatch (Dispatch / external provider)
  → shipment creation and unknown-result recovery (Dispatch / Tracking)
```

The Order remains the commercial record. Confirmation owns assignment, contact/outcome evidence, and the confirmation transition; Inventory owns reservation truth; Dispatch owns provider submission and verification; Tracking owns shipment status. A confirmation result is not a delivery result.

Three access surfaces are present: merchant service/team views; assigned Worker execution; and privileged Confirmation operations/oversight. The backend permission and ownership checks are authoritative; frontend route presence alone does not establish authorization. `OrderConfirmationMode` is creation-time responsibility evidence: the active staffed workload path accepts `WOSSOL_MANAGED` Orders whose confirmation is required and workload-eligible. Merchant-preconfirmed Orders are a separate bypass producer and do not become fake Worker assignments or workload outcomes. API-preconfirmed and future-automated enum values do not, by themselves, prove those producers are live.

## 5. Current Capability Inventory

| Capability | Status | Current product truth |
|---|---|---|
| Merchant Confirmation overview | LIVE (source-backed) | Shows service state, today’s scoped workload/outcomes, needs-attention counts, current/pending team summary, recent merchant-safe activity. |
| Merchant team composition | LIVE (source-backed) | Merchant can reorder/replace core and temporary-support members through versioned pending configuration; selected changes are scheduled/activated, not arbitrary per-order routing. |
| Merchant worker disable/history | LIVE (source-backed) | Scoped scheduled disable, core replacement, cancellation/reactivation and history are modeled and audited. |
| Automatic assignment | LIVE (source-backed) | Assignable/actionable Orders are routed to eligible capacity in a merchant team; support/overflow paths address capacity shortages. |
| Manual assignment and transfer | LIVE (source-backed) | Permissioned admin/team-lead control; actions are recorded. Not merchant order assignment. |
| Worker queue and interaction | LIVE (source-backed) | Worker reads their active queue and may act only on assigned Orders; historical handlers have bounded read access. |
| Contact result recording | LIVE (source-backed) | Confirm, no-answer, wrong-number, postpone, cancellation, notes and append-only interaction/action history. Does not establish automated telephony. |
| Retry and postponed scheduling | LIVE (source-backed) | Workspace calendar/working hours, bounded no-answer retry and due processing; not a call execution engine. |
| Merchant-safe attention and activity | LIVE (source-backed) | Scoped projection, limited reasons and merchant-safe events; not a full internal trace or causal analytics system. |
| Operational oversight / alerts / activity | LIVE (source-backed) | Backend oversight supports bounded dashboard, alert lifecycle and activity queries. |
| Admin Confirmation dashboard placeholder module | PARTIAL / disconnected placeholder | `admin/confirmation/confirmation-data.ts` still has “Not connected” metrics and TODO API binding. Other queue/order/worker/oversight routes use real endpoints. Do not describe all Admin surfaces as disconnected or all dashboard cards as live. |
| Dispatch handoff / verification | LIVE (source-backed) | Confirmation routes into shared Dispatch with controlled eligibility, duplicate protection, verification-required state and limited retry. Provider performance is unproven. |
| Confirmation upsell | LIVE (source-backed) | Scoped pre-dispatch lines/value, inventory reservation and commercial OrderItem changes; not evidence of upsell acceptance or revenue lift. |
| Voice/AI automation | NOT ESTABLISHED | Timer/retry “Call Engine” is scheduling logic. Inspected code/spec does not establish automated dialling, speech intelligence, call recordings, or AI replacing human workers. |
| Merchant allocation of individual orders / call policy | NOT ESTABLISHED / absent from inspected merchant contract | Merchant team controls do not demonstrate direct order routing or mutable retry policy control. |

## 6. Workflow & Lifecycle

1. **Eligibility:** eligible Orders enter `PENDING_CONFIRMATION`; assignment additionally checks Wossol-managed provenance, `confirmationRequired`, `confirmationWorkloadEligible`, an assignable status, no existing active assignment, and call-engine actionability. Inventory/Orders statuses such as waiting stock or blocked customer remain upstream eligibility decisions, not confirmation outcomes.
2. **Routing:** automatic assignment uses the active merchant team configuration and worker/capacity eligibility, with reservations, temporary support, and overflow evidence. Privileged staff can assign, reassign, unassign, or retry assignment. These are distinct assignment types and carry append-only assignment/action/timeline evidence.
3. **Contact:** a Worker receives only currently assigned work and records an outcome. A no-answer result schedules bounded follow-up; postponement stores a due record; a terminal outcome closes/releases the assignment. Worker actions are validated against role, assignment, status transition, and pre-dispatch guards.
4. **Resolution:** confirmation requires an eligible payment choice under the Order’s payment-policy snapshot. Cancellation and wrong-number release reservations; cancellation uses structured reason taxonomy. Customer/address/destination/shipment prep/pricing/notes edits and upsell are constrained to the pre-dispatch window and record actor/history evidence.
5. **Dispatch:** a confirmed Order is eligible for explicit controlled dispatch, not proof that a shipment was accepted or delivered. Known provider rejection may be retried when safe; timeout/ambiguous provider result requires verification. Tests cover duplicate prevention, provider lookup outcomes, stable reference reuse and Test Order rejection.
6. **Recovery / ongoing operations:** retry and postponed processors, overflow recovery, team activation and alert reconciliation are separate lifecycle work. Current source includes bounded retry/error scheduling; test evidence validates selected paths, not every deployed background configuration.

## 7. Value Recipient Map

| Recipient | Value provided by current behavior | Limit |
|---|---|---|
| Merchant | Receives a summarized view of workload/outcomes/attention and team membership; can make constrained team changes and open Orders needing action. | No raw internal queue control, direct order routing, or proof of service quality. Some Admin-side dashboard elements remain placeholders; merchant value depends on populated and current source data. |
| Confirmation Worker | Focused assigned queue, operational order workspace, structured result/follow-up, correction/upsell tools. | Acts only within authorized assignment and pre-dispatch scope; no cross-team or general merchant account authority. |
| Team Lead / Admin | Assignment override, capacity/availability, overflow recovery, oversight/alerts/activity and dispatch exception paths. | Privileged control increases policy and audit burden; not a merchant capability. |
| Customer | Potentially gets a human contact attempt, corrected order details and explicit customer decision recorded. | No measured contact success, consent/quality, delivery, or customer satisfaction evidence. |
| Wossol operations | Structured workload routing, actionable retry timing, durable outcome and exception evidence. | Persisted events and dashboards are operational records, not automatically reliable predictive intelligence. |

## 8. Control & Merchant Agency

Control depth is differentiated by actor. Merchant agency is meaningful but bounded: authorized merchant actors can inspect the serving team, reorder or replace team members, change temporary support, schedule some worker disables, cancel eligible pending changes, and reactivate workers. Core-team changes require replacement and lifecycle scheduling; pending configs are versioned and activation can fail/block. The backend derives merchant/workspace scope and returns opaque worker action references rather than raw internal configuration IDs.

The merchant does not thereby choose which individual worker receives each Order, change the Confirmation retry calendar, override an outcome, operate the internal queue, or control provider shipment state. Team-composition control must not be described as full operational control. Workers can operate assigned Orders; Admin/team-lead control covers the shared operational mechanics. No evidence here establishes an end-user choice of human vs automated contact mode.

## 9. Transparency & Trust

Merchant overview uses business-facing status and aggregates, keeps internal dispatch/overflow mechanics off the merchant-facing summary, and reports unresolved and excluded-closed cohort values separately. Recent activity is filtered to merchant-safe Confirmation timeline events. Team APIs expose scoped/opaque member references and calculate management permissions rather than hardcoding them in the UI. Action, assignment, attempt, config, and alert evidence is attributable and many critical changes are auditable.

Limits: the overview is not a full per-order operational trace; a “service active” label is not independently verified service uptime; counts/rates are code-defined projections, not audited production reporting. UI and backend permission surface coverage should be rechecked as routes evolve, especially the older disconnected Admin dashboard module. A passing authorization test is not a security certification.

## 10. Merchant Value Extraction

The defensible merchant promise is reduced coordination burden around a staffed confirmation service: see whether the workflow reports attention, see the team assigned to the merchant, make bounded team-composition changes, and correct relevant order details before dispatch when authorized. It may prevent some operational errors through scoped edits, inventory reservations, structured cancellation, bounded retries, and explicit dispatch verification. It does not guarantee prevention, speed, contact success, conversion, or shipment outcomes.

The user-visible order workspace is more than a call-result form: it joins customer/order details, product inspection, payment choice, destination and shipment preparation, pricing/notes, and a scoped upsell into a pre-dispatch operation. This compound surface creates execution value; it does not yet establish autonomous sales intelligence or a merchant decision engine.

## 11. Feature Clusters

1. **Staffed execution:** actor-scoped queues + order workspace + explicit outcomes + audit/timeline.
2. **Adaptive capacity:** worker availability/capacity + merchant team configs/reservations + temporary support + overflow recovery.
3. **Time-based contact responsibility:** no-answer retries + postponed records + workspace working hours + due/acted timestamps.
4. **Commercial handoff:** payment eligibility + confirmation state + guarded dispatch + verification/retry + Tracking synchronization boundary.
5. **Merchant service relationship:** overview + team preview/management + attention projection + merchant-safe activity/chat adjacency.
6. **Order improvement during contact:** scoped customer/destination/pricing correction + same-store catalog assistance + upsell + inventory reservations + commercial history.

The value comes from interactions across Confirmation, Orders, Inventory, Permissions, Finance, Dispatch, Tracking, Customers, and Chat—not from a single isolated feature.

## 12. Merchant Journey / Old Way vs Wossol Way

| Journey moment | Common manual substitute | Current Wossol evidence | Residual risk |
|---|---|---|---|
| Decide who handles new work | Shared spreadsheet or ad hoc queue | Capacity- and merchant-team-aware assignment plus overflow | Live staffing/capacity quality unknown. |
| Reach customer and record result | Personal call history / informal notes | Structured interaction outcomes, action history, retry/postpone schedule | No evidence of automated call connection or recording. |
| Resolve an incorrect order | Message back and forth across tools | Authorized pre-dispatch order/customer correction and audit trail | Change is limited to permitted fields/status window. |
| Maintain a service team | Escalate a staffing request outside the product | Merchant-visible team, replacement/reorder and scheduled changes | Not per-order assignment; activation may be blocked. |
| Recover when provider call is unclear | Retry and hope / duplicate shipment risk | Provider verification gate and safe retry eligibility | External provider correctness/availability unknown. |

This comparison describes workflow structure, not proven time savings or better outcomes.

## 13. Hidden / Non-Obvious Advantages

- Confirmation records when retry was scheduled, became due, and was acted on as separate facts; mutable current-state helper fields do not replace action/attempt history (P1 code plus P3 provenance contract).
- Assignment responsibility, actual interaction actor, terminal order result, and provider dispatch state are separate concepts. This can support later accountable operations if evidence remains complete and semantics stay stable.
- Merchant team configuration uses scheduled, versioned lifecycle and capacity commitments rather than equating a visible roster with immediate global staffing changes.
- Upsell lines are scoped to the Order’s merchant/workspace/store, mapped to commercial OrderItems and inventory reservations. This joins revenue correction to stock truth instead of adding a detached “sales suggestion.”
- Ambiguous dispatch does not silently become failure/retry: verification precedes unsafe resubmission, reducing duplicate-work risk by design in code; production prevention rate is not known.

## 14. Data & Intelligence Assets

Current sources include Order confirmation provenance/status/retry state; assignment records/types/actors; worker state and capacity; merchant team configs, version/source/recommendation/allocation; call attempts/results/times/notes; postponed records; action logs with old/new values; structured cancellation reason and outcome-reason events; overflow status/reason/attempts; dispatch attempts/error classification/verification; upsell rows and actor; alert lifecycle; audit events and merchant-safe timeline events.

Potential later analyses: workload-to-outcome by operational cohort; retry timing and contact resolution; overflow/capacity prediction; assignment continuity; structured cancellation reason patterns; correction/upsell frequency; known-vs-ambiguous dispatch recovery. These require definitions, data completeness checks, privacy review, adequate samples, and outcome attribution. Persisted data is not itself intelligence, a recommendation, or a validated moat. No cross-merchant customer evidence should be exposed as Confirmation analytics.

## 15. Cross-Section Compound Advantages

- Orders establishes responsibility provenance and dispatch eligibility; Confirmation must not absorb upstream waiting-stock/blocked-customer states as contact failures.
- Inventory reservations constrain both Order intake/recovery and Confirmation upsell/cancellation, tying a contact workflow to physical stock truth.
- Dispatch and Tracking separate submission, provider identity, unknown-result verification, and shipment state from customer confirmation.
- Finance receives operational-fee events at some terminal actions; fee correctness, merchant economics and profitability were not fully re-audited here.
- Customer Intelligence surfaces bounded customer history within the authorized order/merchant context in the current workspace; no network-wide risk decision is claimed.
- Chat and oversight surface merchant communication/attention, but internal event history and merchant-safe messaging have different privacy boundaries.

The Orders review (`04-review-history/ORDERS_REVIEW_2026-09-25.md`) leaves open whether Final V1’s “cancel only before processing starts” language matches P1 ordinary merchant cancellation of `PENDING_CONFIRMATION`, `WAITING_FOR_STOCK`, and separately `BLOCKED_CUSTOMER` Orders, with provider shipment ID/code required to be absent. This is the Orders merchant-cancel path, not the same actor/action as a Confirmation Worker recording a customer cancellation outcome. This Confirmation audit does not resolve that product-contract question or reinterpret the P3 phrase as equivalent; retain both P3 and P1 rules as an unresolved cross-section boundary.

## 16. Competitive Analysis

The existing competitive baseline lists confirmation workforce/automation as common direct-competitor territory (including MDM, COD Network, COD Mastery, CODZOSS, Fufills, and Delivered.ma). Presence of confirmation, workers, retries, or upsell is not a differentiation claim by itself. The baseline’s working position favors human confirmation with AI assisting staff rather than replacing the human; that is strategic direction, not evidence of an implemented AI assistant.

Wossol’s plausible relative strength to validate is connected operational control: merchant-scoped team visibility/changes coupled with workload allocation, structured outcomes, inventory-aware correction, and guarded dispatch recovery. Weakness candidates: complexity and operational staffing dependency; thin confidence without populated teams/data; an inconsistent Admin shell; no demonstrated voice automation/AI; and unverified workflow/quality metrics. Competitor feature/current-market statements were not refreshed in this audit and remain secondary, time-bounded comparison rather than current fact.

## 17. Marketing Intelligence

**Potentially safe, after implementation/demo confirmation:** “A managed confirmation workflow that keeps customer decisions, follow-up, and dispatch handoff in one accountable Order process.” “See the team serving your Confirmation workload and request controlled team changes.” These describe source-backed capability but should still be checked against availability and exact audience plan.

**Do not claim:** AI calls or fully automated confirmation; guaranteed confirmation/conversion; “best” contact rates; zero missed/duplicate dispatch; real-time assignment perfection; merchant control over every worker/order; delivery success; fraud prevention; predictive customer risk; cost/profit improvement; or universal/global availability.

## 18. Surprise Findings

- “Call Engine” is chiefly a scheduling and readiness engine in inspected P1, not proof of a telephony system: it schedules no-answer retries and postponed due work. Human Worker outcome entry is the current evidenced execution boundary.
- The merchant relationship layer is unusually developed for a feature category often sold as a simple operations queue: roster previews, pending changes, scoped replacement, worker lifecycle history and a measured daily summary are present in code.
- The Admin implementation is mixed, not uniformly mature or uniformly a stub: operational queues and oversight endpoints coexist with legacy disconnected dashboard placeholders.
- Outcome metrics distinguish eligible cohort, unresolved, closed/excluded and completed decisions, improving semantic honesty versus a naked percentage; this still does not prove statistical stability or production quality.

## 19. Potential Category Reframes

Research directions, not finalized positioning: (1) “accountable customer-decision operations” rather than generic confirmation calling; (2) “human-led commercial handoff” spanning customer clarification, stock-aware correction, and dispatch readiness; (3) “merchant-visible managed operations” emphasizing team transparency and bounded agency. Validate customer vocabulary, unmet need, willingness to pay, service execution, and competitive uniqueness before adopting.

## 20. Brand Evidence

Supported product traits: operationally accountable; human-executed in the inspected flow; connected across Orders, stock, and dispatch boundaries; gives merchants bounded visibility/agency; records decisions and exception recovery. Not yet supported as brand promises: intelligent/AI-led, effortless, fastest, conversion-maximizing, always-on, globally scalable, risk-free, or proven to improve outcomes. The experience is part software control plane, part managed operational service; do not obscure the staffing/service dependency.

## 21. Weaknesses / Risks / Gaps

- Contract ambiguity carried from Orders: P3 “before processing starts” vs P1 ordinary merchant cancellation of `PENDING_CONFIRMATION` / `WAITING_FOR_STOCK` / `BLOCKED_CUSTOMER`, plus absent provider shipment ID/code, remains unresolved; both resolutions must remain open. Do not conflate the Orders merchant-cancel operation with a Confirmation Worker recording a customer cancellation outcome, or claim either rule silently narrows the other.
- Admin dashboard placeholders coexist with operational endpoints; distinguish connected working routes from disconnected card definitions and remove stale placeholders as a product task.
- Confirmation depends on a staffed workforce, capacity, assignment quality and actual contact execution; code cannot prove those operational prerequisites are met.
- Current “today” metrics are bounded projections. Do not promote them into performance guarantees or cross-merchant benchmarks without cohort, denominator, completeness and privacy validation.
- Merchant controls team composition, not all routing or call policy; avoid implying broad self-service operational control.
- Retry caps/calendar assumptions are implemented but product policy (including consent, local communications regulation, channel choice and customer preference) was not legally or operationally validated in this audit.
- No inspected P1 proof of dialer/telephony, recordings, quality scoring or AI assistance. The name “Call Engine” risks overstatement.
- Focused test scope passed, but no full Confirmation suite, UI end-to-end, database integration suite, deployment, or live merchant/provider validation was run.

## 22. Future Strategic Potential

Potential additions after operating evidence: contact-window preference/consent-aware orchestration; human agent assistance (summarization, reason coding, coaching) evaluated against worker outcomes; confidence-aware capacity planning; cohort-qualified merchant insights; explainable reason/timing recommendations; merchant-controlled service options. Such work should preserve actor, schedule, assignment, outcome and source provenance; avoid collecting call recordings or sensitive data without explicit legal, consent and retention design. AI voice replacement is not implied or recommended by this audit.

## 23. Claim Safety

| Claim | Status | Safe formulation / boundary |
|---|---|---|
| Wossol provides Confirmation queues and assigned-work execution | SUPPORTED | Say the platform supports an assigned, human-workflow Confirmation process, subject to service configuration. |
| Merchant can see/manage their Confirmation team | QUALIFIED | Can view the merchant-scoped team and make allowed, scheduled composition changes; not per-order worker routing. |
| Confirmation automatically calls customers | UNSUPPORTED | Inspected retry scheduler does not establish an automated dialer. |
| Confirmation improves conversion/delivery/profit | UNSUPPORTED | No measured causal outcome evidence in scope. |
| Confirmation rate is a reliable prediction | UNSUPPORTED | Current daily scoped rate is retrospective and sample-dependent. |
| Dispatch retry prevents duplicate shipments | QUALIFIED | Code enforces eligibility/verification boundaries; no absolute or production guarantee. |
| Confirmation covers cancellation only before processing begins | UNRESOLVED CONTRACT | Preserve Final V1 intent and exact P1 behavior separately until Product authority resolves alignment. |
| Confirmation AI or predictive risk exists | UNSUPPORTED | No current implementation evidence. |

## 24. Commercial Magnitude

Confirmation is high operational consequence because a missed/incorrect customer decision may waste shipping/stock effort, while proper clarification can make the commercial handoff actionable. The software also carries meaningful workforce and customer-data handling cost. Revenue lift, cost reduction, order conversion, failure reduction, retention, time savings and unit economics are UNKNOWN from inspected repositories; quantify via instrumented cohorts and actual operations before positioning.

## 25. Strategic Classification

- Category role: operational capability / managed-service execution, with merchant-control and data-foundation potential.
- Competitive role: expected/table stakes as a category feature; potential compound differentiator is scoped merchant agency + accountable cross-domain handoff, not confirmation alone.
- Launch relevance: likely operationally core wherever Wossol-managed Orders require customer confirmation; runtime readiness and workforce availability remain external conditions.
- Moat: not established. Durable value would require proprietary, permissioned longitudinal outcomes and learning loops validated over time.
- Build posture: stabilize service reliability, policy clarity, Admin surface consistency, exception handling and transparent metrics before investing in AI or category claims.

## 26. Action Register

| Priority / type | Action | Reason / completion condition |
|---|---|---|
| MUST FIX — product governance | Resolve Orders/Confirmation cancellation contract boundary with both P3 and P1 predicates explicit. | Product authority must clarify/amend P3 or bring P1 into alignment; Intelligence should not decide equivalence. |
| MUST FIX — product surface | Reconcile the disconnected Admin Confirmation dashboard cards/TODO with actual backend-connected routes. | One trustworthy map of which operational/admin dashboard data is live; remove stale “Not connected” placeholders when wired. |
| MUST MATCH — service operation | Document enabled service channels, working-hour/contact policy, consent/legal basis, retention, and staffed coverage per workspace. | Code scheduling defaults alone do not establish an approved customer-contact policy or live service. |
| WORTH ADOPTING — observability | Measure end-to-end cohort completeness, attempts-to-decision, capacity/overflow delays, retry exhaustion and dispatch verification, segmented only with adequate samples. | Convert records to auditable operations insight without claiming causality. |
| WORTH ADOPTING — merchant agency | Make pending-team activation/failure, effective time, and limits consistently understandable across all merchant permissions. | Existing state machine can block/fail; avoid premature “change active” message. |
| DO NOT COPY | Do not add AI voice solely because competitors list it. | Competitive baseline prefers human execution with assistive AI; validate policy and customer value first. |
| POTENTIAL MOAT | Evaluate permission-safe longitudinal contact/decision/outcome data and human-feedback loop. | Requires data-quality, privacy and sample-size gates; not a present moat. |

## 27. Evidence Register

All P1 citations below refer to Product commit `e3912a967827bde06450d3510228e5a5ca9e78a7` unless noted. P1 means the source was directly inspected. P2 identifies executed or inspected tests, never substitutes for production proof. P3 identifies intent/contract sources only.

| ID | Tier | Evidence | Supports / limitation |
|---|---|---|---|
| EV-CONF-001 | P1 | `apps/backend/prisma/schema.prisma`: confirmation enums and Order mode/provenance/status fields; assignments, worker state, team configs/members, attempts, postponed records, action logs, dispatch attempts, alerts, upsell. | Durable domain structure, not proof data is populated in production. |
| EV-CONF-002 | P1 | `apps/backend/src/modules/confirmation/confirmation.controller.ts`, `confirmation.module.ts`, `confirmation-order-workspace.guard.ts` | Route split, guards and module wiring; full runtime policy still depends on permission configuration. |
| EV-CONF-003 | P1 | `confirmation.service.ts` — order action transitions/access, assignment, queue, merchant overview/team, team lifecycle, no-answer/postpone handlers. | Current business logic and boundaries. |
| EV-CONF-004 | P1 | `confirmation-call-engine-policy.ts`, `confirmation-call-engine.processor.ts`, `confirmation-lifecycle.processor.ts` | Working-time scheduling and background due processing; not telephony/dialing. |
| EV-CONF-005 | P1 | `confirmation-dispatch.service.ts`, `confirmation-dispatch-errors.ts` | Dispatch eligibility, duplicate guards, verification and retry classification; no provider uptime claim. |
| EV-CONF-006 | P1 | `confirmation-oversight.service.ts`, `confirmation-oversight.controller.ts`, `confirmation-oversight.types.ts` | Operational metrics/alerts/activity projections and access-filter patterns. |
| EV-CONF-007 | P1 | `apps/frontend/src/app/merchant/confirmation/page.tsx`, `team/page.tsx`, `confirmation-data.ts`, chat route | Merchant overview/team UI and API surface. Chat system internals not audited. |
| EV-CONF-008 | P1 | `apps/frontend/src/app/confirmation-worker/**` | Worker execution routes/UI and focused source tests. Not a live deployment check. |
| EV-CONF-009 | P1 | `apps/frontend/src/app/admin/confirmation/**` including `confirmation-data.ts`, queue/order/worker/dispatch/oversight pages | Mixed maturity: connected operational pages plus disconnected dashboard placeholders. |
| EV-CONF-010 | P1 | `confirmation.service.ts` updateUpsell; `ConfirmationUpsellItem` schema; product/inventory calls | Scoped pre-dispatch commercial edits and reservation linkage; no sales lift evidence. |
| EV-CONF-011 | P2 — observed pass | Backend focused Node test run: `confirmation-call-engine-policy.spec.ts`, `confirmation-lifecycle.processor.spec.ts`, `confirmation-domain-foundation.spec.ts`, `confirmation-dispatch.service.spec.ts`, `confirmation-c02-acceptance.spec.ts`, `confirmation-oversight.service.spec.ts`; **102 tests passed, 0 failed**. | Confirms selected unit/source contracts at this commit only. Expected warning/error logs simulated provider sync and DB failure. Not full suite or integration proof. |
| EV-CONF-012 | P2 — inspected source only | Relevant Confirmation UI/workflow, worker, controller-scope and other specs not all executed as a set in this audit. | Test source can evidence intended assertions; do not record as observed pass. |
| EV-CONF-013 | P3 | `docs/ui/merchant/Merchant_Confirmation_UI_Function.md`; `docs/ui/admin/confirmation/CONFIRMATION_WORKER_UI_SPEC.md`; Confirmation assignment and Call Engine system-design docs. | Approved/detailed intent, some of which may be stale; current P1 controls claims. |
| EV-CONF-014 | P3 / governed context | `docs/wossol-system-design/01-system-design/core-systems/CONFIRMATION_MODE_PROVENANCE_P0_08.md`; `04-review-history/ORDERS_REVIEW_2026-09-25.md`. | Responsibility-provenance rules and open cancellation contract issue; Order review issue is not resolved here. |
| EV-CONF-015 | P3 — strategic baseline | `01-competitive/WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md`, competitor Confirmation sections. | Existing baseline only; not current competitor re-verification. |

## 28. Contradictions & Uncertainty

1. **Cancellation contract — OPEN, cross-section.** Orders Final V1 says merchant cancellation only before “processing starts”; Orders review records P1 ordinary merchant-cancel gates of `PENDING_CONFIRMATION`, `WAITING_FOR_STOCK`, or separately `BLOCKED_CUSTOMER`, plus provider shipment ID/code absent, and requires both possible resolutions to remain open. This audit does not reconcile or reinterpret them. A Confirmation Worker’s customer-cancellation outcome is a separate Confirmation lifecycle action; it does not settle the merchant cancellation contract.
2. **Call Engine label — terminology risk.** P1 source schedules readiness/retries and no-answer/postpone due events; P3 system document discusses call attempts/VoIP preparation. No proof that current system places a call. Treat “call engine” as workflow scheduling unless actual dialer source is established.
3. **Admin surface — mixed live state.** Backend oversight/queue endpoints and multiple UI routes are connected, while the legacy dashboard card module retains hard-coded placeholder data and TODO. Scope of the main visible landing dashboard at runtime was not browser-tested.
4. **Mode enum — producer uncertainty.** P1 schema includes API-preconfirmed/future-automated values; P0-08 says reserved modes are inactive. Enum existence is not a live producer.
5. **Metrics — operational, not validated insight.** Source describes denominators and exclusions, but real sample size, ingestion completeness, time-zone behavior in production, and metric usefulness were not independently validated.
6. **Environment uncertainty.** Clean source state and tests say nothing about current configuration, provider integration, staffing, or deployed version.

## 29. Open Questions

- Which interpretation of Orders’ Final V1 cancellation rule is authoritative: amend/clarify the contract to match P1, or bring P1 behavior into alignment? Both remain viable until Product authority decides.
- Which Admin Confirmation dashboard is the intended operator entry point, and are all “Not connected” cards obsolete or awaiting integration?
- What real customer-contact channels operate in each workspace (human telephone, messaging, etc.), and what consent/contact-hour/retention policy governs them?
- How are staffed coverage, worker capacity and overflow service levels contracted, monitored and communicated to merchants?
- Which metrics are production-quality, with what cohort threshold, completeness threshold, and suppression/privacy policy?
- Are contact outcome notes and cancellation reason taxonomies consistently used by workers, and is data quality monitored?
- Is any external dialer/call provider integration implemented outside the inspected Confirmation module? No claim is made absent direct P1 evidence.

## 30. Methodology Learnings

- Treat system names (“Call Engine”) as hypotheses; inspect effects to distinguish scheduling, human action and telephony execution.
- Trace all three parties—merchant, worker and privileged operator—to avoid conflating visibility with control.
- Distinguish connected backend endpoints, specific connected pages and stale disconnected dashboard modules rather than assigning one maturity label to a whole section.
- Carry reviewer-identified cross-section contract issues forward verbatim in substance; no methodology change is proposed.
- Separate a selected test set observed passing from inspected but unrun test sources and from production operation.

## 31. Retroactive Review Impact

No methodology change or retroactive audit is required. Carry the Orders cancellation-contract issue into Tracking/Delivery and related future sections wherever cancellation-after-processing/dispatch authority is discussed. Do not mutate or reinterpret the Orders review record in this audit.

## 32. Canonical Section Takeaway

Wossol Confirmation is currently evidenced as a human-worked, capacity-routed and auditable customer-decision workflow that gives merchants bounded visibility and team-composition agency, then hands confirmed Orders into guarded Dispatch. Its strongest candidate value lies in the connected operational control and evidence trail—not in the category feature of confirmation itself. Telephony automation, AI, outcome lift, production reliability, broad merchant control and a durable data moat remain unproven; the cancellation contract boundary remains explicitly unresolved.
