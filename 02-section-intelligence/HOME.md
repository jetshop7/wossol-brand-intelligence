# Home — Product → Brand Intelligence Audit

## 1. Audit Metadata

| Field | Recorded state |
|---|---|
| Audit date | 2026-09-25 |
| Product source | `jetshop7/wossol-platform` — local workspace `C:\Users\Global Tech\Documents\wossol-platform` |
| Product branch / commit | `dev/wossol-integration` / `e842e8e4e45ba4748412232c914fdeb20063b8ab` |
| Product local state | Clean at the reviewed source-state check. The current commit contains only the unrelated Shopify COD-form follow-up after the Home review work; no product files were modified by this intelligence correction. |
| Local vs committed GitHub state | `origin/dev/wossol-integration` resolves to the same reviewed product commit. Home findings include the approved Home review corrections already present in the branch. |
| Intelligence source | `jetshop7/wossol-brand-intelligence`, `main`, starting commit `a9c0b45783b750852338e49410ffbc47e12a735c`; clean at audit start |
| Methodology | Master Instructions v1.1; no queued retroactive items |
| Competitive reference | `WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md` v1.0 (2026-09-09) |
| Verification performed | Focused backend Home suite: 18/18 passing. Focused frontend Home suite: 14/14 passing. Frontend test emitted only Node's module-type parsing warning. No running authenticated deployment or production data was available for inspection. |

**Audit status:** Complete for the current code and directly related documentation. Runtime deployment/real-data behavior remains **NOT VERIFIED**.

### Review corrections applied

The Home review trail was located in the Codex task history and reconciled against the reviewed product branch. The canonical record now reflects these applied corrections: blockers and actionable attention precede actions and summaries; Home has exactly three Today metrics (created, confirmed, delivered); the obsolete trend chart/range controls and ring-based performance presentation are absent; duplicate stock attention is not repeated in Current Work; recent updates are bounded to three; non-primary performance outcomes are shown only when non-zero; canonical recommendations remain Analytics-owned; and scope, authorization, request-race protection, partial-failure handling, manual refresh, and merchant-safe copy remain intact. No new product change was required in this intelligence-repository correction.

## 2. Audit Coverage Map

| Surface | Status | What was inspected |
|---|---|---|
| Merchant Home route/UI | INSPECTED | `apps/frontend/src/app/merchant/page.tsx`, `home-data.ts`, `MerchantShell.tsx`, CSS assertions |
| HTTP boundary | INSPECTED | `MerchantPortalController`: authenticated `GET /merchant/home-summary` and `GET /merchant/home-performance` |
| Scope, authorization and store isolation | INSPECTED | `resolveHomeScope`, section-access checks, focused backend tests |
| Summary aggregation | INSPECTED | Today metrics, priority decomposition, current-work counts, notification composition |
| Historical performance aggregation | INSPECTED | event-history SQL, period/timezone handling, focused unit and PostgreSQL-test source |
| Inventory connection | INSPECTED | bounded Waiting-for-Stock availability projection and reservation policy usage |
| Analytics / Decision Center connection | INSPECTED | canonical recommendation handoff, bounded safe projection |
| Data / persistence | INSPECTED | `Order`, `OrderStatusHistory`, `DecisionRecommendation` / event / outcome models |
| Notifications | INSPECTED | owner-service composition and failure isolation |
| Failure/recovery/client freshness | INSPECTED | independent request versions, retry states, scoped focus refresh, no polling |
| Automated tests | INSPECTED / PASSED | backend 18/18; frontend 14/14 |
| Current Home UI specification | INSPECTED | `MERCHANT_HOME_SHELL_UI_SPEC.md` |
| Older Dashboard System document | INSPECTED | P3 intent/reference; materially conflicts with executable Home scope |
| Production telemetry, deployed UI, live auth/session | BLOCKED / UNAVAILABLE | No running authenticated environment was provided |
| Provider/raw operational APIs | NOT RELEVANT | Home is deliberately a merchant-safe composition and does not call provider APIs |

## 3. Executive Section Truth

**Home is a code-backed, permission- and scope-aware operational landing surface for merchants.** It composes existing owner-domain facts rather than creating a second Orders, Inventory, Analytics, Finance, or Notifications system. It turns a selected Workspace and authorized Store scope into: current-day order facts, a prioritized exception list, current operational workload, compact event-time confirmation/delivery outcomes, a bounded Decision Center handoff, and Workspace notifications.

The clearest current strength is not “a dashboard.” It is **truthful operational orientation with safe routing back to the owner systems**: historical outcomes are based on immutable status history rather than current status; time boundaries use the Workspace timezone; unauthorized/no-store users receive omitted order data rather than fabricated zeroes; and attention items deep-link to exact Orders predicates.

