# Tracking / Delivery — Section Intelligence

## 1. Audit Metadata

- **Audit date:** 2026-09-25.
- **Methodology:** `00-methodology/MASTER_INSTRUCTIONS.md` v1.1; current operating protocol.
- **Competitive reference:** `01-competitive/WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md` v1 (2026-09-09).
- **Intelligence source:** `jetshop7/wossol-brand-intelligence`, `main`, `db0b100a750ebc183e76e58e1b007c60595e66a7`, clean and synchronized before inspection.
- **Product source:** `jetshop7/wossol-platform`, `dev/wossol-integration`, `76cb3db4116e52df2920d90b863d862ec772429c` (`annalytic`, 2026-09-25). At final source-state verification, `origin/dev/wossol-integration` matched HEAD and the working tree was clean. The inspected Tracking/Delivery paths were unchanged from the source revision read during evidence collection (`e3912a967827bde06450d3510228e5a5ca9e78a7`); no Product files were modified.
- **Scope:** post-dispatch delivery operations, provider status ingestion/mapping, Tracking workers and alerts, Merchant Order Detail delivery projection, public customer tracking, shipment recovery/recreation, and material Finance/Customers handoffs. External inbound purchasing/shipping and delivery-pricing configuration are not treated as Tracking-owned capabilities.
- **Evidence basis:** P1 code/schema, P2 focused tests and typechecks, P3 current and historical Tracking specifications explicitly distinguished, and the competitive master. No live provider session, production database, deployed edge configuration, or fresh browser acceptance was available.

## 2. Audit Coverage Map

| Surface | Coverage | Evidence |
|---|---|---|
| Internal Tracking UI | dashboard, queue, shipment detail, worker portal, alerts, worker administration | EV-TRACK-001–002 |
| API and permissions | authenticated Admin Workspace scope, assigned-worker/team access, actions and token operations | EV-TRACK-003 |
| Provider status ingestion | Accurate/Mayar adapter, exact-reference reads, status/transaction polling, leases, retries and capability failure handling | EV-TRACK-004–005 |
| Lifecycle and evidence | mapping, stale/replay guards, Order transitions, provider history, events, audits, finance/customer side effects | EV-TRACK-006–007 |
| Handling and Alerts | assignment, structured handling, checkpoints, alert triggers/transitions and recovery states | EV-TRACK-008 |
| Shipment recovery | historical/recreated Shipment lineage, constrained recreation, exceptional provider delete | EV-TRACK-009 |
| Merchant surface | Orders-owned, read-only delivery/Tracking timeline and explicit public-link copy | EV-TRACK-010 |
| Public customer tracking | token issuance/storage/retrieval, redacted DTO, status/timeline mapping and Arabic-first route | EV-TRACK-011–012 |
| Contact/consent boundary | direct operator phone/WhatsApp links, manual handling records, Customer consent call-site search | EV-TRACK-013 |
| Specifications | Tracking architecture, Merchant Tracking and Public Tracking V1 masters, historical UI/worker/dashboard/mapping docs | EV-TRACK-014 |
| Verification | focused backend suite, relevant source specs, frontend/backend typechecks; one stale frontend source test fails | EV-TRACK-015 |
| Deployment/provider reality | live provider credentials and API permissions, edge rate-limiting, production scheduler health and delivery outcomes | NOT VERIFIED |
| Other carrier integrations | no multi-carrier routing established in the inspected Tracking implementation; broader repository/provider coverage was not exhaustively proven | PARTIALLY INSPECTED / NOT VERIFIED |

## 3. Executive Section Truth

Tracking is a real post-dispatch operational layer, not just a status badge. It polls a configured Accurate/Mayar delivery integration, validates provider/shipment identity, deduplicates and persists status history, maps provider states into Wossol lifecycle/operational states, assigns internal workers, records structured follow-up, opens/resolves selected exception Alerts, and exposes narrower Merchant and customer projections. Recovery includes constrained shipment recreation and a separately permissioned provider-shipment deletion path.

The strongest current quality is separation of truth and authority: the provider observation remains evidence, Orders owns the official Order lifecycle, Tracking owns internal follow-up, and Merchants receive a read-only, safer projection. A customer can read a bearer-token tracking page after verified dispatch; the page does not synchronously query the carrier.

This is not yet a proven multi-carrier network, delivery guarantee, complete SLA/quality analytics product, or automated customer communication system. Important unresolved consistency issues remain between current P1 and older P3 Tracking documents: provider API/cadence assumptions, promised Alert trigger breadth, dashboard analytics, and whether actor IDs belong in the Merchant timeline. The latest Public Tracking V1 master explicitly supersedes earlier public-search/contact drafts and is the applicable public contract. The related Customers review's consent warning also applies here: a WhatsApp shortcut and a recorded handling outcome do not establish consent enforcement or a confirmed message delivery receipt.

## 4. Scope & Architecture Map

Tracking starts after a current provider Shipment is linked and verified. The operating graph is: Confirmation/approved dispatch → first-class Wossol `Shipment` → Accurate/Mayar provider observation → `DeliveryTrackingStatusHistory` and normalized status → `Order` lifecycle/timeline, Tracking operational record/Alert, Finance fee assessments and Customers delivery-outcome recalculation → Merchant and public customer projections.

