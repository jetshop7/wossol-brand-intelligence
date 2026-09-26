# Messaging / WhatsApp / Messenger / Order Capture — Section Intelligence

## 1. Audit Metadata

- **Audit date:** 2026-09-26.
- **Methodology:** `00-methodology/MASTER_INSTRUCTIONS.md` v1.1; `CODEX_OPERATING_PROTOCOL.md` v1.0.
- **Competitive reference:** `WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md` v1.
- **Intelligence source:** `jetshop7/wossol-brand-intelligence`, `main`, synchronized before inspection; prior HEAD `02859f23eb081c70c9fd2c30846e12a8823482a`, clean/upstream-aligned.
- **Product source:** `jetshop7/wossol-platform`, branch `dev/wossol-integration`, committed HEAD `16223bb5e5bd9cdde0d3e4be3f4f87a4075aa48b`. At inspection, Product working tree had pre-existing uncommitted changes in Advertising and Shopify files; this audit did not modify them. Therefore this is a committed-source audit, not verification of the entire working tree.
- **Evidence limitation:** code/tests establish implementation contracts, not deployed Meta configuration, production webhook delivery, live account eligibility, or merchant use.

## 2. Audit Coverage Map

| Surface | Coverage | Evidence |
|---|---|---|
| Merchant entry and channel connection UI | WhatsApp app; Messenger redirect/Meta entry; capture list and create-order handoff | EV-MSG-001–002 |
| Provider authorization | WhatsApp Embedded Signup and Messenger Page One Connect, scoped state, credential boundary | EV-MSG-003 |
| Inbound provider event boundaries | Raw-body signature/verification, normalized WhatsApp coexistence echoes, Messenger Page echoes | EV-MSG-004–005 |
| Capture persistence and lifecycle | bounded/replay-safe capture; workspace/merchant scope; OPEN/CONSUMED/DISMISSED | EV-MSG-006–007 |
| Canonical Order integration | ordinary Order creation validation plus transactionally consumed capture | EV-MSG-008 |
| Data/privacy/attribution boundary | no conversation archive; limited handoff text; referral-touch architecture vs runtime not established | EV-MSG-009 |
| Tests | 50 focused Messaging tests and 3 Orders contract tests passed | EV-MSG-010 |
| Architecture/history challenge | current runtime contradicts older E1-only architecture statement | EV-MSG-011 |

## 3. Executive Section Truth

Messaging is a **PARTIAL, bounded customer-intent handoff**, not a unified inbox, conversational commerce suite, bot, CRM, or messaging analytics product. Current code provisions Meta WhatsApp and Facebook Page/Messenger identities, verifies specific merchant-authored provider echoes, and creates an open Order Capture only when a deliberately narrow `#WOSSOL` marker convention is met. A merchant then reviews the handoff and manually completes an ordinary canonical Order. Orders—not Messaging—owns Order validity, Store selection, product/variant, customer, delivery, payment, inventory and subsequent lifecycle.

The important product behavior is operational conversion with preserved ownership: an authenticated event can become a scoped, replay-safe queue item; it cannot silently become an Order. Messenger can retain up to eight bounded handoff lines and optionally a provider display name; WhatsApp currently produces a marker-only capture without participant, conversation, or handoff lines. This creates a real but uneven bridge from chat-adjacent activity to Wossol operations. It does not establish general message intake, two-way synchronization, a recoverable thread link, or automatic order understanding.

## 4. Scope & Architecture Map

Messaging owns Meta connection/onboarding state, dedicated credentials, provider/channel identity, qualifying event verification, and capture lifecycle. The capture is scoped to Workspace + Merchant and deliberately has no Store authority. Orders owns final canonical Order creation and atomically consumes an eligible capture while applying normal order rules. Advertising owns separate Meta advertising authority; the shared provider does not merge credentials, permissions, or attribution. Internal Wossol Chat (merchant/team/confirmation communication) is a different product domain and is not evidence of customer-facing Meta messaging.

## 5. Current Capability Inventory