It is **LIVE in the current codebase and covered by focused tests**, but live production availability is not verified. Home is not yet evidence of a full decision command center: it has no Home-owned diagnosis, calculation, mutation, automated action, learning loop, financial view, trend chart, inventory forecasting, or notification ownership.

## 4. Scope & Architecture Map

`MerchantHomePage` → `MerchantShell` context/scope → authenticated merchant endpoints → `MerchantPortalService` composition read model → Orders / Inventory / Analytics / Notifications owner services and persisted records.

| Layer | Current role |
|---|---|
| UI | Renders read-only, permission-aware sections and navigates to existing owner routes. It independently requests Summary and Performance and protects against stale responses. |
| API | `GET /merchant/home-summary` and `GET /merchant/home-performance`, guarded by the existing access-token guard. |
| Scope | The service validates an active authorized Workspace and either an authorized active Store or authorized Stores in the selected Workspace. |
| Orders | Supplies created/current status facts plus event-history facts. Merchant pre-confirmation deletions are excluded. |
| Inventory | Supplies only an optional shortage explanation for visible `WAITING_FOR_STOCK` orders; Home does not reserve or allocate stock. |
| Analytics | Supplies a maximum of three already-ranked, merchant-safe recommendations from the canonical Analytics / Decision Center path. |
| Notifications | Supplies a bounded recipient- and Workspace-scoped recent list; failure does not fail Home. |
| Persistence | Home creates no state. It reads Orders, immutable `OrderStatusHistory`, existing recommendations/evidence, and notification records via their owners. |

## 5. Current Capability Inventory

| Capability | Status | Control depth | Merchant consequence |
|---|---|---:|---|
| Scope-safe Home composition | LIVE | 1 — Visibility | A merchant sees only allowed active Workspace/Store facts; invalid scope fails closed. |
| Today facts: created, confirmed, delivered | LIVE | 1 | Avoids conflating a current order status with what happened today. Each metric links to a reproducible Orders view. |
| Current exception priorities | LIVE | 2 — Configuration/navigation | Separates failed delivery, customer risk, stock waiting, and confirmation action into explicit priorities. |
| Stock-shortage context | LIVE, bounded | 1 | Gives a compact explanation of one key active shortage without claiming it will unblock an order. |
| Current-work overview | LIVE | 1 | Shows active confirmation and delivery workload, with exact Orders filters. |
| Confirmation/delivery performance snapshot | LIVE | 1 | Gives event-time outcome context for a selectable Workspace-calendar period; it does not reconstruct history from current status. |
| Canonical recommended-actions handoff | LIVE, dependent | 4 — Guided Control at the system level; 1 on Home | Up to three Analytics-owned recommendations reach the landing surface without creating a duplicate engine. |
| Workspace notification summary | LIVE, dependent | 1 | Keeps recent work context visible while preserving notification ownership. |
| Permission-aware quick actions | LIVE | 2 | Reduces navigation friction for only already-permitted actions. |
| Manual refresh, scoped freshness, focus recovery | LIVE | 1 | Makes read freshness visible and rechecks stale composition after returning to the tab, without polling. |
| Trend chart / Home-owned insight calculation / Finance | NOT PRESENT BY DESIGN | n/a | Explicitly excluded from current executable Home. |

## 6. Workflow & Lifecycle

1. Merchant enters `/merchant`; the shell establishes merchant context, active Workspace, selected Store, and section access.
2. The UI requests Summary and Performance separately. If the Workspace/Store is blocked or unavailable, it stops the read path and presents a safe status state.
3. Summary resolves the authorized active scope. With Orders access and at least one authorized Store, it reads current-day and current-state Orders facts, an optional Inventory shortage projection, and safe deep links.
4. If Analytics access exists, Home calls the canonical seven-day Decision Center path and projects only up to three safe fields. Analytics failure becomes a local unavailable state.
5. Notifications are read independently; notification failure is similarly isolated.
6. Performance queries `OrderStatusHistory`: confirmation uses each Order's latest qualifying confirmation event in the selected period; delivery counts qualifying tracking events. It does not infer history from present status.
7. UI routes action/metric/priority clicks to the owner route. It does not edit records or persist a Home event.
8. A manual refresh reloads both projections. A window-focus/visible-tab event reloads both only after a successful Summary is at least five minutes old; request-version guards prevent obsolete scope responses from replacing current ones.

## 7. Value Recipient Map

