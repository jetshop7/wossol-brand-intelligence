# Messaging / WhatsApp / Messenger / Order Capture — Section Intelligence

## 1. Audit Metadata

- **Latest audit date:** 2026-09-27 (incremental V1.2 migration; preserve accepted V1.1 audit and review).
- **Methodology:** `00-methodology/MASTER_INSTRUCTIONS.md` v1.2; `CODEX_OPERATING_PROTOCOL.md` v1.1.
- **Competitive reference:** `WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md` v1.
- **Intelligence source:** `jetshop7/wossol-brand-intelligence`, branch `main`, synchronized from `origin/main` to `1d61c577abee29ead0ac877dfdef0819de677aec`; clean at inspection start.
- **Product source:** `jetshop7/wossol-platform`, workspace `C:\Users\Global Tech\Documents\wossol-platform`, branch `dev/wossol-integration`, HEAD `46716c433de40fbdbeb023d297d167c49909b380`, matching local `origin/dev/wossol-integration`; clean. GitHub was unreachable during a read-only live tip check, so remote freshness was not independently verified. Product files were not modified.
- **Migration provenance:** preserves `04-review-history/MESSAGING_WHATSAPP_MESSENGER_ORDER_CAPTURE_REVIEW_2026-09-26.md` (ACCEPT WITH OPEN PRODUCT ISSUES); rechecks source delta through the current workspace, including the Orders V1.2 migration/review and current Messaging architecture. No new Director Quality Gate is claimed.
- **Evidence limitation:** code/tests establish implementation contracts, not deployed Meta configuration, production webhook delivery, live account eligibility, or merchant use.

### Targeted source-delta reconciliation — 2026-09-26

The accepted section review is based on Product commit `16223bb5e5bd9cdde0d3e4be3f4f87a4075aa48b`. The route/backend reconciliation inspected committed Product HEAD `4e26b4369e6416c22c731b8be706d72562a19d5b` (upstream-aligned at inspection), while Product's working tree contained separate uncommitted Shopify changes not covered here.

The Messenger Page onboarding entry has since moved from Merchant Applications to Advertising → Meta One Connect; the former Messenger route redirects there. This changes entry/authorization orchestration, not domain ownership: Messaging continues to own Page connection state and its encrypted credential, webhook and capture lifecycle, and Page finalization retains the Messaging permission boundary. Advertising exposes only a scoped, credential-free Page status projection. A Page may be provisioned independently of an Ad Account, and vice versa. Automated source/service checks are evidence only of coded contracts; live Meta authorization, Page subscription and production capture remain unverified. No Instagram runtime is established.

P1 delta locations include `apps/backend/src/modules/messaging/messaging-meta-messenger-onboarding.service.ts`, `apps/backend/src/modules/advertising/meta-oauth.service.ts`, `apps/frontend/src/app/merchant/advertising/meta/page.tsx`, `apps/frontend/src/app/merchant/applications/page.tsx`, and the former Messenger route redirect. The focused backend tests passed 81/81 and frontend Advertising/Messaging tests passed 13/13; these tests do not establish live provider acceptance.

This targeted delta is not a repeat Messaging audit or new Director acceptance. The historical Product SHA above remains the accepted audit baseline; the One Connect delta must not be described as having received that earlier review. See `03-master-synthesis/PRODUCT_ROUTE_BACKEND_COVERAGE_RECONCILIATION.md` for the full route/backend crosswalk and uncommitted Shopify caveat. The authoritative trigger is `04-review-history/NOTIFICATIONS_REVIEW_2026-09-26.md`.

## 2. Audit Coverage Map

| Surface | Coverage | Evidence |
|---|---|---|
| Merchant entry and channel connection UI | WhatsApp app; Messenger Page through Advertising Meta One Connect; Page selection/connection projection; capture list, generic Page inbox jump and create-order handoff | EV-MSG-001–002, EV-MSG-012 |
| Provider authorization | WhatsApp Embedded Signup and Messenger Page One Connect, scoped state, credential boundary | EV-MSG-003 |
| Inbound provider event boundaries | Raw-body signature/verification, normalized WhatsApp coexistence echoes, Messenger Page echoes | EV-MSG-004–005 |
| Capture persistence and lifecycle | bounded/replay-safe capture; workspace/merchant scope; OPEN/CONSUMED/DISMISSED | EV-MSG-006–007 |
| Canonical Order integration | ordinary Order creation validation plus transactionally consumed capture | EV-MSG-008 |
| Data/privacy/attribution boundary | no conversation archive; bounded capture and immutable Messenger referral touches; participant-based association; partial Order/Advertising resolution | EV-MSG-009, EV-MSG-013–015 |
| Tests and static check | historical 50 Messaging + 3 Orders tests passed; current selected backend tests 93/94 pass with one stale/mock failure; backend typecheck passes | EV-MSG-010, EV-MSG-016 |
| Architecture/history challenge | current architecture header recognizes post-E1 runtime; stale future-scope clauses remain internally inconsistent | EV-MSG-011–012 |