Orders remains the owner of Order identity, authoritative Wossol lifecycle, and the merchant detail composition. The provider is the external source for its own shipment status. Tracking owns worker assignment, manual follow-up, handling history, checkpoints, Alert objects and mapping. External Integrations owns provider transport/parsing; Shipments owns Wossol Shipment identity/recreation; Finance owns fee/ledger effects; Customers owns customer reputation projection. These boundaries are material: Tracking action completion does not itself set a provider state or change the Order state.

Public customer tracking is a distinct unauthenticated, bearer-authorized read surface. Internal Admin/Worker APIs are authenticated and Workspace/assignment scoped. Merchant Tracking is embedded in authorized Merchant Order Detail, not an independent Tracking dashboard.

## 5. Current Capability Inventory

| Capability | Status | Current behavior |
|---|---|---|
| Internal Tracking dashboard/queue | LIVE, operational scope | Workspace-scoped counters, searchable/paginated queue, assigned-work and team scopes; not deep delivery analytics |
| Worker/Team Lead operations | LIVE | balanced initial assignment, sticky ownership, availability/worker management, assigned handling and attributed supervisor intervention |
| Provider status ingestion | LIVE in code; provider access NOT VERIFIED | scheduled priority polling through the Accurate/Mayar adapter, transaction-feed polling when authorized, durable synchronization coordination, retries and identity checks |
| Status mapping and Order handoff | LIVE | provider codes map to Wossol status, operational group, safe labels, history/timeline/audit and selected fee/customer side effects; unknown codes do not invent a trusted mapping |
| Structured handling and checkpoints | LIVE | backend policy controls outcome, method, note audience, cancellation reason, follow-up and append-only actor evidence; due checkpoints reopen work |
| Tracking Alerts | LIVE, trigger coverage PARTIAL | durable, deduplicated Alerts with acknowledgement/escalate/resolve/reopen history; current automatic triggers are narrower than the P3 design list |
| Merchant delivery/Tracking view | LIVE | Orders-owned read-only normalized delivery facts, bounded safe history and explicit copy link; no browser provider call or Merchant mutation |
| Customer Public Tracking | LIVE, deployment-dependent | 32-random-byte bearer, hash lookup plus encrypted backend envelope, safe current DTO, manual rotate/revoke and public read-only page |
| Shipment recreation | LIVE, tightly gated | selected provider recovery states only; explicit continued customer intent, reason, interaction, idempotency, one replacement generation and lineage |
| Provider shipment deletion | LIVE, exceptional/internal | exact permission, literal DELETE confirmation, reason/note and live provider PKR recheck; only confirmed deletion updates Order and closes current Tracking work |
| Automated WhatsApp/SMS or consent-driven messaging | NOT FOUND AFTER SEARCH | direct operator shortcuts and manual records exist; no Customer consent ledger integration or automated send/receipt established |
| Multi-carrier routing and merchant delivery analytics | NOT ESTABLISHED | inspected Tracking path is Accurate/Mayar-specific; P1 dashboard lacks the historical P3 quality/performance metrics/charts |

## 6. Workflow & Lifecycle

1. Verified dispatch links a provider Shipment to the Wossol Order. Public Tracking identity is activated only in the verified-linkage/`SENT_TO_DELIVERY` path; Test Orders do not become publicly trackable.
2. Background services poll due provider state and, separately, provider shipment transactions. A provider snapshot is checked against stored provider reference/ID and current Shipment/Workspace, then deduplicated and guarded against stale state before persistence.
3. A mapped snapshot appends provider history and audit evidence, updates current Shipment/provider fields, can move the canonical Order through an allowed lifecycle transition, and enters or updates the Tracking operational record. Unknown codes preserve evidence and require review rather than guessing a transition.
4. Actionable exceptions are assigned to an eligible internal Tracking Worker using a balanced initial workload rule. Ownership stays sticky; a Team Lead may intervene without impersonating or reassigning the original Worker.
5. Workers use policy-bounded structured outcomes, optional audience-specific note, interaction method and explicit checkpoint. Completion is idempotent/append-only; it changes Tracking handling state, not provider or Order state. A due checkpoint can reopen the handling record for renewed work.
6. Terminal provider evidence closes active handling. Provider/handling history remains distinct from the Merchant-safe Order timeline and the narrower customer bearer-page timeline.
7. Return-processing `RTS` is recorded without forcing a canonical Order cancellation/return transition. `RJCT` may represent delivery cancellation; `RCV`, `OTR` or `RTRN` can independently establish returned state under guarded lifecycle rules.
8. If a qualifying recovery Shipment is recreated, the source remains historical and the replacement has a linked lineage and bounded dispatch path. Provider deletion is a separate, high-sensitivity, live-state-verified exceptional operation.

## 7. Value Recipient Map

| Recipient | Current value |
|---|---|
| Tracking Worker | prioritized assigned queue, contact context, structured next actions and follow-up checkpoints |
| Tracking Team Lead / operations manager | Workspace team visibility, exceptions, workload/capacity indicators, Alert lifecycle and audited intervention |
| Merchant operator | readable persisted delivery context, safe Tracking history, delivery-link copy and selected product-level refusal evidence |
| Customer | Arabic-first, read-only order progress page and conditionally visible current courier contact |
| Confirmation/Orders | verified dispatch handoff and normalized delivery outcome updates into the Order lifecycle |
| Finance operator | delivery terminal outcomes feed selected delivery operational-fee assessments; not settlement truth by themselves |
| Wossol product/operations | durable provider, intervention, return/refusal and outcome evidence for later measurement |