| Recipient | Value now |
|---|---|
| Merchant owner / operator | Fast scoped orientation, concrete exceptions, direct path to the responsible operational surface. |
| Operations manager | Separates immediate failed-delivery/customer-risk cases from stock and confirmation work rather than collapsing all trouble into one count. |
| Inventory operator | Receives a cautious product/variant shortage clue only when it is relevant to an active waiting order. |
| Confirmation / delivery teams | Benefit indirectly: event-history outcomes and exact filtered queues reduce ambiguity about what needs review. |
| Marketing / growth user | Can see a bounded canonical recommendation only with Analytics access; no marketing claim should imply Home itself analyses advertising. |
| Wossol operations / support | Less likely to need to translate raw operational state into a merchant-facing landing view; Home deliberately prevents internal/raw provider leakage. |

## 8. Control & Merchant Agency

Home primarily provides **Level 1 visibility**. Its agency value comes from not merely displaying counts: it supplies truthful paths back to the systems where the merchant can act. Permission-aware creation actions are navigation conveniences, not new authority.

The recommended-actions handoff is the sole emerging Level-4 element, but the intelligence belongs to Analytics. Home neither calculates, ranks, explains in depth, records feedback for, nor executes recommendations. This boundary is a positive constraint: it prevents a superficially attractive landing page from becoming a contradictory second Decision Center.

## 9. Transparency & Trust

Strong trust mechanisms observed:

- Workspace-timezone day and range boundaries avoid hidden server-time assumptions.
- Confirmed/Delivered facts use status-history events, retaining orders whose later current status changed.
- Performance selects a deterministic latest confirmation outcome per Order within the period (`created_at`, then ID ordering) instead of double-counting attempts.
- Selected Store and All Stores use authorized active Store scope; inaccessible Workspace/Store requests fail before Order reads.
- Merchant deletions before confirmation are excluded consistently from Home aggregation.
- Zero is not used as a substitute for unavailable Orders access/no Store, and isolated dependency failure is surfaced as unavailable rather than an invented empty result.
- Stock impact calls a known shortage a “key shortage” and does not promise allocation or an unblock outcome.

Transparency limits: Home does not show calculation evidence, recommendation reasons/hashes, actor-level status history, provider state, or raw Inventory availability. These are appropriate safety/ownership limits but mean Home is an orientation surface, not a forensic audit view.

## 10. Merchant Value Extraction

| Technical truth | Practical benefit | Business / emotional value | Claim eligibility |
|---|---|---|---|
| Event-time Orders history drives today/performance values | The merchant is less likely to mistake a later status for what happened during the selected day/period | More credible operational visibility | GREEN — qualified by Workspace/Store scope and available data |
| Ordered exception decomposition with exact filters | The next queue is clearer than a generic alert badge | Less cognitive load; easier prioritization | GREEN |
| Inventory-aware waiting-stock explanation | The merchant can see a relevant shortage context without opening each order first | Faster triage, with restrained claims | GREEN — do not claim automatic resolution |
| Analytics-owned recommendation projection | Important guidance can reach the daily landing surface | A bridge from operational visibility toward guided control | YELLOW — only where canonical Analytics evidence is available; Home is not the engine |
| Isolated requests/failure states/focus refresh | Partial service failure does not erase the full workspace picture | Reliability and calm under imperfect conditions | GREEN as product behavior; runtime uptime is not verified |

## 11. Feature Clusters

### FC-HOME-01 — Truthful operational orientation

**Components:** Workspace-timezone aggregation, immutable status history, merchant-delete exclusion, exact Orders deep links, safe scope resolution.

**Combined effect:** Home presents a concise answer to “what changed and where should I look?” without silently substituting current state for historical facts.

**Strategic meaning:** This is stronger than generic dashboard visibility, but not yet decision intelligence. The defensible value is trustworthy orientation.

### FC-HOME-02 — Attention without false automation

**Components:** ordered priority rows, stock availability read projection, explicit severity, owner-route navigation, no allocation mutation.

**Combined effect:** It turns exceptions into a navigable workload while preserving uncertainty about whether stock will actually resolve every order.

**Strategic meaning:** A small but credible example of Wossol favoring explainable assistance over false certainty.

### FC-HOME-03 — Bounded intelligence distribution

**Components:** Analytics authorization, canonical ranking/materialization, safe three-item projection, separate unavailable state, Analytics deep link.

**Combined effect:** The merchant can encounter a prioritized suggestion in context without parallel algorithms or duplicated explanation.

**Strategic meaning:** A valid foundation for guidance, not proof of an end-to-end learn-and-execute loop.

## 12. Merchant Journey / Old Way vs Wossol Way

| Journey moment | Likely alternative / old way (qualified inference) | Wossol Home current behavior |
|---|---|---|
| Start of day | Visit Orders, Inventory, Confirmation, delivery and notifications separately; determine what is urgent manually | Scoped Home composes a small operational starting view. |
| Notice failure | Scan broad statuses or rely on a generic count | Separates failed delivery, customer risk, stock, and confirmation priorities with direct filters. |
| Understand an outcome | Infer today/period performance from the current order list | Uses immutable event history for confirmed, delivered, and period performance. |
| Find next action | Switch to Analytics or ignore analysis | Shows at most three canonical recommendations when permitted, then routes to Analytics. |
| Recover from stale/failing view | Retry whole browser/page and infer whether data is current | Isolates Summary/Performance/notification failures and provides retry plus scoped freshness. |