## 3. Executive Section Truth

Messaging remains a **PARTIAL, bounded merchant-authored handoff**, not a unified inbox, conversational commerce suite, bot, CRM, or messaging decision product. Current code provisions Meta WhatsApp and Facebook Page/Messenger identities, verifies specific merchant-authored provider echoes, and creates an open Order Capture only when a deliberately narrow `#WOSSOL` marker convention is met. Messenger additionally accepts signed, bounded ADS/SHORTLINK referral events into immutable pre-Order touch records and may associate them with a later capture for the same Page/participant. An exact single ADS Ad ID can be resolved against the canonical Advertising graph when the merchant manually completes the capture into an Order. WhatsApp remains marker-only. Orders—not Messaging—owns Order validity, Store selection, product/variant, customer, delivery, payment, inventory and subsequent lifecycle.

The important product behavior is operational conversion with preserved ownership: an authenticated business echo can become a scoped, replay-safe queue item; it cannot silently become an Order. Messenger can retain up to eight bounded handoff lines, optionally a provider display name, and a bounded relationship to referral touch evidence. The merchant can jump to the Page's generic Meta Business Inbox, not a specific conversation/thread. WhatsApp currently produces a marker-only capture without participant, conversation, referral or handoff lines. This creates a real but uneven bridge from selected Meta evidence to Wossol operations. It does not establish general message intake, two-way synchronization, thread-level continuity, customer identity/consent, or automatic order understanding.

## 4. Scope & Architecture Map

Messaging owns Meta connection/onboarding state, dedicated credentials, provider/channel identity, qualifying event verification, immutable referral-touch and capture lifecycle. The capture is scoped to Workspace + Merchant and deliberately has no Store authority. Orders owns final canonical Order creation, derives the bounded Messaging attribution from the capture and atomically consumes it while applying normal Order rules. Advertising owns separate Meta advertising authority and resolves supplied Ad identity only against its canonical scoped graph; shared Meta infrastructure does not merge credentials, permissions, or attribution authority. Internal Wossol Chat (merchant/team/confirmation communication) is a different product domain and is not evidence of customer-facing Meta messaging.

## 5. Current Capability Inventory

| Capability | Status | Current behavior |
|---|---|---|
| WhatsApp connection onboarding | LIVE implementation; runtime NOT VERIFIED | Embedded Signup flow provisions scoped Meta WhatsApp connection and encrypted Messaging credential. |
| Messenger Page onboarding | LIVE implementation; runtime NOT VERIFIED | Entered through Advertising → Meta One Connect; authorized Pages can be selected/provisioned under Messaging ownership. |
| WhatsApp capture detection | PARTIAL | Authenticated `smb_message_echoes`, text-only, active exact connection, trimmed exact case-sensitive `#WOSSOL`; creates marker-only capture. |
| Messenger capture detection | PARTIAL | Authenticated Page echo, sender must be Page, exact Page connection; first line must start with case-sensitive `#WOSSOL`; stores up to eight bounded following/inline lines. |
| Messenger referral provenance | LIVE in source; provider/runtime NOT VERIFIED | Signed Page `messaging_referrals` events accept bounded ADS/SHORTLINK families, persist immutable, replay-safe touches, and attach prior same-Page/participant touches to a later qualifying capture. Association has no visible freshness window or conversation ID. |
| Customer inbound message capture | NOT SUPPORTED BY THIS PATH | Tests explicitly reject customer-authored marker messages; ordinary WhatsApp `messages` collection is ignored. |
| Merchant Capture queue | LIVE, bounded | Lists up to 100 newest matching records; open count, context, and dismiss are scoped/permission checked. |
| Capture-to-Order | LIVE, manually mediated | Opens normal Order form with capture context; ordinary validation remains; order and capture consumption/audit occur transactionally. |
| Conversation inbox/history | NOT FOUND AFTER SEARCH | No Wossol message archive or conversation-level UI found in inspected module; Messenger button opens Page inbox, not a specific thread. |
| Instagram | NOT FOUND AFTER SEARCH for runtime onboarding/webhook | Enum/filter affordance exists, but runtime is explicitly not implied; no Instagram webhook implementation found in scoped module search. |
| Referral-to-Order Advertising evidence | PARTIAL | Orders derives Messaging Order attribution from the consumed capture; exactly one ADS touch with Ad ID may resolve to local canonical Meta Ad / available PRIMARY_PARENT hierarchy; unknown identity is unresolved. Multiple touches and SHORTLINK do not select an Ad. |

## 6. Workflow & Lifecycle