## 8. Control & Merchant Agency

Tracking control mainly belongs to internal Wossol operations. Workers can record permitted follow-up; managers manage the pool and Alerts; neither can simply rewrite the provider observation. The Merchant receives visibility and a customer link, not direct carrier calls through Wossol's API, worker assignment control, shipment status mutation, cancellation/recovery action or Tracking Alert control. The public customer can read progress and use a constrained courier `tel:` action only under the approved state/evidence rules; there are no public Order mutations or redelivery forms.

This is operational control for Wossol's internal team, but mostly Level 1 visibility plus bounded copy-link action for the Merchant. It is not Merchant-controlled provider execution or Level 5 intelligent delivery optimization.

## 9. Transparency & Trust

- Internal records preserve provider status history, current status, actor/role, structured outcome, method/result, notes by audience, checkpoint, Alert history, assignment and shipment-recreation lineage.
- Provider observations remain distinct from local handling and Alert state; an Alert state change does not set Order or provider status.
- Merchant projections remove provider IDs/references/codes, provider identity, raw transactions, internal notes and Worker identity; separate merchant-safe notes/reasons/checkpoints are explicitly projected.
- Public token lookup uses the exact bearer hash, generic unavailable behavior and read-only service path; plaintext bearer is not stored directly, and manual rotate/revoke writes audited metadata without token value.
- Unknown provider states receive neutral Merchant/customer language while retaining internal review evidence.
- Worker contact links and persisted “notification sent” outcomes are manual/operator-reported evidence. They are not provider-confirmed delivery receipts, consent ledger entries, or proof a message reached the recipient.
- Public controller deliberately passes no client address to its in-process rate limiter because no trusted-proxy topology is owned there; distributed/per-client limiting is a production edge dependency, not verified deployment truth.

## 10. Merchant Value Extraction

Merchants can follow a delivery journey without being exposed to carrier-specific raw state or having to interpret worker notes. Persisted attempt counts, safe reason/location, checkpoints, delivery/return timestamps, structured refusal reasons and explicit customer links can reduce status ambiguity and support follow-up. The product leaves the external provider and internal labor mechanics out of the Merchant surface while preserving a challengeable history.

The value is visibility and safe operational follow-through. This audit did not establish that tracking improves delivery rate, reduces RTO, accelerates delivery, increases communication success, or gives a Merchant a live/real-time view.

## 11. Feature Clusters

1. **Provider truth to explainable progress:** verified identifiers + polling/retries + idempotent status history + normalized lifecycle + audience-specific projections.
2. **Exception to accountable action:** operational grouping + balanced/sticky assignment + structured outcomes + Alert lifecycle + follow-up checkpoint/reopen.
3. **Safe customer visibility:** high-entropy bearer + encrypted retrieval envelope + redacted DTO + stable status/reason keys + Arabic-first page.
4. **Recover without erasing history:** immutable source Shipment + explicit customer intent + bounded replacement lineage + isolated provider-delete exception.
5. **Outcome evidence to downstream records:** delivered/returned/cancelled status feeds Finance assessments and Customers reputation inputs; this is not itself profitability or delivery-quality intelligence.

## 12. Merchant Journey / Old Way vs Wossol Way

| Stage | Likely alternative/risk (inference) | Current Wossol behavior |
|---|---|---|
| Dispatch | merchant waits for a provider message or separate lookup | verified shipment feeds backend provider polling; not browser polling |
| Monitor | carrier-specific codes require interpretation | Merchant sees normalized status and safe context in Order Detail |
| Investigate | contact operations to learn why an attempt failed | safe reason, attempt count, latest location/checkpoint may be visible when persisted |
| Follow up | manual calls and unstructured staff notes | internal operator uses direct contact shortcut and policy-validated structured handling |
| Recover | retry/recreate may lose source or intent | bounded replacement with source lineage and explicit customer-wants-order evidence |
| Share | merchant forwards raw provider link/ID | authorized Merchant/Tracking/Confirmation surfaces explicitly retrieve Wossol bearer URL |
| Understand outcome | carrier status alone is not economic truth | delivery state is persisted and handed to Finance/Customers; no verified analytics loop yet |

Old-way burden here is a reasonable workflow inference, not a measured baseline study.

## 13. Hidden / Non-Obvious Advantages

- Provider and local Order states are not collapsed: return progress can persist while the Order remains out of the terminal return state until qualifying provider evidence arrives.
- Polling uses per-shipment due scheduling, durable synchronization coordination, bounded concurrency/batches, retry backoff, transaction overlap/cursors and stale/replay protections.
- Terminal statuses require a second consistent confirmation before routine polling stops; actionable/recovery/unknown states receive higher polling priority.
- Public-token storage separates lookup hash from an authenticated-encryption envelope needed for trusted explicit link copying; keys remain outside the database through the secure-credentials primitive.
- Replacement Shipments preserve their roots and history; provider deletion cannot cancel locally until live provider state confirms the narrowly allowed PKR operation.
- Structured cancellation/refusal can preserve product/variant attribution only for product-specific reason codes, creating useful future evidence without changing the purpose of the Order timeline.

These are code-level resilience/trust mechanisms. They do not establish production uptime, freshness or delivery outcomes.