The alternative workflow is an inference from normal multi-module operations, not first-hand merchant research. No time-saved or conversion/retention outcome is established.

## 13. Hidden / Non-Obvious Advantages

1. **History over present-state reconstruction.** The event-time implementation prevents a common operational misstatement: treating “currently delivered” as “delivered in this period.” This is a defensible micro-proof of data discipline.
2. **Safe degradation is merchant experience.** Recommendations and notifications can fail locally while the central operational summary remains usable; no fake zero or empty success state is created.
3. **Scope isolation is product value, not only security plumbing.** Selected Store vs All Stores does not merge availability across stores, and performance/summary reads are constrained to the merchant's authorized active stores.
4. **No Finance is intentional.** Removing money from Home preserves Finance as its own controlled truth surface and reduces misleading “business health” implications from partial operational metrics.

## 14. Data & Intelligence Assets

| Asset | Current use on Home | Future relevance | Limitation |
|---|---|---|---|
| Immutable `OrderStatusHistory` with source system/context/time | Today metrics and event-time performance | Reliable longitudinal operational measurement | Home exposes aggregates, not explanatory journey/evidence. |
| Order merchant/workspace/store keys | Scope and tenancy isolation | Merchant- and store-specific intelligence | No cross-merchant comparison on Home. |
| Inventory availability / reservation requirements | A single bounded shortage impact | Stock-aware operational recommendations | Home does not forecast, allocate, or explain all constraints. |
| `DecisionRecommendation` evidence/hash/events/outcomes | Indirectly supports canonical Analytics handoff | Feedback and outcome evaluation foundation | Home shows no raw evidence and does not emit feedback on render/navigation. |
| Notifications | Recent contextual update list | Operational continuity | Implementation bounds to three, is Workspace—not Store—scoped, and does not make Home a notification ledger. |

**Intelligence maturity:** Data → connected historical facts and bounded guidance. Pattern interpretation/action/learning occur, if at all, in Analytics; Home itself does not establish them.

## 15. Cross-Section Compound Advantages

| Compound | Current evidence-backed value | Status |
|---|---|---|
| Orders + Confirmation + Tracking → performance | History preserves period outcomes across domains, avoiding current-status distortion | LIVE |
| Orders + Inventory → stock attention | Waiting orders gain a cautious, scoped shortage clue | LIVE, bounded |
| Orders + Analytics / Decision Center → guidance | Operational work and selected recommendations coexist without duplicate computation | LIVE, dependent on Analytics availability/access |
| Orders + Notifications → context | Recent Workspace notices can accompany operation facts without widening order data access | LIVE, bounded |
| Home + Finance | Deliberate separation prevents money data from being used as a shallow Home health signal | LIVE restraint |

## 16. Competitive Analysis

Direct competitors generally offer operational dashboards, order status visibility, and basic analytics; those are category table stakes according to the competitive baseline. The available competitive master does not provide sufficient feature-depth evidence to claim any competitor lacks a scoped Home, event-time aggregation, or bounded recommendation projection.

| Home quality | Comparative reading | Confidence |
|---|---|---|
| Basic landing-page operational visibility | TABLE STAKES / PARITY target | Medium: direct competitors advertise dashboards/operational visibility, but depth varies. |
| History-backed period semantics and exact deep links | POTENTIAL DIFFERENTIATOR in implementation quality | Low–medium: Wossol code verifies this depth; competitor equivalence is not verified. |
| Canonical recommendation handoff | POTENTIAL DIFFERENTIATOR only in combination with real Decision Center evidence | Low: competitors' recommendation depth is largely not verified; Home alone is not differentiation. |
| Finance exclusion / domain boundaries | Trust-design choice, not a standalone competitor claim | Medium on Wossol; competitor comparison insufficient. |

**Competitive conclusion:** Do not market “dashboard” as a differentiator. If adjacent sections validate it, Wossol may eventually communicate a more defensible distinction: operational views that keep historical truth, scope, and action ownership intact. That is currently supporting proof—not a category-winning claim.

## 17. Marketing Intelligence

### MA-HOME-01 — “See the work that needs you”