1. An authorized merchant provisions WhatsApp or a Facebook Page. Messenger Page provisioning can be initiated through Meta One Connect with explicit Page selection; Messaging permission/state/credential ownership remains separate from Advertising. State is one-time/expiring and scope-bound.
2. Meta sends webhook bytes. The handler verifies subscription/signature and bounded payload shape. Messenger referral events are normalized separately from business-authored message echoes.
3. A referral event associated with exactly one active Page connection may write an immutable, replay-safe ADS/SHORTLINK touch. A message echo must separately be business-authored and associated with the exact connection; customer-authored `#WOSSOL` does not qualify.
4. A qualifying marker creates an OPEN idempotent capture. WhatsApp stores no handoff lines; Messenger stores bounded text lines and may resolve a display name. Messenger capture creation associates prior same-Page/participant referral touches not later than the echo; no maximum age or conversation-ID constraint was observed.
5. Authorized merchant users inspect the Workspace queue. Captures can be dismissed or used to open the standard Order form. Messenger affordance opens the generic Page Business Suite inbox and copies a display name; it does not navigate to the customer thread.
6. The merchant supplies/validates missing canonical fields and creates the Order. Orders rechecks capture scope/status, derives source attribution from its touch relations, optionally resolves exactly one ADS Ad ID against Advertising's canonical graph, and consumes the capture in the same serializable transaction. Touch-to-capture association still does not establish causality.

## 7. Value Recipient Map

| Recipient | Current value |
|---|---|
| Merchant operator | A narrow explicit handoff signal can surface in an Order queue instead of being treated as an Order automatically. Messenger may carry bounded notes into manual entry. |
| Merchant owner | Workspace-scoped permissions, safe credential handling, and auditable capture dismissal/consumption separate access from provider setup. |
| Orders/operations | Canonical Order validation and lifecycle remain centralized; capture provenance can remain attached after consumption. |
| Customer | No direct customer-facing Wossol messaging benefit established; message delivery, response, and consent behavior are outside verified scope. |
| Marketing/management | A supported Messenger referral touch can flow toward a canonical Order and optional exact Ad identity; attribution freshness/causality and downstream outcomes remain unproven. |

## 8. Control & Merchant Agency

The merchant chooses which provider account/Page to connect, can review a capture, decide to dismiss it, and complete a canonical Order through the standard Orders authority. The backend may derive bounded referral attribution, but the merchant does not review/edit a conversation-thread match in the observed flow. Messaging does not autonomously infer products, create Orders from customer inbound text, decide canonical customer identity, or control delivery/confirmation. This is an operational handoff, not agentic or intelligent conversational control.

## 9. Transparency & Trust

Trust strengths include raw-byte HMAC verification for webhook signatures, bounded payloads, exact provider connection resolution, explicit merchant-authorship checks, unique capture/referral replay handling, safe credential projections, Workspace/Merchant scoping, and audited dismissal/consumption. Order creation revalidates the capture and derives source from stored evidence inside the canonical transaction. Limits include absent touch freshness/session rule, discarded referral `ref`, and no thread/conversation identifier.

Limits are equally important: provider delivery/deployment is unverified; some Meta permissions and account conditions are external; WhatsApp capture does not identify a participant or include the preceding conversation; Messenger does not store conversation/thread ID and the UI opens a generic Page inbox; queue listing caps at 100 with no cursor pagination. The handoff should not be presented as a reliable unified message inbox.

## 10. Merchant Value Extraction

The defensible value has two bounded pieces: a business-authored marker can surface a candidate for manual Order completion, and a supported Messenger referral can carry selected source identity forward without the merchant typing the Ad ID. Messenger supports practical note transfer; WhatsApp is currently closer to an alert/placeholder than an information-rich order handoff. This can reduce source re-entry only when the referral is correctly associated, the provider path works and a merchant reviews/completes the capture; these operational conditions and any time saving are not proven.

## 11. Feature Clusters

1. **Verified event → bounded capture:** authenticated provider evidence + business-authorship test + exact scoped connection + unique message identity avoids treating arbitrary incoming text as a Wossol order.
2. **Capture → canonical Order:** queue review + ordinary Order validation + transactional one-time consumption protects the Order boundary while avoiding re-entry of available Messenger handoff text.
3. **Separate authorities on shared Meta infrastructure:** shared Meta authorization entry does not merge Messaging and Advertising credentials, permissions, or domain ownership.
4. **Referral touch → capture → Order evidence:** immutable signed touch plus participant association plus Orders-owned canonical identity resolution can preserve limited source context across the manual handoff, but lacks freshness/thread semantics and causal proof.

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

Current persisted assets include scoped connection/provider identity and credential ownership; bounded capture marker/lines/display-name/status/message identity; consumed Order reference; safe audit events; and immutable Messenger referral touches with bounded source/type, Ad ID when present, timestamp, participant/Page scope and hashed replay identity. A separate join table associates capture with eligible touches. This is actual pre-Order referral evidence, not a full conversation archive or general customer identity. The Page-scoped participant identifier is persisted and used for association; retention policy was not found. `providerConversationId` is currently null on these event/capture paths, and a referral `ref` value is parsed but not stored. No customer identity resolution, consent, message archive, behavioral analytics, causal attribution, recommendation, or learning loop is established.