## 14. Data & Intelligence Assets

Captured data includes provider identity/status/reason/transaction references, delivery attempts, agent/branch snapshots, provider and Wossol Shipment lineage, polling health/failures, status transitions, actor/role, assignment, follow-up, structured handling outcomes, Alert changes, delivery/return terminal evidence and structured cancellation reasons/item attribution. Customer-safe, Merchant-safe and internal projections are distinct.

This history can support future operational quality analysis, provider comparison, recovery effectiveness, customer/product cancellation research and economics if identity, sample size, missingness, provider definitions and attribution are validated. Today it is not a mature SLA engine, cross-provider benchmarking system, recommendation loop, causal learning system, predictive RTO model or delivered-profit view. Delivery data feeding a Finance fee or Customer reputation counter does not prove profit or predictive customer quality.

## 15. Cross-Section Compound Advantages

- **Confirmation × Tracking × Orders:** only verified dispatch creates post-dispatch work; Tracking returns evidence to the Order lifecycle while the Merchant sees a unified safe timeline.
- **Tracking × Customers:** delivery outcomes can feed customer reputation. Carry forward Customers Review's unresolved normalized-phone reputation key vs country-aware identity mismatch; Tracking's phone-linked observations do not resolve that issue.
- **Tracking × Finance:** terminal delivery outcomes can assess delivery/cancellation/return fees; an assessment is not a complete settlement or delivered margin.
- **Tracking × Products × Orders:** customer refusal can link a product/variant snapshot for a product-specific reason, but V1 has no product cancellation analytics.
- **Tracking × Public Tracking:** secure bearer distribution provides customer read access without an account, while identity/PII/internal work remain redacted.
- **Tracking × Messaging/Consent:** manual call/WhatsApp shortcuts are separate from Customer consent evidence; neither current work record nor public status page proves consent-managed messaging.

## 16. Competitive Analysis

The competitive master classifies tracking, delivery and basic order-status visibility as category baseline. It describes CODZOSS stage visibility, Fufills as a strong operations-monitoring benchmark with RTO warnings and multi-carrier execution, and Delivered as a multi-carrier/regional network. These are dated baseline observations, not fresh 2026 competitor verification.

Wossol's candidate depth is the connected evidence and boundary model—polling/replay safeguards, accountable internal follow-up, read-safe Merchant/public projections and constrained recovery—not proof it is faster or better than a carrier platform. The inspected executable path is Accurate/Mayar-specific; multi-carrier routing and merchant-facing provider-quality comparison were not established. The competitive master does not establish that competitors lack Wossol's precise combination, so any distinctiveness claim remains potential pending current competitor verification and production evidence.

## 17. Marketing Intelligence

**Asset ID:** TRACK-01 — Merchant-safe delivery context. **Evidence:** EV-TRACK-006, 010. **Status:** GREEN for persisted projection; YELLOW for freshness/provider availability. **Recipient/problem:** merchant needs intelligible shipment progress without carrier-code translation. **Angle:** “Follow delivery updates and history from the Order.” **Qualification:** persisted provider observations; no live/real-time or delivery-success guarantee.

**Asset ID:** TRACK-02 — Customer tracking link. **Evidence:** EV-TRACK-011–012. **Status:** GREEN for implemented secure read path, conditional on environment/edge configuration. **Angle:** “Share a read-only Wossol tracking page after verified dispatch.” **Qualification:** no automated notification or customer self-service/re-delivery action.

**Asset ID:** TRACK-03 — Accountable delivery follow-up. **Evidence:** EV-TRACK-003, 008. **Status:** SALES-USEFUL / internal proof. **Angle:** show structured operator outcomes, scheduled checkpoints, and immutable history. **Qualification:** do not present manual entry as confirmed customer contact or a delivered message.

Avoid “live tracking,” “real-time,” “multi-carrier optimization,” “RTO reduction,” “AI delivery intelligence,” “automated customer notifications,” “consent-managed outreach,” “predict delivery,” and “guaranteed delivery.”

## 18. Surprise Findings

The strongest surprise is not the status page. It is the combination of evidence integrity with separation of authority: provider status is captured and checked; human follow-up is recorded independently; Order lifecycle is not manufactured by a worker action; customer views are constructed from allowlisted semantics. The bearer retrieval envelope is also a less visible but important implementation detail enabling repeatable secure copy without plaintext database storage.

## 19. Potential Category Reframes

Current product truth can be described as **delivery follow-through with evidence** or **a clearer delivery view attached to each Order**. Tracking is not a general carrier network, merchant logistics command center, customer communications platform, or intelligent delivery optimization product today.

## 20. Brand Evidence

- **Transparency:** normalized status, history, safe reason and follow-up facts.
- **Accountability:** operator identity/role, append-only handling, Alert history and bounded deletion/recovery audits.
- **Control:** internal Wossol operators have structured actions and checkpoints; Merchant action is deliberately narrower.
- **Care/clarity:** public page uses Arabic-first customer wording rather than raw carrier codes; whether this improves outcomes is unmeasured.
- **Reliability orientation:** high-entropy token, scope checks, idempotency, stale-event guards, retries and provider-confirmed destructive boundary.

These are product qualities supported by source code, not final brand-positioning decisions.

## 21. Weaknesses / Risks / Gaps