| Field | Record |
|---|---|
| Capability | Prioritized operational attention with exact owner-system routes |
| Evidence | EV-HOME-003, EV-HOME-004, EV-HOME-010 |
| Recipient | Merchant owner / operations manager |
| Problem | Operational exceptions are scattered across modules or reduced to generic counts |
| Functional value | Sees specific priority types and opens the matching Orders queue |
| Proof point | Separate current-state priorities for failed delivery, customer risk, stock wait, and confirmation action |
| Best use | Product tour, onboarding, sales demo |
| Claim eligibility | **GREEN** — avoid implying automatic remediation or universal real-time monitoring |

### MA-HOME-02 — “Operational facts that keep their time context”

| Field | Record |
|---|---|
| Capability | Workspace-timezone, event-history-backed today/performance facts |
| Evidence | EV-HOME-002, EV-HOME-005, EV-HOME-009 |
| Recipient | Operators who distrust superficial dashboard totals |
| Functional value | Today/period outcomes do not disappear merely because an Order later changes status |
| Proof point | Immutable status-history query semantics and passing focused tests |
| Best use | Sales enablement / trust proof, not headline copy |
| Claim eligibility | **SUPPORTING PROOF ONLY** |

### MA-HOME-03 — “Guidance arrives where work starts”

| Field | Record |
|---|---|
| Capability | Bounded Analytics-owned recommendations on Home |
| Evidence | EV-HOME-006, EV-HOME-007 |
| Recipient | Merchant seeking the next priority, not another report |
| Functional value | At most three canonical recommendations can be surfaced and explored in Analytics |
| Caveat | Home has no recommendation engine and does not expose evidence/explanation or execution |
| Best use | Demo sequence after Analytics has been independently validated |
| Claim eligibility | **YELLOW / SALES-USEFUL**; do not call Home “AI decision intelligence” |

**Demo moment:** Select a Workspace/Store, show four distinct attention types (where data exists), open an exact Orders queue, then show that a recommendation routes to its Analytics owner. The realization is not “Wossol automates the work”; it is “I know where to look next without losing scope or history.”

## 18. Surprise Findings

- The strongest technical value is hidden beneath unremarkable cards: historical outcome calculations explicitly avoid current-status reconstruction.
- A current Home page can carry Decision Center output without duplicating recommendation logic, ranking, evidence, or feedback. This is a disciplined architecture choice, not merely a UI shortcut.
- Finance exclusion is enforced across DTO/service/UI/test expectations, making restraint a concrete trust feature rather than a visual omission.

## 19. Potential Category Reframes

**Potential reframe (qualified):** A merchant landing page need not be an oversized dashboard. It can be a **safe operational orientation layer**: show only what is current, scoped, and actionably routed; leave financial truth, deep analysis, and record ownership with their specialist domains.

Evidence supports the implementation direction, not the proposition's superiority over competitor products. Treat as a product-design principle and future brand evidence, not final positioning.

## 20. Brand Evidence

| Territory | Evidence today | Assessment |
|---|---|---|
| Clarity | Explicit priority types, compact owner-system routes, omitted rather than false zero data | Brand truth today — small but real |
| Trust / accountability | Event history, deterministic calculations, scope validation, safe failures | Brand truth today — technical proof, not a broad promise yet |
| Merchant agency | Permission-aware routes and Analytics handoff help a merchant orient and act | Emerging brand truth; Home itself has limited control depth |
| Intelligence / guidance | Canonical recommendations can appear in Home | Emerging only; the substantive claim belongs to Analytics and requires later audit validation |
| Control | Visibility/navigation rather than Home-owned workflow control | Unsupported as a Home-only brand claim |

## 21. Weaknesses / Risks / Gaps

| ID | Finding | Impact | Classification / action |
|---|---|---|---|
| H-R01 | **Material documentation contradiction remains.** The current Home spec still describes an Orders trend, four Today cards, up to three operational groups including stock, and six recent notifications; the reviewed executable code/tests remove the trend, show three Today facts, return two current-work groups, and slice notifications to three. | Stakeholders, QA, and marketing could evaluate or promise a superseded product shape even after the UX corrections are implemented. | **MUST FIX** documentation/source-of-truth reconciliation. See C-HOME-001. |
| H-R02 | Home performance has percentages but no causal explanation, comparative baseline, or intervention effect. | A merchant can see an outcome without learning why it changed. | **POST-LAUNCH / WHITESPACE**; do not overclaim intelligence. |
| H-R03 | Recommended actions have no Home-level explanation/evidence/feedback; this is intentional, but users must leave Home to understand them. | Guidance may feel opaque on the landing surface. | **WORTH ADOPTING** only after Analytics audit; preserve canonical ownership. |
| H-R04 | No live authenticated runtime or representative data was available. | Rendering, real data shape, role behavior, latency and deployment status are not verified. | **OPEN VERIFICATION**; not a code defect. |
| H-R05 | The older Dashboard System document is P3 intent and includes inventory snapshot, charts, best product, and insights not verified in current Home code. | Unsafe to use as present-tense marketing or acceptance scope. | **DO NOT CLAIM** until implemented and audited. |