## 15. Cross-Section Compound Advantages

- **Orders:** Orders retains sole authority and atomically consumes capture; it derives bounded source attribution and can pass one exact Messenger ADS Ad ID to the Advertising resolver.
- **Customers:** display name does not establish Customer record linkage or consent. Prior Customers audit found no Messaging integration with its consent helper; no enforcement is established here.
- **Confirmation/Tracking/Finance:** downstream processes apply only after ordinary canonical Order creation; no messaging-specific fulfillment, delivery, or financial outcome is proven.
- **Advertising:** separate authority. One Messenger referral Ad ID can become exact evidence only when a scoped canonical Meta Ad is uniquely found and the available primary-parent chain is coherent; no provider call or heuristic graph inference occurs. The touch-to-capture association is not proven causal attribution and may be stale because no freshness interval was observed.
- **Commerce:** Meta Messenger capture is not evidence of a Commerce store channel or Shopify-native order ingestion.
- **Internal Chat:** separate team/merchant communication product; not a channel inbox or customer conversation archive.

## 16. Competitive Analysis

The competitive master provides a broad operating-software baseline but does not, by itself, prove which named competitor supports an equivalent marker-triggered, provider-authenticated, transactionally consumed handoff. No competitor absence or Wossol uniqueness claim is made. Generic WhatsApp integration, chatbot, social inbox, or order-entry features are not depth-equivalent evidence. Current differentiation is a narrow implementation detail, not yet a validated category advantage; external competitor verification would be needed for a comparative claim.

## 17. Marketing Intelligence

**Current truth:** “Turn a supported, business-authored WhatsApp or Messenger handoff into a reviewable capture, then complete the Order through Wossol’s normal controls” remains the core explanation. For Messenger only, a separately received supported referral may be preserved and, in the single-ADS case, resolved to a canonical Ad when its identity matches. This is supporting provenance evidence, not a claim that the referral caused the Order. Avoid implying customer-message ingestion, inbox consolidation, thread-level continuity, auto-ordering, consent-managed contact, or universal channel coverage.

**Potential territory:** a controlled bridge between social selling intent and structured operations. This remains provisional until broader customer inbound capture, complete conversation context, deployment evidence, usage, and measured merchant outcomes are established.

## 18. Surprise Findings

1. WhatsApp and Messenger are not behaviorally symmetric: Messenger captures bounded text lines; WhatsApp only captures a marker echo and stores no participant or handoff lines.
2. The provider's business echo—not a customer sending the marker—is required. The feature is therefore an operator-authored transfer convention, not social-message recognition.
3. A connected Page's onboarding is initiated from Advertising’s Meta One Connect while Messaging owns the resulting connection/credential, so UX placement and domain authority differ.
4. The current architecture header now acknowledges the post-E1 webhook/capture/referral runtime, but older E1 paragraphs and “future Capture-to-Order” wording remain internally stale or ambiguous.
5. Messenger referral evidence now supplies a narrower source/provenance path than the V1.1 audit found, but still stops short of conversation identity, complete attribution, and outcomes.

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
7. **Referral association quality:** runtime stores and consumes touches, but joins all prior same-Page/participant touches at or before the business echo time; no freshness/session/conversation window was found. A lone stale ADS touch may therefore be attached to a later unrelated capture and appear exact at Order attribution.
8. **Arrival-order dependency:** capture links only touches already persisted when the business echo is processed. If an earlier-timestamp referral webhook arrives after the marker/capture, the observed code has no backfill path to attach it later. Current unit coverage exercises referral-before-marker ordering, not out-of-order delivery.
9. **Referral evidence loss:** `ref` is parsed but not persisted, and current webhook paths leave `providerConversationId` null. SHORTLINK touch source/type may remain in touch records but does not become Advertising evidence.
10. **Consent and PII:** Page-scoped participant ID and optional display name are retained; no consent capture/retention policy or canonical Customer binding was established.
11. **Deployment and operations:** credentials/configuration, Meta review/permissions, webhook availability, retry/retention behavior, production acceptance and usage/outcomes are not evidenced.
12. **Architecture drift:** the latest doc header now recognizes runtime beyond E1, but internal future-scope clauses remain stale/ambiguous; see contradiction.

## 22. Future Strategic Potential

If intentionally developed, the strongest path is a trustworthy operator loop: explicit, consent-aware customer intent capture; context-preserving thread return; transparent review/identity confirmation; deterministic product matching; and canonical Order handoff with clear exception handling. This is **IDEA / OPPORTUNITY**, not approved or current capability. Future work must preserve domain separation and not convert message presence into identity, consent, attribution, or order truth.