| Capability | Status | Current behavior |
|---|---|---|
| WhatsApp connection onboarding | LIVE implementation; runtime NOT VERIFIED | Embedded Signup flow provisions scoped Meta WhatsApp connection and encrypted Messaging credential. |
| Messenger Page onboarding | LIVE implementation; runtime NOT VERIFIED | Entered through Advertising → Meta One Connect; authorized Pages can be selected/provisioned under Messaging ownership. |
| WhatsApp capture detection | PARTIAL | Authenticated `smb_message_echoes`, text-only, active exact connection, trimmed exact case-sensitive `#WOSSOL`; creates marker-only capture. |
| Messenger capture detection | PARTIAL | Authenticated Page echo, sender must be Page, exact Page connection; first line must start with case-sensitive `#WOSSOL`; stores up to eight bounded following/inline lines. |
| Customer inbound message capture | NOT SUPPORTED BY THIS PATH | Tests explicitly reject customer-authored marker messages; ordinary WhatsApp `messages` collection is ignored. |
| Merchant Capture queue | LIVE, bounded | Lists up to 100 newest matching records; open count, context, and dismiss are scoped/permission checked. |
| Capture-to-Order | LIVE, manually mediated | Opens normal Order form with capture context; ordinary validation remains; order and capture consumption/audit occur transactionally. |
| Conversation inbox/history | NOT FOUND AFTER SEARCH | No Wossol message archive or conversation-level UI found in inspected module; Messenger button opens Page inbox, not a specific thread. |
| Instagram | NOT FOUND AFTER SEARCH for runtime onboarding/webhook | Enum/filter affordance exists, but runtime is explicitly not implied; no Instagram webhook implementation found in scoped module search. |
| Referral attribution from messaging | NOT VERIFIED / architecture-only | Architecture defines immutable AcquisitionReferralTouch, but runtime writes/consumption were not found in scoped code search. |

## 6. Workflow & Lifecycle

1. An authorized merchant provisions WhatsApp or a Facebook Page. State is one-time/expiring and scope-bound; secrets are kept in the Messaging credential boundary.
2. Meta sends webhook bytes. The handler verifies subscription/signature and bounded payload shape before evaluating supported message echoes.
3. The provider echo must be business-authored and associated with exactly one active, matching Messaging connection. Customer-authored `#WOSSOL` does not qualify.
4. A qualifying marker creates an OPEN idempotent capture. WhatsApp stores no handoff lines; Messenger stores bounded text lines and may resolve a display name. Neither stores a conversation archive.
5. Authorized merchant users inspect the Workspace queue. Captures can be dismissed or used to open the standard Order form. Messenger affordance only opens Page Business Suite inbox and copies a display name; it does not navigate to the customer thread.
6. The merchant supplies/validates missing canonical fields and creates the Order. Orders rechecks scope/status in its serializable creation transaction and consumes the capture in the same transaction; race/replay cannot create multiple Orders from one capture.

## 7. Value Recipient Map

| Recipient | Current value |
|---|---|
| Merchant operator | A narrow explicit handoff signal can surface in an Order queue instead of being treated as an Order automatically. Messenger may carry bounded notes into manual entry. |
| Merchant owner | Workspace-scoped permissions, safe credential handling, and auditable capture dismissal/consumption separate access from provider setup. |
| Orders/operations | Canonical Order validation and lifecycle remain centralized; capture provenance can remain attached after consumption. |
| Customer | No direct customer-facing Wossol messaging benefit established; message delivery, response, and consent behavior are outside verified scope. |
| Marketing/management | Potential acquisition evidence is an architecture direction, not verified current insight or attribution output. |

## 8. Control & Merchant Agency

The merchant chooses which provider account/Page to connect, can review a capture, decide to dismiss it, and complete a canonical Order through the standard Orders authority. Messaging does not autonomously infer products, create Orders from customer inbound text, decide customer identity, or control delivery/confirmation. This is an operational handoff, not agentic or intelligent conversational control.

## 9. Transparency & Trust

Trust strengths include raw-byte HMAC verification for webhook signatures, bounded payloads, exact provider connection resolution, explicit merchant-authorship checks, unique message identity/replay handling, safe credential projections, Workspace/Merchant scoping, and audited dismissal/consumption. Order creation revalidates the capture inside the canonical transaction.