## 22. Future Strategic Potential

| Layer | Evidence / status | Safe interpretation |
|---|---|---|
| Current foundation | Scoped operational composition, historical state, Inventory and Analytics seams, immutable recommendation evidence models | A credible substrate for richer orientation and guided action |
| Approved/documented direction | Older Dashboard document describes charts, inventory snapshot, insights, best product, and V2 readiness | **P3 only** and contradicted in part by current executable Home; not current capability |
| Inferred opportunity | Explain priority causes and effect, connect ads/stock/confirmation/delivery, let merchants act then evaluate outcome | Strategic opportunity, not approved Home work |
| Long-term territory | A command surface that proceeds from fact → explanation → recommendation → execution → outcome learning | **BLUE — future territory**; requires validated Analytics and owner-domain controls |

## 23. Claim Safety

| Claim | Rating | Reason |
|---|---|---|
| “A merchant Home that keeps operational priorities in one scoped view.” | GREEN | Direct code and tests support it. |
| “See today's created, confirmed, and delivered Order facts using Workspace time.” | GREEN | Direct code supports it; availability remains permission/store dependent. |
| “Home gives you recommendations.” | YELLOW | Only canonical Analytics recommendations, at most three, when authorized/available. |
| “Home tells you why performance changed or what action will improve it.” | RED | No Home-owned causal explanation, predicted impact, or automated execution. |
| “Wossol's Home is a complete command center with charts, stock forecasts, best products and insights.” | RED | Older design intent is not current executable truth. |
| “Home provides financial health.” | RED | Finance is intentionally forbidden. |

## 24. Commercial Magnitude

| Finding | Magnitude |
|---|---|
| Scope-safe operational orientation | HIGH LEVERAGE supporting experience |
| History-backed performance integrity | HIGH LEVERAGE trust proof |
| Priority decomposition and exact routes | HIGH LEVERAGE workflow aid |
| Stock shortage impact | SUPPORTING / MICRO-PROOF |
| Recommendation projection | HIGH LEVERAGE only when Analytics quality is validated |
| Current Home as standalone market differentiator | NOT ESTABLISHED |

## 25. Strategic Classification

| Area | Classification |
|---|---|
| Compact merchant operational landing view | TABLE STAKES / PARITY target |
| Exact historical semantics, scope and safe degradation | POTENTIAL DIFFERENTIATOR in execution quality |
| Home-owned intelligence | WOSSOL WEAKER / NOT PRESENT by design; belongs to Analytics |
| Canonical guidance distribution | POTENTIAL DIFFERENTIATOR in combination |
| Full command center / decision loop | WHITESPACE / future territory |

## 26. Action Register

| Action | Classification | Rationale |
|---|---|---|
| Reconcile `MERCHANT_HOME_SHELL_UI_SPEC.md` with code/tests or explicitly mark superseded requirements | MUST FIX | Prevents false acceptance criteria and premature claims. |
| Keep Home's historical metrics and exact deep links aligned with Orders contract as Orders evolves | MUST BEAT | This is a trust asset easily lost through shallow dashboard changes. |
| Audit Analytics / Decision Center before using Home recommendation handoff in primary messaging | MUST MATCH | Home cannot substantiate decision-intelligence positioning alone. |
| Consider an Analytics-owned explanation affordance reachable from Home, not a duplicate engine | WORTH ADOPTING | Can reduce opacity while retaining source-of-truth boundaries. |
| Do not add Finance to Home merely for “business dashboard” completeness | DO NOT COPY | It would weaken deliberate financial-domain truth and access boundaries. |
| Develop fact → explanation → action → outcome loops across owner domains | WHITESPACE / POST-LAUNCH | Potential progression from orientation to genuine guided control. |

## 27. Evidence Register

### EV-HOME-001

- **Claim / finding:** Home is an authenticated merchant composition read model with separate summary and performance endpoints.
- **Evidence type:** P1
- **Repository / commit:** `jetshop7/wossol-platform` / `d52fd3e8db087d914efa0d56ad12d9b56f28cb2f`
- **Files / symbols:** `apps/backend/src/modules/merchant-portal/merchant-portal.controller.ts:73-89`; `merchant-portal.service.ts:324-409,530-622`
- **Observed behavior:** Controller delegates to service; service comment and implementation resolve scope and return read projections.
- **Status / confidence:** LIVE in code / High.

### EV-HOME-002

- **Claim / finding:** Home validates active authorized scope and omits Order data rather than representing denied/no-store access as zero.
- **Evidence type:** P1 + P2
- **Files / symbols:** `merchant-portal.service.ts:324-347,405-409,530-546`; `merchant-home-summary.service.spec.ts:43-84`
- **Observed behavior:** Invalid Workspace/Store fails closed; permitted active Stores bound queries; missing Orders permission/no Store returns no Order aggregate.
- **Status / confidence:** LIVE in code / High.