## 23. Claim Safety

| Safe, bounded | Unsafe / unsupported |
|---|---|
| Supported Meta merchant-authored marker echoes can create reviewable Order captures. | “All WhatsApp/Messenger orders flow into Wossol.” |
| Messenger captures bounded handoff text and may retain a supported referral touch; WhatsApp capture currently is marker-only. | “Wossol reads customer chats / understands any message / automatically creates Orders.” |
| A merchant manually completes a capture as a normal Order; one Messenger ADS referral may resolve to an exact canonical Ad identity. | “Unified inbox,” “AI conversational commerce,” “customer identity/consent from chat,” “Instagram messaging live,” or “the Ad caused this Order.” |
| Meta connection/setup code exists with scoped credential boundaries. | Production reliability, live provider connection, growth/efficiency outcome, competitor superiority, full/causal messaging attribution, or learning intelligence. |

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
| P1 | Finish reconciling the Messaging architecture: it now acknowledges current webhook/capture/referral runtime at the top, but retains “future Capture-to-Order” and E1-scope clauses that read as current restrictions. | Product architecture | Partial documentation correction leaves current ownership/maturity ambiguous. |
| P1 | Define the intended WhatsApp handoff information contract and show a usable way for an operator to associate the marker with a customer/order context without unsupported identity inference. | Messaging + Orders | Marker-only capture has low completion context. |
| P1 | Define customer-contact consent/retention policy and enforce any required permission before messaging or marketing use. | Customers + Messaging | Captures are not consent records. |
| P2 | Add queue pagination/retention/operational monitoring policy and test provider retries/production failure behavior. | Messaging | Bounded listing and runtime operations remain unclear. |
| P2 | Either implement Instagram runtime or keep UI/filter language consistently explicit that it is unsupported. | Messaging | Enum can be mistaken for channel availability. |
| P1 | Define freshness/session/conversation boundaries and out-of-order webhook reconciliation for participant-linked referral touches; test stale, delayed, duplicate and multiple-touch cases before treating joined Ad evidence as exact Order attribution. | Messaging + Advertising + Orders | Current code links all prior already-persisted same-Page/participant touches without a time window or observed backfill; a lone stale touch may be joined, while a delayed earlier touch may be missed. |
| P2 | Decide whether to retain bounded referral `ref`/shortlink evidence and whether `providerConversationId` can be safely preserved; define participant identifier retention. | Messaging + Privacy/Product | Current path discards `ref`, leaves conversation ID null, and persists Page-scoped participant identity. |

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
| EV-MSG-009 | `WOSSOL_MESSAGING_ACQUISITION_AND_ORDER_CAPTURE_MASTER_ARCHITECTURE_V1.md`; scoped source search at prior baseline `16223bb` | Intended immutable referral boundary; at that historical source snapshot no runtime write/consume path was found | Historical P3/P1 observation only; superseded by current Product delta EV-MSG-013–015 |
| EV-MSG-010 | Backend focused tests: 50 Messaging specs; `orders-messaging-capture.spec.ts`: 3 specs | Source contracts and expected boundaries; all executed tests passed | P2; includes structural/unit tests, not provider integration |
| EV-MSG-011 | Same P3 architecture vs prior P1 implementation at `16223bb` | Original E1-only/no webhook/no consumption conflict | Historical conflict; current header now acknowledges post-E1 runtime, but stale internal future-scope clauses remain |

## 28. Contradictions & Uncertainty

**CONTRADICTION ID: MSG-CONTRA-001**

- **Source A:** P3 `WOSSOL_MESSAGING_ACQUISITION_AND_ORDER_CAPTURE_MASTER_ARCHITECTURE_V1.md`: the current header says the architecture governs and explicitly acknowledges post-E1 webhook, `#WOSSOL` capture and referral runtime. Internal ownership/permission paragraphs still describe final Capture-to-Order as future, and E1 boundary text describes webhooks/consumption as future work.
- **Source B:** P1 current Product at `46716c4` includes WhatsApp/Messenger webhooks, Merchant Capture UI, Orders transactional consumption and Messenger referral touch production/linking.
- **Nature:** the broad E1-only/no-webhook/no-consumption conflict is partly resolved by the updated header, but the same document retains future-scope language that can read as current restrictions unless E1 is clearly scoped as a historical phase.
- **Evidence strength:** P1 establishes current source behavior; the current P3 header corroborates post-E1 runtime, while internal P3 clauses remain ambiguous.
- **Working conclusion:** do not describe current product as E1-only or as lacking webhooks/Capture-to-Order. Keep a narrower documentation-governance issue for stale future-scope clauses; production approval/deployment remains unverified.
- **Remaining uncertainty:** whether stale clauses are intentionally historical E1 scope or an uncorrected current contract, and whether provider setup/runtime is deployed and approved.
- **Required verification:** Product architecture owner should label E1 history explicitly and reconcile the remaining future-scope phrases.