1. No live provider account/API permissions, sync freshness, scheduler uptime, carrier SLA, delivery rate or customer/merchant experience was independently verified.
2. Current code's polling/transaction path and older provider-permission documentation disagree; live provider capability must not be inferred from mocks/tests.
3. Current automatic Alert generation is narrower than P3's broad stale/delay/intervention trigger design; operational checkpoints reopen work but are not equivalent to all promised Alert types.
4. P1 dashboard exposes workload/state counts but not the P3 documented delivery-quality rates, historical filters/charts or worker performance snapshot.
5. Older Merchant Tracking specification calls for actor User ID visibility; current P1 Merchant Tracking projection omits worker identity. Current product/spec authority needs an explicit ruling.
6. Internal call/WhatsApp shortcuts and a manually selected `DELIVERY_NOTIFICATION_SENT` outcome are not proof of actual contact, provider message delivery, or consent. Customer consent ledger call sites were not found in inspected Tracking code.
7. Public controller's in-process rate limiter is deliberately bypassed when client address is unknown. Production abuse protection depends on an external trusted edge that was not inspected.
8. One provider-specific adapter was established; multi-carrier failover/routing and comparative provider quality were not established.
9. A relevant Merchant Tracking source spec fails because it still expects old “Delivery / Tracking” text and checks fields in the page file after the history renderer was extracted into `tracking-activity.tsx`. Current code uses “Tracking & Activity”; treat as stale/brittle test, but refresh the guard so future privacy/projection regressions remain meaningful.
10. P1 dashboard/polling implementation provides no validated delivery improvement, SLA or learning loop. Provider status and delivery fees are not delivered profitability.

## 22. Future Strategic Potential

| Category | Assessment |
|---|---|
| Current foundation | provider history, normalized lifecycle, internal work/Alert evidence, safe projections, explicit public token and recovery lineage |
| Inferred potential | measured exception recovery, carrier performance, delivered/return quality and customer-safe notification from committed events |
| Dependencies | provider data permissions/completeness, explicit Alert policy, consent/contact governance, exact identity, sample quality, production telemetry and owner-approved contract alignment |
| Brand relevance | may support “delivery progress with accountable follow-through”; stronger reliability/control claims require measured outcomes |
| Not current truth | multi-carrier optimization, RTO reduction, automatic messaging, real-time courier map/ETA, predictive alerts, and delivery-profit recommendations |

## 23. Claim Safety

| Claim | Status | Boundary |
|---|---|---|
| Merchant can see normalized delivery context and safe history on an Order | GREEN | where persisted; data freshness/provider access not guaranteed |
| Share a secure, read-only customer tracking link after verified dispatch | GREEN, configuration-qualified | edge rate limiting and public-origin configuration are deployment dependencies |
| Internal Tracking records structured customer/courier/provider follow-up | GREEN | records operator assertions, not independently verified contact outcomes |
| Wossol automatically texts/WhatsApps customers or manages consent | RED | no automated sender or consent integration established |
| Real-time, precise location/ETA, or multi-carrier optimization | RED | not established in inspected implementation |
| Alerts detect every delay/stale provider/intervention failure | RED / unresolved contract | P3 intent exceeds current confirmed trigger implementation |
| Improve delivery rate, prevent returns, or guarantee delivery | RED | no outcome or causal evidence |

## 24. Commercial Magnitude

**FOUNDATIONAL operational capability.** A delivery provider handoff is where customer promise becomes delivery reality. Persisted progress, safe explanations and recoverable exceptions are important to merchant trust and Wossol operations. Higher commercial claims depend on provider reliability, contact outcomes, production monitoring and validated reduction in failed delivery/returns.

## 25. Strategic Classification

| Area | Classification |
|---|---|
| Delivery status visibility, provider tracking | TABLE STAKES |
| Merchant-safe Order timeline and customer share link | PARITY / useful experience depth |
| Accountable structured internal handling and bounded recovery | POTENTIAL DIFFERENTIATOR |
| High-integrity provider sync/history and secure bearer retrieval | FOUNDATIONAL TRUST ASSET |
| Multi-carrier routing, provider benchmarking, measured delivery optimization | WHITESPACE / not established |
| Delivery quality/worker performance analytics | P3 intent; P1 implementation gap |

## 26. Action Register

| Priority | Action | Rationale |
|---|---|---|
| MUST CLARIFY | Reconcile current Accurate/Mayar endpoint and transaction-feed code with P3 provider-permission/cadence documents; verify real workspace capabilities. | Prevents incorrect freshness/reliability claims and disabled-feed surprises. |
| MUST FIX / CLARIFY | Decide whether P3 alert triggers include stale/unavailable provider, delayed movement and failed expected follow-up; implement missing trigger types or amend the V1 contract explicitly. | Durable Alert object exists, but trigger breadth appears narrower than specification. |
| MUST CLARIFY | Reconcile P3 dashboard delivery-quality/worker-performance metrics and the older Merchant actor-ID requirement with current P1 outputs/projection. | Contract authority is ambiguous; do not silently call differences “by design.” |
| MUST FIX in claim workflow | Treat phone/WhatsApp activity as manual, self-reported; map each outbound customer contact flow against Customer consent capture/read/revocation where appropriate. | Handling method/outcome is not a receipt and does not prove consent. |
| MUST MATCH | Verify production edge rate limits and origin/token configuration for the public bearer route. | Controller intentionally fails open without a defensible client identity. |
| WORTH ADOPTING | Replace stale Merchant source test with a projection-level contract covering extracted component, redaction and normalized timeline behavior. | Current UI appears moved/renamed while test no longer guards actual behavior. |
| MUST BEAT | Measure provider freshness, recovery resolution, reattempt/delivery outcomes and cost downstream before building delivery quality guidance. | Separate operational activity from causal performance intelligence. |
| WHITESPACE | Consider provider-quality views or carrier choice only after verified multi-provider data and policy. | Competitor baseline includes multi-carrier breadth; current Wossol route is provider-specific. |