Limits are equally important: provider delivery/deployment is unverified; some Meta permissions and account conditions are external; WhatsApp capture does not identify a participant or include the preceding conversation; Messenger does not store conversation/thread ID and the UI opens a generic Page inbox; queue listing caps at 100 with no cursor pagination. The handoff should not be presented as a reliable unified message inbox.

## 10. Merchant Value Extraction

The defensible value is reducing one narrow kind of manual transfer: an operator who sends a specific business-authored marker can cause a bounded candidate to appear in Wossol, after which the merchant turns it into an order under existing controls. Messenger supports practical note transfer; WhatsApp is currently closer to an alert/placeholder than an information-rich order handoff. This can reduce missed intent only if the provider echo subscription works, staff know the convention, and someone reviews the queue; those conditions are not proven operationally.

## 11. Feature Clusters

1. **Verified event → bounded capture:** authenticated provider evidence + business-authorship test + exact scoped connection + unique message identity avoids treating arbitrary incoming text as a Wossol order.
2. **Capture → canonical Order:** queue review + ordinary Order validation + transactional one-time consumption protects the Order boundary while avoiding re-entry of available Messenger handoff text.
3. **Separate authorities on shared Meta infrastructure:** shared Meta authorization entry does not merge Messaging and Advertising credentials, permissions, or domain ownership.

## 12. Merchant Journey / Old Way vs Wossol Way

| Stage | Common manual baseline (hypothesis, not universal) | Current Wossol evidence |
|---|---|---|
| Detect intent | Staff notice and remember social messages | Only supported merchant-authored echo marker becomes a capture; customer incoming message is not ingested by this path. |
| Transfer details | Copy/paste or separate notes | Messenger bounded lines can prefill context; WhatsApp currently provides no detail lines. |
| Create order | Re-enter into order system | Standard Order form retains validation, with atomic capture consumption on success. |
| Follow conversation | Return to original thread | Messenger opens generic Page inbox; Wossol does not preserve thread link/history. |

The comparison is not evidence of universal prior practice or measured time savings.

## 13. Hidden / Non-Obvious Advantages

- The capture is intentionally not an Order and cannot bypass Orders validation.
- Workspace-level messaging permits channel setup independent of Store; Store is selected during canonical Order creation.
- One-time event identity and atomic consumption jointly address provider redelivery and merchant double completion.
- Incoming customer-authored text is explicitly excluded, a meaningful safety boundary that simultaneously limits usefulness.
- Messenger optional display name is convenience only, not Customer identity resolution.

## 14. Data & Intelligence Assets

Current persisted assets include scoped connection/provider identity and credential ownership; bounded capture marker/lines/display-name/status/message identity; consumed Order reference; and safe audit events. WhatsApp has no provider participant/conversation identity in the capture. Messenger’s participant field is populated from the recipient Page echo data; no conversation archive or product inference is established. Architecture describes `AcquisitionReferralTouch`, but runtime production/use was not found; do not claim messaging-attributed acquisition, customer matching, intent analytics, or learning loops.

## 15. Cross-Section Compound Advantages

- **Orders:** strongest realized compound; Orders retains sole authority and atomically consumes capture.
- **Customers:** display name does not establish Customer record linkage or consent. Prior Customers audit found no Messaging integration with its consent helper; no enforcement is established here.
- **Confirmation/Tracking/Finance:** downstream processes apply only after ordinary canonical Order creation; no messaging-specific fulfillment, delivery, or financial outcome is proven.
- **Advertising/Commerce:** separate provider/domain authority. Do not infer ad attribution, commerce channel capability, or cross-channel messaging from shared Meta infrastructure.
- **Internal Chat:** separate team/merchant communication product; not a channel inbox or customer conversation archive.

## 16. Competitive Analysis

The competitive master provides a broad operating-software baseline but does not, by itself, prove which named competitor supports an equivalent marker-triggered, provider-authenticated, transactionally consumed handoff. No competitor absence or Wossol uniqueness claim is made. Generic WhatsApp integration, chatbot, social inbox, or order-entry features are not depth-equivalent evidence. Current differentiation is a narrow implementation detail, not yet a validated category advantage; external competitor verification would be needed for a comparative claim.