**CONTRADICTION ID: MSG-CONTRA-002 — Referral touch freshness and attribution semantics**

- **Source A:** P1 `messaging-messenger-webhook.service.ts` stores referral touches and, when a capture is created, links all already-persisted same-Page/participant touches at or before the business-echo timestamp; capture has no conversation ID, the query has no evident maximum age, and no retroactive-link path was found if the touch arrives after capture processing.
- **Source B:** P1 Orders derives Ad evidence when exactly one linked touch is ADS with an Ad ID; Advertising resolves that ID against its scoped canonical graph.
- **Nature:** canonical Ad identity resolution establishes which Ad an external ID names, but the observed participant/time association does not establish that an old touch caused or belongs to the later Order-intent conversation.
- **Working conclusion:** source supports captured Messenger referral provenance and a conditional Ad identity join, not causal or necessarily contemporaneous Order attribution. A lone stale touch may still yield exact Ad identity evidence; freshness semantics are insufficiently evidenced.
- **Required verification:** establish bounded time/session/conversation and late-arrival/backfill policies, test stale, out-of-order and multiple-touch cases end-to-end, and inspect how downstream Advertising labels this evidence.

Additional uncertainty: Meta production permissions/webhook delivery and actual merchant use; consent/retention policy for participant IDs and handoff text; queue pagination/retention; loss of `ref` and absent conversation linkage; skipped Postgres integration; current competitor parity.

## 29. Open Questions

1. Are the remaining “future Capture-to-Order” and no-runtime statements in the architecture intentionally historical E1 scope, or should Product architecture remove/label them?
2. Is production WhatsApp Coexistence echo subscription enabled for customer workspaces, and what exact merchant message workflow is supported?
3. How is the WhatsApp marker associated with usable customer/order information without assuming missing identity?
4. What data retention and consent rules govern Messenger handoff lines, customer display name, and any subsequent customer contact?
5. What freshness/session/conversation and late-arrival/backfill rules should associate referral touches to a later capture, and how should stale, delayed or multiple touches be represented downstream?
6. Should bounded `ref` or provider conversation evidence be preserved, and what participant-ID/text retention policy applies?
7. Is generic Messenger Page inbox acceptable operationally, or should a thread-level return path be part of the product contract?

## 30. Methodology Learnings

No methodology change proposed. V1.2 makes visible the compound provenance/context value previously missed. Existing evidence hierarchy and provenance-to-outcome tests suffice; distinguish touch capture, participant-based association, canonical Ad identity resolution, Order outcome, causality and business decision as separate stages.

## 31. Retroactive Review Impact

RR-V12-002 is updated for this incremental migration; its V1.2 Director Quality Gate remains pending. New referral evidence may be relevant to Advertising exact-evidence coverage and the Orders provenance chain; this audit records the boundary without recursively re-auditing those sections. Existing Customers identity/consent and Orders ownership findings remain controlling. No methodology change or unrelated queue entry is proposed.

## 32. Canonical Section Takeaway

Wossol implements a real but narrow Meta business-message-to-Order-capture bridge: a verified merchant-authored marker creates a bounded candidate, the merchant manually completes a canonical Order, and Orders consumes the capture transactionally. Messenger now also captures allowlisted ADS/SHORTLINK referral touches and can attach prior same-Page/participant evidence to a later capture; one ADS Ad ID may resolve to a canonical Advertising identity. This is stronger provenance continuity than the prior V1.1 audit recorded, but no freshness window, conversation link, causal attribution, customer consent/identity, or downstream learning is established. WhatsApp remains marker-only. This is not a unified inbox, general customer-message ingestion, automatic conversational ordering, Instagram runtime, or measured growth capability. The current architecture header recognizes the runtime, while legacy E1/future wording still needs Product clarification.

## 33. V1.2 Incremental Migration / Delta Review

This migration preserves the V1.1 accepted core and EV-MSG-001–011. Its most important new Product fact is that Messenger referral provenance is no longer architecture-only: a signed Page webhook persists allowlisted referral events and later associates eligible touch records with a qualifying capture. The prior EV-MSG-009 negative finding is explicitly historical at Product `16223bb`; it is superseded by the current delta, not silently erased.

### Prior truth retained

- Messaging Capture remains a bounded candidate, not an Order, Customer, conversation or consent record.
- Provider-authenticated business-authored marker echoes remain a necessary capture condition; incoming customer messages, general inbox sync and automatic Order creation remain unsupported by this path.
- Merchant review and normal Orders validation remain required; Orders retains canonical Order authority and capture is consumed transactionally once.
- Messenger/WhatsApp remain asymmetric: Messenger has bounded handoff lines and optional provider display name; WhatsApp is marker-only.
- Shared Meta infrastructure does not merge Messaging and Advertising credential/permission ownership. No production provider acceptance or merchant outcomes are established.