## 27. Evidence Register

**EV-TRACK-001 — Internal Tracking UI.** **Type:** P1. **Paths:** `apps/frontend/src/app/admin/tracking/{page.tsx,queue/page.tsx,alerts/page.tsx,shipments/[orderId]/page.tsx,workers/**}`, `apps/frontend/src/app/tracking-worker/**`. **Observed:** internal workspace counters, queues, alerts, worker detail, shipment detail and structured action surfaces. **Confidence:** High.

**EV-TRACK-002 — Tracking worker/dashboard client.** **Type:** P1. **Path:** `apps/frontend/src/app/admin/tracking/tracking-data.ts`. **Observed:** typed operational counters/queue and provider/worker/alert detail projections. **Confidence:** High.

**EV-TRACK-003 — Authenticated API/permissions.** **Type:** P1. **Paths:** `apps/backend/src/modules/tracking/tracking.controller.ts`, `tracking.service.ts`, `apps/backend/src/modules/permissions/permission-catalog.ts`. **Observed:** Admin-only active Workspace scope; worker assigned-record limitations; separate team, handling, Alert, worker, token and provider-delete permissions. **Confidence:** High.

**EV-TRACK-004 — Provider snapshot synchronization.** **Type:** P1/P2. **Paths:** `apps/backend/src/modules/tracking/delivery-tracking-sync.service.ts`, `delivery-tracking-active-polling.service.ts`, `delivery-tracking-polling.service.ts`, `apps/backend/src/modules/external-integrations/accurate-mayar.service.ts`. **Observed:** Accurate/Mayar exact-reference read and response-identity checks, transactional history/lifecycle path, active priority polling, scheduler wake loop, retries and sync health evidence. **Confidence:** High for code behavior; live provider state not verified.

**EV-TRACK-005 — Provider transaction feed.** **Type:** P1/P2. **Paths:** `delivery-tracking-transaction-sync.service.ts`, `delivery-tracking-transaction-polling.service.ts`, `external-provider-event-envelope.ts`, provider sync coordinator. **Observed:** paged incremental feed, overlap/cursor, idempotent external transaction persistence, lease/capability state; polling can stop when all workspaces report authorization unavailable. **Confidence:** High for current source; real account authorization unverified.

**EV-TRACK-006 — Status mapping/lifecycle.** **Type:** P1/P2. **Paths:** `delivery-status-mapping.service.ts`, `delivery-tracking-sync.service.ts`, status-mapping specs. **Observed:** provider code normalization, allowed Order transitions, RTS return-progress distinction, independent return evidence handling, safe unknown behavior and stale/dedup guards. **Confidence:** High for source/tests.

**EV-TRACK-007 — Tracking persistence.** **Type:** P1. **Path:** `apps/backend/prisma/schema.prisma` (`Shipment`, `DeliveryTrackingStatusHistory`, `TrackingOperationalRecord`, `TrackingHandlingRecord`, `TrackingAssignmentHistory`, `TrackingAlert`, `TrackingAlertHistory`, `ProviderShipmentTransaction`, `PublicOrderTrackingToken`). **Observed:** Workspace/Order/Shipment links, current/historical lineage, immutable history/idempotency and public bearer fields. **Confidence:** High.

**EV-TRACK-008 — Handling, worker allocation and Alerts.** **Type:** P1/P2. **Paths:** `tracking.service.ts`, `tracking.repository.ts`, `tracking-handling-policy.ts`, `tracking-foundation.spec.ts`, `Tracking Worker` and `Tracking Team Lead` specs. **Observed:** balanced/sticky assignment, structured policy validation, due checkpoint reopening, actor audit and constrained automatic Alert types. Source `alertForGroup` creates for recovery-required, unknown, DEX and HTR; checkpoint reopening itself writes audit evidence without creating each P3-described stale/delay Alert. **Confidence:** High for inspected paths.

**EV-TRACK-009 — Recovery lifecycle.** **Type:** P1/P2. **Paths:** `apps/backend/src/modules/shipments/shipment-recreation.service.ts`, `shipments.service.ts`, `apps/backend/src/modules/orders/orders.service.ts`, related recreation and provider-delete specs. **Observed:** bounded replacement eligibility/intent/lineage and separate PKR live recheck deletion semantics; related focused tests passed. **Confidence:** High.

**EV-TRACK-010 — Merchant Tracking projection.** **Type:** P1/P2. **Paths:** `apps/backend/src/modules/orders/orders.service.ts`, `apps/backend/src/modules/tracking/merchant-order-tracking-projection.ts`, `apps/frontend/src/app/merchant/orders/detail/page.tsx`, `tracking-activity.tsx`, `order-data.ts`. **Observed:** authorized read-only Order projection, normalized facts, history and copied secure link; no provider field/worker identity in the Merchant tracking event UI. **Confidence:** High for source; one stale source test failure noted in EV-TRACK-015.