## 17. Marketing Intelligence

**Current truth:** “Turn a supported, business-authored WhatsApp or Messenger handoff into a reviewable capture, then complete the order through Wossol’s normal order controls” is the strongest truthful explanation, with WhatsApp/Messenger channel limits stated. Avoid implying customer message ingestion, inbox consolidation, auto-ordering, or universal channel coverage.

**Potential territory:** a controlled bridge between social selling intent and structured operations. This remains provisional until broader customer inbound capture, complete conversation context, deployment evidence, usage, and measured merchant outcomes are established.

## 18. Surprise Findings

1. WhatsApp and Messenger are not behaviorally symmetric: Messenger captures bounded text lines; WhatsApp only captures a marker echo and stores no participant or handoff lines.
2. The provider's business echo—not a customer sending the marker—is required. The feature is therefore an operator-authored transfer convention, not social-message recognition.
3. A connected Page's onboarding is initiated from Advertising’s Meta One Connect while Messaging owns the resulting connection/credential, so UX placement and domain authority differ.
4. The architecture document still calls itself governing E1-only and explicitly says no webhook or Capture-to-Order consumption, now contradicted by P1 implementation.

## 19. Potential Category Reframes

Potential idea: “intent handoff into operational commerce.” Current support is too narrow to establish a conversational-commerce category. Preserve as hypothesis; test merchant comprehension, setup success, capture review and completion rates, missed/duplicate cases, and difference from existing social inbox/order workflows.

## 20. Brand Evidence

Evidence for cautious brand traits: explicit permission and authorship boundaries may support disciplined, controlled, practical operation. Evidence against inflated sophistication: no AI, general customer conversation ingestion, autonomous order construction, thread-level continuity, or learning intelligence was established. This feature is not sufficient to define brand personality or promise.

## 21. Weaknesses / Risks / Gaps

1. **Uneven channel value:** WhatsApp captures only the marker, with no customer/participant or handoff content; Messenger supports more detail.
2. **Narrow trigger semantics:** exact/case-sensitive marker conventions may be fragile; nonmatching customer-originated or mixed-content messages are intentionally ignored.
3. **No conversation continuity:** no Wossol thread/history, no direct Messenger thread URL, and no established WhatsApp return-to-conversation workflow.
4. **Consent/contact safety:** no customer consent capture/enforcement tied to these captures was found; do not treat a handoff as marketing permission or a basis to message a customer.
5. **Queue scale/operability:** list capped at 100 without pagination; no measured workload/alerting/retention contract established.
6. **Instagram:** no runtime onboarding/webhook verified despite enum/filter presence.
7. **Referral attribution:** architecture direction lacks verified runtime persistence/consumption.
8. **Deployment and operations:** credentials/configuration, Meta review/permissions, webhook availability, retries, production acceptance, and usage/outcomes are not evidenced.
9. **Architecture drift:** P3 is stale or superseded, but authoritative version reconciliation is unresolved; see contradiction.

## 22. Future Strategic Potential

If intentionally developed, the strongest path is a trustworthy operator loop: explicit, consent-aware customer intent capture; context-preserving thread return; transparent review/identity confirmation; deterministic product matching; and canonical Order handoff with clear exception handling. This is **IDEA / OPPORTUNITY**, not approved or current capability. Future work must preserve domain separation and not convert message presence into identity, consent, attribution, or order truth.

## 23. Claim Safety

| Safe, bounded | Unsafe / unsupported |
|---|---|
| Supported Meta merchant-authored marker echoes can create reviewable order captures. | “All WhatsApp/Messenger orders flow into Wossol.” |
| Messenger captures bounded merchant-provided handoff text; WhatsApp capture currently is marker-only. | “Wossol reads customer chats / understands any message / automatically creates orders.” |
| A merchant manually completes a capture as a normal Order. | “Unified inbox,” “AI conversational commerce,” “customer identity/consent from chat,” or “Instagram messaging live.” |
| Meta connection/setup code exists with secure scoped credential boundaries. | Production reliability, live provider connection, growth/efficiency outcome, competitor superiority, or messaging-derived attribution. |