### Product truth changed since accepted baseline

- Messenger webhook handles a separate signed referral event family. It stores ADS and SHORTLINK touch evidence, a hashed replay identity, Page/account and participant scope, source/context and capture timestamp. Unsupported referral families are ignored. The parsed `ref` is not persisted; conversation ID remains null.
- A later merchant-authored Page echo can create an OPEN capture and associate prior touch rows for the same Page/participant with timestamps no later than the echo. The current query has no evident maximum-age/session/conversation constraint; “eligible” therefore must not be read as “same conversation” or “recent.”
- Orders now derives Messaging-source Order attribution from the consumed capture, ignores browser-supplied attribution for this path, and calls Advertising resolution only when exactly one linked touch is ADS and has an Ad ID. A known scoped Ad may be recorded as exact with available PRIMARY_PARENT ancestors; missing/ambiguous canonical identity remains unresolved. Multiple touches and SHORTLINK do not pick a last-click winner.
- Messenger Page provisioning now runs from Advertising → Meta One Connect with explicit Page selection and Messaging-owned authorization/credentials/permission checks. Messenger may also open the generic Page-level Business Inbox, not a specific thread. WhatsApp remains managed through its own Embedded Signup entry.
- The current architecture header now acknowledges post-E1 runtime and referral handling, partially resolving the earlier broad architecture/source contradiction. Remaining internal statements that call Capture-to-Order or webhook support future work still need explicit historical E1 scoping or correction.

### V1.2 second-pass value synthesis

| Lens | Current bounded value / limitation |
|---|---|
| Merchant job removed / reduced | For a supported Messenger referral, the backend can carry source evidence toward Order creation without the merchant manually re-entering an Ad ID. The merchant still reviews a capture and supplies/validates Order facts; no time saved is measured. |
| Tool / process consolidation | Wossol connects Meta-origin evidence to a Wossol capture/Order workflow and exposes a Page inbox jump. It does not replace Meta Business Inbox or create a unified conversation inbox. |
| Friction / steps removed | Automatic signed touch capture and deduplication can remove manual source transcription for a narrow path. Provider setup, capture review, customer detail, product matching, consent and Order submission remain. No step-count study. |
| Context continuity | Meta Page/participant referral → immutable touch → later business-authored marker capture → canonical Order attribution. This is participant/time continuity, not thread continuity; an unbounded prior touch may be joined, and a late-arriving earlier touch has no observed backfill. |
| Control / trust added | Raw-body signature checks, exact active Page resolution, allowlisted normalization, replay digest, scoped persistence and explicit merchant Order completion preserve meaningful boundaries. The freshness rule and data-retention/consent policy are unresolved. |
| Provenance / truth added | Source (`ADS`/`SHORTLINK`), type, optional Ad ID, event time, Page/participant association and capture reference can be retained. `ref` and conversation identity are not preserved in this implementation. |
| Operational → economic → decision chain | Referral touch is DATA CAPTURED; capture relation is CONNECTED; canonical Ad resolution is conditionally CALCULATED/RECONCILED identity. Orders then enters operational lifecycle. Advertising/Analytics may consume exact evidence, but current touch linkage does not prove causality; delivery/economic outcome, recommendation, action and learning are separate or unestablished. |
| Decision effort reduced | A resolved Ad identity may reduce later manual source matching; no Messaging-owned interpretation or recommended action exists. Stale association risk can instead add reconciliation work. |
| Future compounding | Scoped, replay-safe, timestamped touches could support better source quality if freshness, consent, retention, conversation association and downstream outcomes are made reliable. No network effect or learning moat is established. |
| Proof / demo consequence | Demonstrate signed Messenger referral → business-authored marker → bounded capture → merchant Order completion → exact-or-unresolved Ad evidence. Show a stale/multiple-touch case as unresolved; do not call the result an attributed conversion without qualification. |

### Upstream and downstream value chain

**Upstream:** Meta Page referral event (ADS or SHORTLINK) and a later business-authored echo → exact Messaging Page connection and participant → immutable touch evidence → scoped capture relation. **Orders:** normal merchant entry/validation → capture consumed once → Messaging-source attribution, with optional Advertising evidence. **Downstream:** Advertising may associate a single ADS Ad ID with its local canonical graph; Analytics may consume exact Order evidence and compatible operational/economic facts. There is no current Messaging-owned outcome report, complete conversation history, causal model, message-to-customer identity resolution or learning loop.

The chain is meaningful only conditionally. The code's same-participant + timestamp-before-message association has no evident freshness cutoff. It may connect an old single Ads touch to an unrelated later capture; exact canonical Ad identity would not cure that provenance-association problem. Conversely, if an earlier-timestamp referral webhook is persisted only after a qualifying echo was processed, no backfill path was found. Current unit specs cover referral-before-marker linking, replay and SHORTLINK non-inference, but not stale/out-of-order policy or full production correlation.