**EV-TRACK-011 — Public bearer backend.** **Type:** P1/P2. **Paths:** `public-order-tracking.controller.ts`, `public-order-tracking.service.ts`, `public-tracking-url.service.ts`, schema `PublicOrderTrackingToken`. **Observed:** verified-link activation; high-entropy bearer, hash lookup, encrypted backend retrieval, exact public safe DTO, token rotation/revocation and audited key-free metadata; controller sets no-store/noindex/no-referrer. `check(null)` means edge limiting is required. **Confidence:** High for source/tests, deployment not verified.

**EV-TRACK-012 — Public customer UI.** **Type:** P1/P2/P3. **Paths:** `apps/frontend/src/app/track/[token]/page.tsx`, `apps/frontend/next.config.ts`, `track/page.source.spec.ts`, `CUSTOMER_PUBLIC_ORDER_TRACKING_V1_MASTER.md`. **Observed:** Arabic-first read-only bearer page; sanitized status/reason/handling keys, destination/products/totals, conditionally evidenced courier call, store CTA; no customer phone/address, raw codes, WhatsApp automation, or public mutation. The current master supersedes older public-search/contact specs. **Confidence:** High for current source and current P3 contract.

**EV-TRACK-013 — Contact/consent boundary.** **Type:** P1/P3. **Paths:** worker/admin shipment pages, `tracking-handling-policy.ts`, `TrackingHandlingRecord` schema, `apps/backend/src/modules/customers/customer-consent-ledger.ts`; repository search for `appendCustomerConsentEvent`. **Observed:** operator Phone/WhatsApp deep links and method/outcome records; no sender receipt or consent helper call site in Tracking. Customer Review directs this distinction be carried into Tracking. This does not decide the lawful basis of operational delivery contact. **Confidence:** High for inspected scope, not global/deployed absence.

**EV-TRACK-014 — Current and historical Tracking contracts.** **Type:** P3. **Paths:** `docs/wossol-system-design/01-system-design/admin-platform/tracking/{README.md,MERCHANT_ORDER_TRACKING_V1_MASTER.md,CUSTOMER_PUBLIC_ORDER_TRACKING_V1_MASTER.md,Tracking Dashboard, Alerts & Performance System.md,Tracking Status Mapping & Reopen Logic.md,Merchant-facing Tracking Visibility.md}` and worker/handling/logs docs. **Observed:** current bounded Merchant/Public masters plus older broader visibility, API-permission, dashboard and Alert trigger claims. Customer Public master explicitly supersedes earlier public-contract decisions; other P3/P1 deltas remain identified below. **Confidence:** High for documented intent; code governs executable behavior.

**EV-TRACK-015 — Verification.** **Type:** P2. **Observed:** selected backend Tracking/Public Tracking/Accurate Mayar/Shipment recreation/provider deletion/scheduler run completed with 166 tests, 0 failures; frontend and backend typechecks passed. Frontend source checks: public tracking and Tracking Worker checks passed (4 tests total), while `merchant-order-tracking.source.spec.ts` had 1 failure/1 test because it expects `Delivery / Tracking` and scans the Order page for reason/product text now rendered by `tracking-activity.tsx`; current page uses `Tracking & Activity`. This is recorded as a stale/brittle source test, not silently treated as a UI pass. No browser/runtime test was run in this audit. **Confidence:** High for local commands and output only.

**EV-TRACK-016 — Provider discovery context.** **Type:** P3/P4 reference. **Path:** `docs/integrations/accurate-mayar/ACCURATE_MAYAR_CUSTOMER_TRACKING_DISCOVERY.md`. **Observed:** prior read-only provider discovery reports restricted direct ID/transaction paths and uncertain provider fields. This audit did not repeat external introspection or treat that historical note as current runtime proof. **Confidence:** Contextual only.

## 28. Contradictions & Uncertainty