## 24. Commercial Magnitude

Potentially high frequency in social-commerce businesses, but realized value is unmeasured and constrained by marker convention, echo eligibility, manual queue review, channel asymmetry, and missing conversation context. Commercial magnitude is **UNCERTAIN**; no adoption, conversion, time-saved, or revenue evidence is available.

## 25. Strategic Classification

- **Product status:** PARTIAL operational bridge.
- **Control depth:** Level 2–3: explicit setup and bounded operational control; final Order remains merchant-entered and domain-validated.
- **Differentiation:** unverified/narrow; do not position as category-defining.
- **Evidence strength:** high for source implementation contract; low for production and outcomes.
- **Copyability:** marker convention and queue are technically copyable; safe scoping/atomic domain handoff are more substantial implementation qualities but not proven durable moat.

## 26. Action Register

| Priority | Action | Owner/domain | Reason |
|---|---|---|---|
| P1 | Reconcile version/status of Messaging master architecture with existing webhook, onboarding, and Order-consumption implementation; say whether P3 is superseded and update the authoritative spec. | Product architecture | Current source contradiction can mislead implementation and reviewers. |
| P1 | Define the intended WhatsApp handoff information contract and show a usable way for an operator to associate the marker with a customer/order context without unsupported identity inference. | Messaging + Orders | Marker-only capture has low completion context. |
| P1 | Define customer-contact consent/retention policy and enforce any required permission before messaging or marketing use. | Customers + Messaging | Captures are not consent records. |
| P2 | Add queue pagination/retention/operational monitoring policy and test provider retries/production failure behavior. | Messaging | Bounded listing and runtime operations remain unclear. |
| P2 | Either implement Instagram runtime or keep UI/filter language consistently explicit that it is unsupported. | Messaging | Enum can be mistaken for channel availability. |
| P2 | Verify whether and where AcquisitionReferralTouch is produced and consumed before claiming attribution. | Acquisition + Messaging | Architecture alone is not runtime truth. |

## 27. Evidence Register

| ID | Evidence / source | Supports | Strength / limitation |
|---|---|---|---|
| EV-MSG-001 | `apps/frontend/src/app/merchant/applications/messaging/whatsapp/page.tsx`; Messenger app page | WhatsApp onboarding UI; Messenger setup redirect | P1 UI; not runtime proof |
| EV-MSG-002 | `apps/frontend/src/app/merchant/orders/captures/page.tsx`; Orders create page and `messaging-order-capture.client.ts` | Queue, bounded filters, manual completion navigation/prefill | P1 UI/client; text is not automatically canonical data |
| EV-MSG-003 | Backend `messaging-meta-onboarding.*`, `messaging-meta-messenger-onboarding.*`, client services, `messaging-connection-read.service.ts` | Scope-bound authorization, Page selection, credential-safe connection projections | P1 implementation; tests in EV-MSG-010 |
| EV-MSG-004 | `messaging-whatsapp-webhook.controller.ts`, `messaging-whatsapp-webhook.service.ts` | Raw-body signature, bounds, `smb_message_echoes`, exact marker and capture shape | P1 source; P2 tests |
| EV-MSG-005 | `messaging-messenger-webhook.controller.ts`, `messaging-messenger-webhook.service.ts` | Raw-body signature, Page echo authorship, marker/line truncation, bounded capture | P1 source; P2 tests |
| EV-MSG-006 | `messaging-order-capture.controller.ts`, `messaging-order-capture.service.ts` | Scoped access, read/dismiss permissions, open-first queue, max 100 and safe projection | P1 source |
| EV-MSG-007 | `apps/backend/prisma/schema.prisma`, Messaging capture service | Unique provider identity; one capture/Order relation; lifecycle/scope | P1 schema/service |
| EV-MSG-008 | `apps/backend/src/modules/orders/orders.service.ts` (`messagingCaptureIdentity`, `requireOpenMessagingCaptureInTransaction`, create transaction) | Normal Order path and conditional atomic consumption/audit | P1 source |
| EV-MSG-009 | `WOSSOL_MESSAGING_ACQUISITION_AND_ORDER_CAPTURE_MASTER_ARCHITECTURE_V1.md`; scoped `AcquisitionReferralTouch` source search | Intended immutable referral boundary; runtime write path not found | P3 and bounded negative search, not proof of global absence |
| EV-MSG-010 | Backend focused tests: 50 Messaging specs; `orders-messaging-capture.spec.ts`: 3 specs | Source contracts and expected boundaries; all executed tests passed | P2; includes structural/unit tests, not provider integration |
| EV-MSG-011 | Same P3 master architecture vs EV-MSG-004–008 | Contradiction: E1-only/no webhook/no consumption text vs present implementation | P1 prevails for current behavior; P3 supersession/status unresolved |