### Strategic and marketing delta

The strategic interpretation strengthens from “bounded handoff only; no referral runtime found” to “bounded handoff plus partial, immutable Messenger referral provenance and a conditional Order-to-Ad identity join.” It does **not** strengthen to full attribution, customer identity/consent, causal conversion, revenue, profit, growth, real-time inbox, or decision intelligence. Competitive classification remains unverified/narrow; the competitive master does not establish a competitor absence for equivalent referral capture.

Current marketing eligibility is **SUPPORTING PROOF ONLY / QUALIFIED** for preserving selected Messenger referral evidence into an Order. A demo may show the integrity boundary and exact-or-unresolved identity resolution, but must label that it is not proof the Ad caused the Order. Core truthful explanation remains the narrow merchant-mediated capture. No methodology change is proposed. RR-V12-002 is updated; its Director V1.2 Quality Gate is still pending.

## 34. V1.2 Evidence Additions

**EV-MSG-012 — Current entry and Page ownership.** **Type:** P1/P2. **Product:** `jetshop7/wossol-platform`, commit `46716c433de40fbdbeb023d297d167c49909b380`. **Paths/symbols:** `apps/backend/src/modules/messaging/messaging-meta-messenger-onboarding.service.ts`; `apps/backend/src/modules/advertising/meta-oauth.service.ts`; merchant Advertising Meta One Connect and Messaging connection projection routes. **Observed:** explicit Page provisioning/selection through One Connect, Messaging permission enforcement, Messaging-owned Page connection/credential and provider subscription, while Advertising retains separate Ad Account authority. **Status:** implemented in source. **Caveat:** no live Meta authorization or production Page subscription verified.

**EV-MSG-013 — Messenger referral event provenance.** **Type:** P1, focused P2 tests. **Paths/symbols:** `messaging-messenger-webhook.service.ts` (`normalizeMessengerWebhook`, `createReferralTouch`, `createCapture`); `messaging-order-capture.service.ts`; Prisma `AcquisitionReferralTouch` and `MessagingOrderCaptureReferralTouch`; migrations `20261120_messaging_acquisition_order_capture_e1` and `20261205_messaging_referral_replay_identity`. **Observed:** signature-verified, bounded ADS/SHORTLINK event handling; immutable touch writes with hashed replay identity; later same-Page/participant prior-touch links into merchant-authored capture. **Status:** LIVE in source. **Caveat:** no observed freshness limit, conversation ID, persistence of referral `ref`, DB integration execution, deployment or provider acceptance.

**EV-MSG-014 — Capture-to-Order and Advertising resolution.** **Type:** P1. **Paths/symbols:** `apps/backend/src/modules/orders/orders.service.ts` (`requireOpenMessagingCaptureInTransaction`, `messagingCaptureAttributionInTransaction`); `apps/backend/src/modules/advertising/advertising-acquisition-evidence-resolver.service.ts` (`resolveMessengerReferralAd`); `order-attribution-advertising-evidence.spec.ts`. **Observed:** browser attribution is ignored for capture-based Order; Orders derives source from capture/touch relation and one ADS Ad ID can resolve against the scoped persisted Meta graph; unknown identity is unresolved, multi-touch does not choose a winner. **Status:** LIVE in source. **Caveat:** exact Ad identity is not causal or freshness proof; provider/network call is not made by resolver.

**EV-MSG-015 — Current architecture and data boundary.** **Type:** P1/P3. **Paths:** `docs/wossol-system-design/01-system-design/core-systems/WOSSOL_MESSAGING_ACQUISITION_AND_ORDER_CAPTURE_MASTER_ARCHITECTURE_V1.md`; `apps/backend/prisma/schema.prisma` messaging referral/capture models. **Observed:** current doc header recognizes post-E1 webhook/capture/referral behavior; current schema separates immutable pre-Order touches, capture links and consumed Order, while leaving conversation IDs nullable. **Caveat:** internal document paragraphs retain future-scope language; participant retention and production migration state are unverified.

**EV-MSG-016 — V1.2 verification.** **Type:** P2. **Product commit:** `46716c433de40fbdbeb023d297d167c49909b380`. **Observed:** focused backend run across 11 Messaging/Orders/Advertising specs reported 93 passed / 1 failed / 0 skipped; backend typecheck exited 0. The lone failure was `messaging-meta-messenger-onboarding.service.spec.ts` “a provisioned active META/MESSENGER Page identity satisfies the existing webhook resolver contract,” which throws because its mock omits the newly required `acquisitionReferralTouch.findMany` delegate. This is a stale test-harness incompatibility observed in the current run, not proof of a deployed handler failure. Postgres referral-replay integration and browser/provider acceptance were not run. Frontend source specs could not be launched because the frontend workspace lacks `ts-node/register`; no frontend test result is claimed.