### EV-HOME-003

- **Claim / finding:** Today facts and attention are separate, current-day/current-state projections with exact Orders links.
- **Evidence type:** P1 + P2
- **Files / symbols:** `merchant-portal.service.ts:546-580`; `merchant-home-summary.service.spec.ts:127-178`; `apps/frontend/src/app/merchant/page.tsx:222-230`
- **Observed behavior:** Created/confirmed/delivered use workspace date/history predicates; priority rows are failed delivery, customer risk, waiting stock, confirmation action.
- **Status / confidence:** LIVE in code / High.

### EV-HOME-004

- **Claim / finding:** Waiting-for-Stock context is a read-only, cautious Inventory availability projection.
- **Evidence type:** P1 + P2
- **Files / symbols:** `merchant-portal.service.ts:413-528`; `merchant-home-summary.service.spec.ts:86-126`; `page.tsx:222`
- **Observed behavior:** Ranks a shortage by visible waiting orders, retains store isolation, returns unknown as absent, and UI avoids unblock promises.
- **Status / confidence:** LIVE in code / High.

### EV-HOME-005

- **Claim / finding:** Performance uses immutable event history rather than current Order status, selecting a latest qualifying confirmation outcome per Order.
- **Evidence type:** P1 + P2
- **Files / symbols:** `merchant-portal.service.ts:349-409`; `merchant-home-summary.service.spec.ts:292-308`; `merchant-home-summary.postgres.spec.ts`
- **Observed behavior:** SQL uses `DISTINCT ON` with deterministic event ordering and source-system/status constraints; tests validate semantics and store isolation.
- **Status / confidence:** LIVE in code / High. PostgreSQL suite source was inspected; it was not executed because its isolated database prerequisite was not supplied.

### EV-HOME-006

- **Claim / finding:** Home consumes but does not own Decision Center recommendation calculation/ranking/materialization.
- **Evidence type:** P1 + P2
- **Files / symbols:** `merchant-portal.service.ts:582-597`; `apps/backend/src/modules/analytics/merchant-analytics.service.ts:1522-1546`; `merchant-home-summary.service.spec.ts:178-212`
- **Observed behavior:** Home requests an Analytics-owned seven-day projection, takes max three safe rows, and isolates unavailable state.
- **Status / confidence:** LIVE in code / High.

### EV-HOME-007

- **Claim / finding:** Recommendation history/evidence and feedback/outcome seams exist in persistence, but are not surfaced or emitted by Home render/navigation.
- **Evidence type:** P1
- **Files / symbols:** `apps/backend/prisma/schema.prisma:8204-8287`; `MERCHANT_HOME_SHELL_UI_SPEC.md:76-84`
- **Observed behavior:** Recommendation models retain immutable evidence, events, and outcomes; Home receives a limited projection.
- **Status / confidence:** LIVE persistence foundation / High; end-to-end outcome evaluation behavior not audited here.

### EV-HOME-008

- **Claim / finding:** Home has isolated client requests, retries, request-version protection, and a five-minute scoped focus refresh rather than polling.
- **Evidence type:** P1 + P2
- **Files / symbols:** `apps/frontend/src/app/merchant/page.tsx:15,95-192`; `merchant-home-v1.spec.ts`
- **Observed behavior:** Summary and Performance load independently; stale responses cannot overwrite current scope; focus refresh coalesces in-flight reads.
- **Status / confidence:** LIVE in code / High.

### EV-HOME-009

- **Claim / finding:** Home deliberately excludes Finance and internal/raw provider material.
- **Evidence type:** P1 + P2
- **Files / symbols:** `MERCHANT_HOME_SHELL_UI_SPEC.md:19-23`; `merchant-home-v1.spec.ts:10-15`; `merchant-home-summary.service.spec.ts:36-40`
- **Observed behavior:** DTO/service/UI test assertions deny Finance projection; spec establishes merchant-safe boundary.
- **Status / confidence:** LIVE in code / High.

### EV-HOME-010

- **Claim / finding:** Notifications are Workspace-scoped, bounded to three in current implementation, and failure isolated.
- **Evidence type:** P1 + P2
- **Files / symbols:** `merchant-portal.service.ts:600-622`; `merchant-home-summary.service.spec.ts:309-313`; `page.tsx:234`
- **Observed behavior:** Service calls notifications by Workspace and slices to three; UI distinguishes unavailable from empty.
- **Status / confidence:** LIVE in code / High.

### EV-HOME-011