## 28. Contradictions & Uncertainty

**CONTRADICTION ID: MSG-CONTRA-001**

- **Source A:** P3 `WOSSOL_MESSAGING_ACQUISITION_AND_ORDER_CAPTURE_MASTER_ARCHITECTURE_V1.md`: status says “E1 backend/data foundation only”; says E1 does not implement public webhook ingestion or Capture-to-Order consumption.
- **Source B:** P1 current backend includes WhatsApp/Messenger webhook controllers/services and Orders transactional capture consumption; merchant UI exposes connection/capture workflows. Focused tests exercise the behavior.
- **Nature:** claimed architecture/runtime maturity and prohibited implementation scope contradict current implemented code.
- **Evidence strength:** P1 source is strongest for current code behavior; P3 remains evidence of prior intended scope but cannot negate implementation.
- **Working conclusion:** classify the implemented flows as current partial capability where code supports them; do not repeat E1-only/no-webhook/no-consumption as current truth.
- **Remaining uncertainty:** whether this architecture version is superseded, whether all provider/API setup is deployed and production-enabled, and whether changed scope was approved. No authoritative replacement/version decision found during this audit.
- **Required verification:** product architecture owner should update/version the master spec and deployment/configuration status. This is an unresolved product-governance issue, not grounds to erase P1 evidence.

Additional uncertainty: Meta production verification and actual merchant use; consent policy; AcquisitionReferralTouch runtime; queue pagination/retention; what WhatsApp coexistence event data Meta emits in the deployed configuration; current competitor parity.

## 29. Open Questions

1. What is the authoritative successor/status for the E1-only master architecture?
2. Is production WhatsApp Coexistence echo subscription enabled for customer workspaces, and what exact merchant message workflow is supported?
3. How is the WhatsApp marker associated with usable customer/order information without assuming missing identity?
4. What data retention and consent rules govern Messenger handoff lines, customer display name, and any subsequent customer contact?
5. Is `AcquisitionReferralTouch` produced anywhere in runtime and consumed by a verified attribution projection?
6. Is generic Messenger Page inbox acceptable operationally, or should a thread-level return path be part of the product contract?

## 30. Methodology Learnings

No methodology change required. This section applies existing hierarchy correctly: present P1 implementation defeats stale P3 architecture for current code truth, while the version conflict itself remains explicitly unresolved. Maintain the distinction between provider connection, authenticated message echo, bounded capture, canonical Order, and attribution/consent.

## 31. Retroactive Review Impact

No methodology-triggered retroactive queue addition is required. Existing Orders, Customers, Integrations, Advertising, Confirmation, and Chat boundaries were used as dependencies; this audit does not revise those sections. No prior Messaging-specific section or review record existed at synchronized HEAD.

## 32. Canonical Section Takeaway

Wossol currently implements a real but narrow Meta business-message-to-Order-capture bridge: a verified merchant-authored marker can create a bounded capture, which the merchant manually completes through canonical Orders with atomic one-time consumption. Messenger carries limited handoff text; WhatsApp is marker-only. This is not an inbox, customer-message ingestion system, automated conversational ordering, consent/identity engine, Instagram runtime, or proven acquisition-attribution capability. The stale E1-only architecture claim must be reconciled before product claims expand.