1. **CONTRADICTION-TRACK-001 — Provider API permission/cadence contract.** **Source A (P3):** the appended “Priority Shipment Polling V1” in `Tracking Status Mapping & Reopen Logic.md` says `shipment(id)` is authorized, `listShipmentTransactions` is not, and the shipment-ID poll is the authoritative path. **Source B (P1/P2):** current `AccurateMayarService` tests assert exact-reference `listShipments` reads without `shipment(id)`; current executable `DeliveryTrackingTransactionSyncService` pages `listShipmentTransactions` and maintains a capability-unavailable path, while active polling uses the reference-list reader. **Nature:** documented API permissions/cadence no longer clearly describe current code. **Evidence strength:** P1 current source and synthetic P2 adapter tests establish intended code path, not live account authorization. **Working conclusion:** source intends both reference-list active polling and capability-gated transaction polling; which succeeds in production is NOT VERIFIED. **Required verification:** check current provider-account permissions/read access, then amend docs or code and record actual authorized path/cadence.
2. **CONTRADICTION-TRACK-002 — P3 Alert breadth vs P1 triggers.** **Source A (P3):** finalized dashboard/Alert architecture includes unusually long preparation, stale/unavailable data, delayed pickup/transit, unchanged progress, status conflicts and intervention without expected change. **Source B (P1):** automatic Alert creation in inspected `alertForGroup` covers recovery-required and unknown states plus DEX/HTR; due checkpoint processing reopens handling and audits but does not create those full Alert types. **Nature:** durable Alert lifecycle is implemented, but promised trigger coverage is not demonstrated. **Working conclusion:** classify Alert infrastructure LIVE and trigger breadth PARTIAL/UNCERTAIN; do not claim a complete delay/staleness monitor. **Required verification:** enumerate intended triggers and either add/test them or explicitly narrow/version the contract.
3. **CONTRADICTION-TRACK-003 — P3 dashboard analytics vs P1 counters.** **Source A (P3):** Dashboard design describes delivery quality rates, status distribution, time filters/charts and worker-performance snapshots. **Source B (P1):** current `TrackingService.dashboard` returns current counts for actionable/reopened/waiting/closed/unassigned, Alerts, available workers and checkpoint/today buckets; UI renders counters and queue links only. **Nature:** P1 does not establish the broader P3 performance/quality dashboard. **Working conclusion:** operational dashboard is LIVE; analytics depth is PARTIAL/NOT IMPLEMENTED in inspected P1. **Required verification:** product owner clarifies approved V1 scope; update implementation or current contract.
4. **CONTRADICTION-TRACK-004 — Merchant actor-ID visibility.** **Source A (P3):** historical `Merchant-facing Tracking Visibility.md` final summary says Merchant Tracking exposes actor User ID. **Source B (P1/current consolidated P3):** current `MERCHANT_ORDER_TRACKING_V1_MASTER.md` describes a safe composed read projection; current Orders/Tracking projection and UI do not expose Tracking Worker identity. **Nature:** user-visible accountability boundary is inconsistent across approved-sounding documents. **Working conclusion:** the current executable projection omits identity; historical actor-ID wording is not silently treated as either current entitlement or superseded. **Required verification:** product authority declares the controlling Merchant V1 actor-visibility rule and reconciles the older document.
5. **CONTRADICTION-TRACK-005 — Consent evidence vs manual contact.** **Source A (P1/P3 Customers):** a separate immutable consent ledger exists as evidence foundation but Customer Review says no capture/projection/contact enforcement was established. **Source B (P1 Tracking):** operators can open Phone/WhatsApp links and save manual outcomes/methods; no consent-ledger call site or send receipt was found in Tracking. **Nature:** an action shortcut and self-reported outcome are not consent enforcement or proof of successful sending. **Working conclusion:** manual-assisted operations exist; consent-managed outreach and confirmed message delivery do not. This is not a legal finding and does not determine any separate operational-contact basis. **Required verification:** map contact paths, policy/consent scope and any external send evidence.
6. **SUPERSEDED PUBLIC-DESIGN CONFLICT — Earlier public specs described Order Code lookup, customer PII/contact CTA and broader courier data; the README identifies `CUSTOMER_PUBLIC_ORDER_TRACKING_V1_MASTER.md` as authoritative and that master expressly supersedes them. Current P1 uses the secure token page and strict current DTO. Treat old material as historical, not an unresolved P1 defect.**
7. **UNCERTAINTY-TRACK-006 — Production controls/outcomes.** No live workspace/API access, provider permission check, real dispatch, latency/freshness measurement, edge-rate-limit verification, delivery/return rates, customer reach or merchant adoption was performed. P1 and passing local tests cannot answer these questions.

## 29. Open Questions

1. Which provider API operations and workspace permissions are actually enabled in production, and do both current polling paths complete successfully?
2. Which P3 Alert conditions are current requirements versus historical design aspirations? Who approves the reduced or expanded trigger set?
3. Are delivery-quality/worker performance dashboard metrics still in approved V1 scope, and what metric definitions/time windows are authoritative?
4. Should Merchant Tracking expose actor identity, and if so which exact identity granularity and privacy policy?
5. Which internal operational customer-contact actions should read consent evidence, and what constitutes proof of contact/send completion?
6. Is external edge rate limiting deployed for the anonymous token page, across replicas and by client, and is its trusted-proxy boundary documented?
7. What measured outcomes can support future provider-quality, delay warning, recovery or Merchant delivery-performance claims?

## 30. Methodology Learnings

No general methodology change identified. Existing contradiction and evidence rules are sufficient. Section-specific learning: treat a “Final V1” label as product intent, not proof that every listed dashboard/Alert behavior is executable; compare the concrete trigger/query output and generated projection against the approved contract. This does not justify changing the universal methodology.

## 31. Retroactive Review Impact

No methodology change and no queue entry. Customers Review explicitly required carrying the consent-enforcement distinction into Tracking/Delivery; this audit records it in EV-TRACK-013 and CONTRADICTION-TRACK-005. The Customers identity/reputation key mismatch remains a connected-domain caveat under Section 15, not a Customers re-audit. No other prior section was changed.

## 32. Canonical Section Takeaway

Tracking/Delivery is Wossol's post-dispatch evidence and internal follow-up layer, with useful provider sync safeguards, controlled recovery, and bounded Merchant/customer views. It currently proves neither better delivery outcomes nor broad delivery intelligence. Before stronger reliability, analytics or communication claims, reconcile provider-permission documentation, Alert/dashboard scope, Merchant actor visibility and manual-contact/consent boundaries—and verify the actual production provider and edge configuration.