- **Claim / finding:** Older Dashboard System direction is broader than current Home implementation.
- **Evidence type:** P3 vs P1/P2
- **Files / symbols:** `docs/wossol-system-design/01-system-design/core-systems/Dashboard System.md:22-44,258-387`; `MERCHANT_HOME_SHELL_UI_SPEC.md:49-100`; `merchant-home-v1.spec.ts` test “Home removes the large trend chart...”
- **Observed behavior:** Older document specifies chart/inventory/insights layout; current code/tests intentionally omit the trend and hold a narrower operational composition.
- **Status / confidence:** Contradiction documented / High.

### EV-HOME-012

- **Claim / finding:** The final Home review corrections are present in the reviewed product branch.
- **Evidence type:** P1 + P2
- **Repository / commit:** `jetshop7/wossol-platform` / `e842e8e4e45ba4748412232c914fdeb20063b8ab`
- **Files / symbols:** `apps/frontend/src/app/merchant/page.tsx:222-234`; `apps/frontend/src/app/merchant/home-data.ts:1-17`; `apps/backend/src/modules/merchant-portal/merchant-portal.service.ts:150-622`; `apps/frontend/src/app/merchant/merchant-home-v1.spec.ts:7-148`; `apps/backend/src/modules/merchant-portal/merchant-home-summary.service.spec.ts`
- **Observed behavior:** The reviewed surface has three Today facts, no trend/ring UI or trend contract, bounded three-item updates, non-zero secondary performance outcomes, dedicated attention, and Analytics-owned recommendation projection. Focused review validation reported 9/9 frontend and 10/10 backend checks plus both typechecks and `git diff --check`.
- **Status / confidence:** LIVE in code / High. The review task reported focused validation; this intelligence correction did not rerun product tests.

## 28. Contradictions & Uncertainty

### C-HOME-001 — Home design/specification is not fully aligned with executable behavior

| Field | Record |
|---|---|
| Source A | `MERCHANT_HOME_SHELL_UI_SPEC.md` says Home has an Orders trend, four Today cards, up to three current-work groups including Waiting for stock, and Recent Updates bounded to six. |
| Source B | Current service/UI/tests have no trend endpoint/chart, three Today facts, two current-work groups, and notification `.slice(0, 3)`. Frontend test explicitly asserts the large trend chart is removed. |
| Evidence strength | Source A: P3/current design documentation but stale/inconsistent in these points. Source B: P1 executable code + P2 passing tests. |
| Working conclusion | The current executable Home is the narrower implementation described in this audit. Do not treat absent spec elements as live. |
| Remaining uncertainty | It is unclear whether the spec should be updated to match implementation or the removed elements are planned to return. |
| Required verification | Product owner/maintainer should designate the current authoritative UX contract, then reconcile docs and acceptance tests accordingly. |

### C-HOME-002 — “Command Center” terminology could overstate current depth

The older Dashboard document calls the dashboard a command center, while current code limits Home to read composition and navigation. The safe conclusion is **operational orientation**, not Home-owned command/execution or full decision intelligence. No implementation conflict exists; this is a claim-safety interpretation conflict.

### Other uncertainty

- No production deployment, authentication journey, live data distribution, latency, accessibility-tool run, or mobile rendering was directly observed.
- Only the current product workspace was inspected; it was dirty in unrelated Shopify COD-form files. No GitHub fetch was made, so remote divergence beyond local status is **NOT VERIFIED**.
- Competitor Home/dashboard depth is insufficiently verified for direct superiority claims.

## 29. Open Questions

1. Should the Home UI specification be reconciled to the intentional no-trend/three-notification implementation, or are those features still approved near-term work?
2. Which canonical Analytics recommendations currently materialize against representative merchant data, and how useful/explainable are they in real use? (Requires Analytics audit/runtime access.)
3. Does production preserve the focused-test authorization, scope, timezone, and safe-degradation behavior under real database volume and authentic sessions?

## 30. Methodology Learnings

No reusable methodology change was identified. The existing contradiction protocol, current-vs-future separation, coverage map, and claim-safety system correctly handled the P3 dashboard document versus P1/P2 Home implementation conflict.

## 31. Retroactive Review Impact

No methodology change. The Home review correction record is captured in `04-review-history/HOME_REVIEW_CORRECTIONS.md`; the retroactive methodology queue remains unchanged.

## 32. Canonical Section Takeaway

**Home is not Wossol's intelligence proposition by itself. It is a tested, merchant-safe orientation layer that preserves operational truth: the right scope, the right time basis, explicit exceptions, and routes back to the system that owns the action.** Its strongest brand contribution is evidence for clarity and trustworthy operational visibility. Its future strategic value depends on whether Analytics and the connected owner domains can turn that orientation into explainable, actionable, and measurable guidance without breaking the disciplined boundaries already present.
