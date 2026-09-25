# Analytics / Decision Center — Section Intelligence

## 1. Audit Metadata

- **Audit date:** 2026-09-25.
- **Methodology:** `00-methodology/MASTER_INSTRUCTIONS.md` v1.1; `00-methodology/CODEX_OPERATING_PROTOCOL.md` v1.0.
- **Competitive reference:** `01-competitive/WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md` v1.0 (2026-09-09), not freshly verified against competitors.
- **Intelligence source:** `jetshop7/wossol-brand-intelligence`, branch `main`, synchronized HEAD `14bfae854ae487d14645b13c0f6ca002d03cf7fa`, clean before this audit.
- **Product source:** `jetshop7/wossol-platform`, local workspace `C:\Users\Global Tech\Documents\wossol-platform`, branch `dev/wossol-integration`, HEAD/upstream `76cb3db4116e52df2920d90b863d862ec772429c` (`annalytic`, 2026-09-25). Product working tree contains unrelated uncommitted Shopify, Orders, messaging, and Messenger edits (including untracked messaging/Messenger files); these were preserved and not modified. No Analytics-scope changes were present in the worktree at inspection.
- **Scope:** Merchant Analytics operational and cohort reporting, merchant profitability inputs/calculation and breakdowns, Advertising evidence projection, Decision Center/Business Health/Customer Quality Impact/Performance Impact/Next Best Actions, recommendation persistence and feedback, Home's bounded Analytics handoff, plus the distinct internal Admin Platform Analytics surface and direct dependencies.
- **Evidence boundary:** Product source, Prisma schema/migrations, tests and current product documentation. No production database, deployed migration state, live advertising connection, representative merchant session, production recommendation outcomes, or merchant research was accessed. Source tests/typechecks are not proof of deployment or business outcome.

## 2. Audit Coverage Map

| Surface | Coverage | Evidence |
|---|---|---|
| Merchant Analytics entrypoint, UI and filters | active Merchant route, MerchantShell scope, Operational/Profitability/Advertising tabs, Decision Center interaction affordances | EV-ANL-001–003 |
| Merchant API, authentication, scope and authorization | route guards, active Merchant/Workspace/membership, Analytics section access, Store allowlist, Product/Variant scope, Workspace timezone | EV-ANL-001–002 |
| Operational cohort and lifecycle calculations | selected and equal-duration prior period, created-order cohort, lifecycle history/current status, denominators, Test Order query boundary | EV-ANL-004–006 |
| Profitability source and formulas | Finance collections/fees, persisted Inventory allocations/current layer costs, costs/expenses, completeness, attribution and currency behavior | EV-ANL-007–009 |
| Advertising projection | selected Meta connection/account, provider-local reporting date, exact attribution, outcome and spend evidence, caps/unavailable states | EV-ANL-010 |
| Decision Center and action ranking | comparisons, categorical health, thresholds, non-causal descriptions, customer and performance impacts, bounded actions, Market Center recommendation | EV-ANL-011–013 |
| Recommendation evidence, feedback and Home seam | immutable materialization/replay, Merchant interaction events, internal outcome seam, bounded Home projection | EV-ANL-014 |
| Separate Admin Platform Analytics | internal permissions, six read projections, filter contract and owner-domain separation | EV-ANL-015 |
| Product docs, schema and tests | current financial/UI specifications, historical/stale documents, schema and active/archived migration evidence, focused test/typecheck results | EV-ANL-016–018 |
| Live production data, provider accounts, rollout/migration application and merchant outcomes | Not inspected / unavailable | NOT VERIFIED; prevents runtime, adoption, correctness-at-scale and realized-outcome claims |

## 3. Executive Section Truth

Analytics is two materially different products. Merchant Analytics is an authenticated, authorized, Workspace-scoped read projection that combines operational cohort reporting, evidence-qualified profitability, persisted Advertising reporting and a deterministic advisory Decision Center. A separate Admin Platform Analytics is an internal, permissioned, read-only operational reporting workspace over a concrete Workspace. They do not share the same audience, data authority or claim boundary.

Merchant Analytics has moved beyond a dashboard: it can compare same-scope periods, classify categorical health, surface up to three bounded review suggestions, and preserve exactly which evidence generated a recommendation. It remains decision support, not intelligent control. Recommendations do not execute owner-domain actions; Merchant Accept/Dismiss is intent feedback, not proof of action, outcome, causal impact or system learning. Financial completeness, scope and historical evidence limitations remain consequential.

The most material current correctness risk is that the active Merchant Analytics query does not filter `Order.isTestRecord = false`. Orders establishes Test Orders as validation-only and confirmation-only, while the active Analytics projection includes orders selected by Workspace, Store, date and other filters without a test exclusion. Consequently Test Orders may affect operational counts, denominators, cohort maturity, Customer Quality grouping and financial input selection. A separate `AnalyticsService` contains a test-record exclusion but is not the service registered by the active Analytics module. The intended contract/current implementation resolution is open; no fix was made in this audit.

## 4. Scope & Architecture Map

```text
MerchantShell (active Workspace + selected/authorized Store)
  → GET /merchant/analytics (AccessToken + active Merchant/Workspace/section scope)
     → operational created-Order cohort + prior equal-duration cohort
     → Finance collections/operational fees + Inventory FIFO allocations/current layer cost
     → Analytics-owned ad-spend declarations + additional expenses
     → optional selected-account persisted Meta reporting evidence
     → Decision Center aggregates + optional Market Center published snapshot
     → immutable recommendation evidence + bounded Merchant response
     → Accept/Dismiss/Shown/Viewed interaction events (no execution)

Admin user + concrete active Workspace + platform_analytics.view
  → /admin/platform-analytics/*
     → separate internal period/event-time operational reporting projections
```

Merchant Analytics owns derived projections, time/scoping logic, Merchant-entered analytics inputs (daily Product ad spend and Additional Expenses), and DecisionRecommendation evidence/feedback persistence. Orders owns Order identity and lifecycle evidence; Finance owns collection, fee and ledger facts; Inventory owns cost-layer/allocation and stock truth; Advertising owns provider connection/sync/attribution evidence; Market Center owns published cross-Workspace signals. Analytics does not become their authority. Home consumes at most three safe, canonical Analytics recommendations and does not calculate or rank its own copy (Home audit EV/HOME evidence; Analytics-side seam EV-ANL-014).

The internal Admin Platform Analytics V1 surface is not Merchant analytics, Finance, a BI warehouse, or the Merchant Decision Center. It presents bounded Workspace operational facts and owner links; separate access and distinct period/lifecycle semantics apply.

## 5. Current Capability Inventory

| Capability | Status | Current behavior |
|---|---|---|
| Merchant operational reporting | LIVE, with scope/contract risk | Workspace-timezone created-Order cohorts, lifecycle counts/rates, Store/Product/Variant/Region slices, time series and comparable prior period; active path currently does not exclude Test Orders |
| Merchant scope and authorization | LIVE in source | Active Merchant user and Workspace membership, Analytics section access, active Store allowlist, Product/Variant membership checks; selected scopes are server-resolved |
| Profitability projection | LIVE, evidence-qualified | Realized Finance collections less persisted allocation cost, fees, declared ad spend and expenses where attributable; completeness states prevent many false zeros; historical COGS reads current layer unit cost |
| Merchant financial inputs | LIVE | Product/day spend is SPEND, DECLARED_ZERO or absent/UNKNOWN; Additional Expenses are editable Merchant-entered Workspace costs with audit records |
| Advertising Analytics | LIVE, provider-evidence dependent | Read-only persisted Meta reporting for one selected compatible connection/account; exact attribution and Finance-compatible outcomes are kept separate from provider-native purchase conversions |
| Business Health / comparisons | LIVE, deterministic advisory | Categorical dimensions and same-scope preceding-period deltas; no numeric score or market benchmark; core outcome thresholds are code constants |
| Next Best Actions | LIVE, bounded guidance | Deterministic ranked top three with evidence, conservative owner labels and suggested review steps; does not assign, notify or mutate owner domains |
| Customer Quality / Performance Impact | LIVE, descriptive aggregates | Merchant Customer relationship groups and order outcome patterns; no identity/Platform Customer data or speculative loss valuation; current relationship state is used for historical cohort grouping |
| Recommendation evidence and feedback | PARTIAL | Durable immutable generation evidence/hash, replay identity and append-only SHOWN/VIEWED/ACCEPTED/DISMISSED events; internal outcome model/seam exists but no scheduler/evaluator/causal loop is established |
| Personalized Market Opportunity | PARTIAL / narrow | Optional Libya-only, fixed 30-day published Market Center evidence plus minimum/final Merchant cohort checks; absent on unsupported/insufficient/failure paths; not a national demand or profit claim |
| Admin Platform Analytics | LIVE in source, runtime not verified | Separate permissioned concrete-Workspace read-only overview/orders/merchants/products/regions/operations projections and filter options |
| Intelligent execution / learning | NOT ESTABLISHED | No Analytics-triggered owner-domain execution, automatic outcome evaluation, learning, prediction or causal attribution established |

## 6. Workflow & Lifecycle

1. Merchant selects a Workspace and Store context in MerchantShell. API verifies active Merchant membership, Workspace membership, Analytics access, Store scope and Product/Variant relationships; the Workspace IANA timezone defines period boundaries.
2. Merchant chooses a standard or bounded custom period. Analytics queries Orders created within the current period and an immediately preceding equal-duration comparison period, under identical selected scope. The contract describes created-order cohort semantics; lifecycle history/current status contributes whether cohort orders have reached terminal states.
3. Operational reporting aggregates order outcomes and Wossol-managed Confirmation evidence, derives denominators/rates, groups Products/Variants/Stores/Regions, and reports whether cohort Orders remain unresolved. A Test Order filter is absent from the active cohort predicate (EV-ANL-006).
4. Profitability joins selected Orders to Finance collection/fee facts and persisted Inventory cost allocations/layers. It resolves Product-day ad-spend coverage and expenses, returns per-metric COMPLETE/PROVISIONAL/INCOMPLETE/UNAVAILABLE states, and declines unsupported allocations or currency mixing.
5. Advertising Analytics reads persisted provider slices/evidence only. It does not call providers or resolve attribution. A compatible single account/cohort is needed; exact Wossol attribution is distinct from Meta-reported Purchases, and downstream ROAS requires compatible Wossol outcomes/Finance collections.
6. Decision Center compares aggregates and evaluates code-defined health/action thresholds. Where available, it may incorporate a fixed Market Center category snapshot and current Merchant Customer status group evidence. It ranks and caps actions before materializing immutable evidence.
7. Merchant may expand details and send feedback events. Accept is explicitly intent to review/follow, not execution. Merchant Home requests the canonical seven-day path and projects no more than three safe fields; navigation does not execute or record recommendation feedback.
8. Internal Admin Platform Analytics separately authorizes an Admin on a concrete Workspace and reads operational measures for period/event-time views. Its measures must not be conflated with Merchant created-cohort or finance-calculation semantics.

## 7. Value Recipient Map

| Recipient | Current value |
|---|---|
| Merchant owner/operator | Scoped operational trends, qualified financial visibility, explicit missing-data reasons, persisted advertising/outcome evidence, bounded next-step suggestions |
| Merchant finance/marketing operator | Editable Product/day acquisition spend and additional expense evidence; exact-account provider context without silent Store/Variant allocation |
| Merchant operations / confirmation team | Cohort rates, comparisons, cancellations/returns and limited review prompts; not worker-level performance scoring or assignments |
| Wossol operational/Admin staff | Separate internal Workspace-wide reporting for bounded flows, snapshots and department links under Admin permission |
| Inventory and Finance operators | Analytics can point to missing cost evidence and consume owner-domain facts without writing stock or Finance truth |
| End customer | No direct Analytics surface or direct customer value established |

## 8. Control & Merchant Agency

The Merchant has Level 1 visibility and bounded Level 4 guidance from comparisons, evidence summaries and suggested review steps. The Merchant may configure Analytics-owned cost inputs and record recommendation intent, but cannot accept a recommendation into owner-domain execution through this surface. Analytics cannot change an Order, reserve stock, alter Finance ledger, control an ad account, or edit customer status. The internal Admin view is separately permission-gated but is reporting, not merchant control.

This is not Level 5 intelligent control: a complete see → explain → recommend → execute → measure → learn loop is not implemented. A durable recommendation and feedback event are evidence of recommendation/interaction persistence only.

## 9. Transparency & Trust

- Periods and scope are returned in the response; financial metrics carry completeness state and machine-readable reasons. Workspace calendar dates and timezone boundaries are explicit.
- Ad-spend UNKNOWN differs from explicit zero; temporary period input replaces only one Product's saved value in a calculation and is not persisted.
- Unsupported multi-Store Product spend, Variant/Region allocation, shared expenses, fee allocation, mixed currencies and missing cost evidence fail closed rather than use invented splits or FX.
- Advertising shows a selected provider/account evidence context; unresolved attribution and provider-native conversions do not silently become Wossol Orders or deliveries.
- Recommendation evidence snapshots are canonicalized and hashed; replay can reuse exact evidence; feedback/outcome models retain event history instead of mutating recommendation status.
- These are source-level controls. No live session, production database, deployed migrations, provider authority, or independently reconciled Finance truth was verified.

## 10. Merchant Value Extraction

The strongest present benefit is reducing the work of reconciling operational, finance, cost and acquisition records into one scoped analytic view—while exposing where the answer is incomplete. Product-level acquisition spend can be made explicit per local day; realized collection, rather than order selling price, anchors revenue; inventory allocation provenance supports cost calculation; exact-account advertising avoids a falsely unified multi-account picture. Decision Center can highlight a bounded review candidate without pretending to know cause or automatically acting.

The value is conditional on clean and complete owner-domain evidence. This audit does not establish more profit, lower acquisition cost, improved delivery, decision accuracy, time saved, or merchant adoption.

## 11. Feature Clusters

1. **Operational cohort → finance evidence → attributable cost:** lifecycle evidence + Finance collections/fees + persisted Inventory allocations create a more defensible profitability foundation than selling-price arithmetic. Current-layer cost mutability and Test Order scope remain limitations.
2. **Unknown-aware Product economics:** Product/day spend states + FIFO cost provenance + expense/fee attribution boundaries preserve explicit incompleteness instead of manufacturing a global margin.
3. **Advertising outcome chain:** selected-account spend + EXACT attribution + Wossol lifecycle + Finance collections can support confirmed/delivered ROAS when compatibility is complete; provider Purchases stay a separate authority.
4. **Decision Center guidance:** comparable cohorts + categorical health + impact summaries + deterministic action ranking + durable evidence make recommendations inspectable, but not executed or learned from.
5. **Canonical Home handoff:** Home may distribute the Analytics-ranked safe recommendation without duplicate ranking/materialization; substantive decision evidence and explanation belong to Analytics.

## 12. Merchant Journey / Old Way vs Wossol Way

| Stage | Plausible alternative (inference) | Current Wossol behavior |
|---|---|---|
| Gather | export separate order, finance, stock-cost and Ads tables | authorized Merchant Analytics joins selected owner-domain facts |
| Compare | hand-build periods/spreadsheets and reconcile dates | Workspace-timezone current/prior equal-duration periods with scope returned |
| Calculate | treat order value/ad spend as full economics | Finance collection, persisted COGS and declared inputs with status/reasons; no unsupported allocation |
| Interpret | eyeball trends or rely on provider purchase figures | categorical thresholds, exact-vs-provider evidence distinction, bounded non-causal prompts |
| Decide | assign work through other operating tools | suggestion points to review; owner domain and human authority remain separate |
| Learn | manually compare outcomes | immutable evidence and Merchant feedback are stored, but no automatic outcome evaluation/learning loop found |

These alternative workflows are reasoned examples, not measured merchant research.

## 13. Hidden / Non-Obvious Advantages

- Analytics has explicit incompleteness semantics across finance/cost/ad spend and often preserves independent operational metrics when a financial input is incomplete.
- Finance collections, not OrderItem selling amounts alone, anchor realized revenue. Inventory allocations are consumed as recorded, not rebuilt inside Analytics.
- Advertising measures one compatible selected connection/account and protects against conversion/provider mix-ups.
- Recommendation generation has meaningful evidence identity, idempotent replay and append-only feedback structure; this is an auditability foundation, not yet learning.
- Admin Platform Analytics and Merchant Analytics keep separate audiences, permissions and semantics instead of making an Admin report appear to be merchant decision support.

## 14. Data & Intelligence Assets

The system can bring together scoped Orders and status histories, Merchant Customer relationship labels, Finance collection/fee facts, Inventory allocation and cost-layer facts, Product/day ad-spend declarations, expenses, selected-account advertising slices and exact attribution, Market Center published aggregates, and immutable recommendation snapshots/interaction events. This is a substantial data substrate.

It does not by itself prove a reliable cross-domain intelligence graph. Cost layer `unit_cost` is read as current value, so later cost edits can change historical Analytics COGS; no immutable allocation-time cost snapshot was established. Merchant Customer labels/block state are read from the current linked relationship when grouping historical Orders; the evidence is not a point-in-time reputation snapshot. Recommendation outcome rows are only an internal typed seam absent a scheduler/evaluator. Thus Data → Connection → Pattern is partial, Decision is deterministic review support, Action is external/manual, and Learning is NOT ESTABLISHED.

## 15. Cross-Section Compound Advantages

- **Orders × Finance × Inventory × Analytics:** realized collection + lifecycle cohort + persisted FIFO allocation supports qualified profitability, subject to costs/currency/maturity.
- **Advertising × Orders × Tracking/Finance:** selected-account exact attribution plus downstream lifecycle and collection can build outcome-based advertising evidence when each link is authoritative and complete; full profit attribution remains conditional.
- **Customers × Orders × Analytics:** scoped Merchant relationship-status groups and order outcomes can suggest a cautious review pattern; current-state labels, small denominators and non-causality constrain interpretation.
- **Market Center × Merchant cohort × Decision Center:** published aggregate category activity combined with a sufficiently mature local cohort can surface a narrow review opportunity, not market demand or expansion profitability.
- **Home × Analytics:** canonical, bounded handoff increases access to guidance without making Home an independent decision engine.

## 16. Competitive Analysis

The competitive master identifies basic operational analytics/dashboard visibility as category infrastructure. It documents MDM operational statistics, COD Network API/statistics breadth, COD Mastery operational benchmarks, Fufills monitoring, and Ximpli profit/AI positioning. SSAS COD advertises analytics/profit tracking but is explicitly low verification. The baseline is dated and not freshly checked.

Potential distinctiveness is not “has analytics,” a dashboard, AI, benchmark, Profit card or recommendation label. The possible category advantage is a connected, authority-aware chain from exact advertising evidence through Wossol order/fulfillment/Finance facts into conservative and inspectable merchant guidance. Current code establishes meaningful foundations, but outcome completeness, recommendation usefulness, runtime data and action/outcome learning remain unverified. Classification: **TABLE STAKES** for basic reporting; **POTENTIAL DIFFERENTIATOR** for evidence-qualified cross-domain guidance; **INSUFFICIENT EVIDENCE** for superiority, moat, competitor absence, or proven decision advantage.

## 17. Marketing Intelligence

**Asset ANL-01 — Know which numbers are incomplete.** Evidence: EV-ANL-007–009. Eligibility: SUPPORTING PROOF ONLY. Angle: show missing cost/ad evidence and unsupported allocation explicitly. Qualification: does not establish completeness or accuracy of all business costs.

**Asset ANL-02 — Advertising outcomes beyond provider Purchases.** Evidence: EV-ANL-010. Eligibility: FUTURE MARKETING TERRITORY / SUPPORTING PROOF ONLY. Angle: demonstrate exact attribution and distinct confirmed/delivered Wossol outcomes when a compatible account/cohort exists. Qualification: no guarantee of full profit; provider account, evidence, attribution and Finance completeness required.

**Asset ANL-03 — Explainable review suggestions.** Evidence: EV-ANL-011–014. Eligibility: SUPPORTING PROOF ONLY. Angle: show bounded evidence, stated owner, suggested human review and immutable generation record. Qualification: suggestions are deterministic, not causal, executed, measured or learned; current test-order scope risk must be resolved before representative demonstration.

Safe current territory is **scoped, evidence-qualified analytics with bounded operational guidance**, subject to Test Order and runtime limitations. Avoid “AI decision engine,” “real profit,” “automatically optimizes,” “learns what works,” “predicts,” “fully reconciled ad-to-profit,” “market demand intelligence,” or outcome guarantees.

## 18. Surprise Findings

The Merchant system includes more than operational charts: Product/day spend completeness, persisted Inventory cost provenance, selected-account advertising joins, immutable recommendation generation evidence, and a separate internal Admin analytics workspace. Yet the current implementation still has an important mismatch between test-order lifecycle policy and Analytics cohort selection, and a durable outcome schema without an automatic evaluator. The contrast is strategically useful: analytical rigor is emerging, but it is not yet a closed-loop intelligence system.

## 19. Potential Category Reframes

Possible future territory: **commerce decisions grounded in the evidence that actually determines outcomes**—operational lifecycle, attributable acquisition spend and collected/cost facts—rather than isolated dashboard metrics. Current supported phrasing should stay closer to **scoped analytics and evidence-qualified operational guidance**. The broader reframe depends on resolving Test Order scope, historical cost/status semantics, production evidence availability and a real recommendation outcome loop.

## 20. Brand Evidence

- **Clarity:** explicit metric status/reasons and known-vs-unknown states.
- **Accountability:** scoped source facts and immutable recommendation evidence/feedback events.
- **Restraint:** no invented FX/allocation, no provider Purchases silently relabelled as Wossol Orders, and suggested actions do not execute.
- **Connectedness:** multiple owner-domain facts can be presented within one authorized period and scope.
- **Guidance (emerging):** deterministic, bounded review suggestions are available where evidence thresholds qualify.

These are product evidence hypotheses, not final brand positioning or proof of merchant trust/outcome.

## 21. Weaknesses / Risks / Gaps

1. **Test Order contamination risk:** active `MerchantAnalyticsService` `orderWhere` omits `isTestRecord: false`, unlike a separate older `AnalyticsService`. Test Orders can therefore enter the current Merchant operational and financial cohort; thresholds and cohort maturity can be affected. Resolve the intended contract and correct/verify the active route before relying on commercial analytics.
1a. **Product/Variant revenue-scope mismatch:** Product/Variant scope narrows selected OrderItems and their Inventory allocations, but top-level cohort revenue sums each matched Order's entire Finance collection. For mixed-item Orders, filtered top-level revenue/gross profit do not represent selected-item economics. Direct Product/Variant breakdown rows separately use OrderItem selling-value snapshots; do not interpret filtered top-level totals as product economics until scope semantics are corrected or qualified.
2. **Historical COGS mutability:** Analytics reads current cost-layer unit cost through historical allocation records; updating a cost can change historical profitability without rewriting allocation quantity. No historical cost snapshot was established.
3. **Customer status temporal leakage:** current `MerchantCustomer.reputationLabel`/`isBlocked` is grouped over cohort Orders. This does not preserve what the status was when each Order occurred; performance association can move after the fact.
4. **Recommendation learning gap:** ACCEPTED is intent only; no execution/outcome scheduler/evaluator or causal measurement is established. Outcome schema alone is not learning.
5. **Profitability coverage limits:** missing daily ad spend makes metrics incomplete; shared/Store/Variant/Region allocation and non-attributable fees/expenses are unavailable. Returns do not automatically reverse COGS or restock without authoritative provider evidence.
6. **Market opportunity narrowness:** Libya-only fixed published period, minimum thresholds and fail-soft behavior; snapshot activity is not national demand, product-market fit, causal growth or profit evidence.
7. **Operational completeness/maturity:** current-status/history aggregation is created-cohort reporting, not event-time reporting; immature cohorts may be provisional. Current status history behavior, duplicate/re-entry nuances and production data quality need representative checks.
8. **Current versus historical docs:** `ENG-MERCHANT_ANALYTICS_V1A.md` describes older/deferred operational-only semantics (including simpler confirmation denominator and no Finance/AI scope); current financial/UI specs and active implementation cover broader behavior. Label/maintain old engineering baseline so it is not mistaken for current contract.
9. **Runtime/database state:** repository source and migrations do not prove migration application, environment flags, API deployment, permission configuration, provider sync, stored evidence or Merchant-visible behavior.
10. **Admin Analytics:** internal Dashboard breadth is operational and bounded; no warehouse/BI export, company P&L, predictive engine or Analytics-triggered operations are established.
11. **Testing scope:** focused Analytics tests pass, but the broad backend test command also emitted unrelated disabled-write rehearsal failures; no production or authenticated browser acceptance occurred.

## 22. Future Strategic Potential

- **Current foundation:** scoped cohorts, domain-owned financial/cost facts, explicit completeness, selected-account provider evidence, deterministic review actions and immutable generation/interaction records.
- **Approved direction:** the current product UI/financial contracts define the Analytics and Decision Center behaviors described in this audit; this does not approve broader autonomous execution or intelligence claims.
- **Inferred potential:** resolve test-data exclusion, preserve point-in-time cost/customer evidence, offer calibrated explanations, then evaluate recommendation outcomes against a defensible baseline and human-confirmed action evidence.
- **Strategic relevance:** a genuinely linked advertising → order → delivery → collection/cost → decision loop could exceed basic operational dashboards if completeness and usefulness are demonstrated.
- **Brand relevance:** clarity/accountability/guidance could scale, but today remain evidence-backed qualities and hypotheses rather than a finished promise.

## 23. Claim Safety

| Claim | Status | Qualification |
|---|---|---|
| Merchant Analytics is scoped and timezone-aware | GREEN, source-supported | deployment/runtime behavior not checked |
| Analytics can present evidence-qualified profitability | YELLOW | only attributed/available authoritative inputs; Test Order filtering risk; incomplete states common |
| Advertising Analytics can connect spend to downstream Wossol outcomes | YELLOW | selected account, exact compatible attribution and complete lifecycle/Finance evidence required |
| Decision Center provides bounded, deterministic suggested reviews | YELLOW | advisory only, thresholds are deterministic, no action execution or proven outcomes |
| Analytics learns from accepted recommendations | RED / DO NOT CLAIM | no evaluator/learning loop; Accept only records intent |
| Wossol knows true/real profit or market demand | RED / DO NOT CLAIM | attribution/cost coverage and authority gaps; market signals represent bounded Wossol-observed activity |
| Wossol has better analytics/AI than named competitors | RED / DO NOT CLAIM | competitor master is dated and depth evidence insufficient |

## 24. Commercial Magnitude

- **FOUNDATIONAL:** trusted Workspace/Store scope and truthful incomplete-vs-zero distinction.
- **HIGH LEVERAGE:** Finance + Inventory + Ads + lifecycle integrations when complete and correctly attributable.
- **SUPPORTING:** deterministic comparisons, Business Health dimensions and next-step guidance.
- **MICRO-PROOF:** recommendation evidence hashes/replay, audit records for cost inputs, and explicit timezone handling.

Magnitude does not establish competitive differentiation or measured commercial result.

## 25. Strategic Classification

| Dimension | Classification |
|---|---|
| Merchant value | Medium-to-high potential; current value is scoped synthesis and honest completeness, not measured decision improvement |
| Competitive distinctiveness | Basic analytics is table stakes; qualified cross-domain outcome guidance is a potential differentiator |
| Control depth | Level 1 visibility plus bounded Level 4 guidance; no Level 5 intelligent control |
| Intelligence maturity | Connected data / deterministic interpretation / recommendation foundation; execution and learning not established |
| Evidence confidence | High for source behavior and focused tests; low/unverified for production data/runtime, recommendation impact and adoption |
| Brand evidence | Clarity, accountability, restraint, connectedness; guidance emerging |
| Moat potential | Possible accumulated data/feedback only if evidence quality and valid outcome loop are established; no present moat claim |

## 26. Action Register

| Priority | Action | Owner / dependency | Rationale |
|---|---|---|---|
| MUST MATCH | Decide whether Test Orders must be excluded from commercial Merchant Analytics, then align P1 active service, tests, and current contract | Product + Analytics/Orders | Prevent validation records from distorting conversion, health, profitability and recommendations |
| MUST MATCH | Decide mixed-item Order behavior for Product/Variant-filtered top-level revenue and gross profit; align calculation, labels and mixed-item tests | Product + Analytics + Finance | Prevent whole-Order collection from being compared with selected-item COGS |
| MUST MATCH | Document/implement point-in-time semantics for cost-layer unit costs and Merchant Customer status in historical Analytics | Analytics + Inventory/Customers | Historical comparisons should not silently change when current source attributes change |
| WORTH ADOPTING | Add representative fixtures for Test-vs-real Orders across operational, Decision Center, and profitability projections | Analytics/Orders | Catch cross-section cohort leakage |
| WORTH ADOPTING | Add an explicit recommendation outcome evaluator only with action evidence, baseline, evaluation window and non-causal guardrails | Product/Analytics | Convert schema foundation into testable learning without equating feedback intent with success |
| WORTH ADOPTING | Keep engineering baseline docs clearly versioned/archived against current financial/UI contract | Analytics documentation owner | Reduce confusion from V1A deferred scope and changed denominator semantics |
| WORTH ADOPTING | Validate source quality and sample sizes against production-like, privacy-safe cohorts | Product/Data | Thresholds/tests cannot establish reliability at real volume |
| NOT CLAIM | Do not market accurate real profit, full ad-to-profit, automatic optimization, AI learning, national demand or proven competitor advantage | Marketing/Product | Current evidence does not support these claims |

## 27. Evidence Register

- **EV-ANL-001 — Active Merchant Analytics route and provider.** **Type:** P1. **Paths:** `apps/backend/src/modules/analytics/analytics.controller.ts`, `analytics.module.ts`, `merchant-analytics.service.ts`; `apps/frontend/src/app/merchant/analytics/analytics-experience.tsx`. **Observed:** `/merchant/analytics` uses authenticated Merchant controller and registered `MerchantAnalyticsService`; merchant UI loads the richer experience. Separate `AnalyticsService` file is not the active module provider. **Confidence:** High.
- **EV-ANL-002 — Workspace, Store and entity scope.** **Type:** P1. **Path:** `merchant-analytics.service.ts` (`scope`, `merchantActor`, Store/Product/Variant scope checks). **Observed:** requires active Merchant user/account, Analytics section access, active Workspace membership and active Stores, resolves restricted Store IDs and validates Product/Variant within authorized scope. **Confidence:** High.
- **EV-ANL-003 — Merchant user surface.** **Type:** P1/P2. **Paths:** `apps/frontend/src/app/merchant/analytics/analytics-experience.tsx`, `advertising-analytics.tsx`, `profitability-experience.tsx`; `apps/frontend/src/app/merchant/analytics/`. **Observed:** current experience contains Decision Center, operations, profitability inputs/breakdowns and Advertising; card controls are advisory and write only analytics inputs/feedback. Source components inspected; no live browser session. **Confidence:** High for source, not runtime.
- **EV-ANL-004 — Period and created cohort.** **Type:** P1/P2. **Paths:** `analytics-period.ts`, `merchant-analytics.service.ts:getMerchantAnalytics`; `analytics-period.spec.ts`, `merchant-analytics.service.spec.ts`. **Observed:** default last seven Workspace calendar days; bounded custom dates and exclusive UTC end; immediately preceding equal-duration cohort; orders selected by createdAt; lifecycle history/current status determines outcomes; rates expose authoritative denominators. **Confidence:** High.
- **EV-ANL-005 — Comparison and health rules.** **Type:** P1/P2. **Paths:** `analytics-decision-center.ts`, `analytics-decision-center.spec.ts`. **Observed:** count/percentage-point comparisons; financial deltas only on COMPLETE evidence for both periods; deterministic categories and explicit denominator thresholds; incomplete/provisional profitability cannot yield definitive profitability classification. **Confidence:** High for implemented rules; thresholds not validated against production utility.
- **EV-ANL-006 — Test Order exclusion discrepancy.** **Type:** P1/P2 + cross-section P1. **Paths:** active `merchant-analytics.service.ts` orderWhere and `operational`; non-registered `analytics.service.ts`; `apps/backend/prisma/schema.prisma` Order `isTestRecord`; `02-section-intelligence/ORDERS.md` EV-ORD-006–007 and `PRODUCTS.md` EV-PROD-016–017 Test Product boundary. **Observed:** active current/prior Order query filters Merchant/Workspace/Store/Region/item/date and pre-confirmation deletion, but not `isTestRecord: false`; the separate legacy `AnalyticsService` does set that predicate but is not registered by AnalyticsModule. Orders treats Test Orders as validation-only/never dispatched. Active Merchant Analytics tests inspect same query scope but do not assert test exclusion. **Conclusion:** probable contract/implementation mismatch with potential denominator and profitability contamination; intended Analytics treatment requires Product resolution. **Confidence:** High for query difference; impact depends on actual Test Order presence and intended contract.
- **EV-ANL-007 — Revenue/COGS/fee authority.** **Type:** P1/P2. **Paths:** `merchant-analytics.service.ts:profitability`; Prisma `FinancialCollection`, `FinancialOperationalFeeAssessment`, `InventoryCostAllocation`, `InventoryCostLayer`; `docs/analytics/MERCHANT_ANALYTICS_FINANCIAL_MODEL.md`. **Observed:** Finance collection facts anchor realized revenue; COGS uses persisted allocations multiplied by layer unit_cost; currency mismatch/incomplete cost fails closed; fees come from Finance assessments. No FIFO reconstruction or selling-price-as-COGS. **Confidence:** High for formula/source; external cash truth unverified.
- **EV-ANL-008 — Expense/ad-spend semantics.** **Type:** P1/P2. **Paths:** `analytics-financial.ts`, `merchant-analytics.service.ts` dailySpend/expenses/input APIs; migrations `20260819_merchant_analytics_financial_inputs_v1`, `20260824_merchant_product_daily_ad_spend_v1`; `analytics-financial.spec.ts`; current financial/UI specs. **Observed:** explicit SPEND/DECLARED_ZERO and missing UNKNOWN; Product/day scope; temporary period value calculation-only; overlap, missing dates, Store ambiguity, shared costs and Variant/Region allocation do not become invented values; writes emit audit evidence. **Confidence:** High for source/tested semantics.
- **EV-ANL-009 — Profitability denominator/return limits.** **Type:** P1/P2. **Paths:** `merchant-analytics.service.ts:profitability`, `analytics-financial.ts:productUnitEconomics`; current financial model and UI spec. **Observed:** returns do not reverse COGS or restore stock without authoritative provider restock; additional expenses are not segmented; Product direct contribution excludes unallocated Workspace costs; operation fees spanning multiple Products are unavailable. **Confidence:** High.
- **EV-ANL-010 — Advertising evidence projection.** **Type:** P1/P2. **Paths:** `merchant-analytics.service.ts:getAdvertisingAnalytics`, `advertisingDecisionEvidence`; Advertising UI/spec and `merchant-analytics.service.spec.ts`. **Observed:** persisted Meta only; one selected compatible connection/account; provider calendar/timezone is explicit; EXACT evidence required for attributed Orders; provider purchases separated; Confirmed/Delivered ROAS requires compatible Wossol outcomes and Finance collections; no provider call or attribution mutation. Focused tests include timezone, exact attribution, account and failure states. **Confidence:** High for source/tests, production data unavailable.
- **EV-ANL-011 — Customer Quality and Performance Impact.** **Type:** P1/P2. **Paths:** `merchant-analytics.service.ts:customerQualityImpact`, `analytics-decision-center.ts:performanceImpact`, presentation helpers and tests. **Observed:** aggregated Merchant Customer statuses only; current linked relationship labels and manual block flag are applied to historical cohort Orders; cancellation stage and return evidence are separated; minimum sample conditions and non-causal wording. **Confidence:** High.
- **EV-ANL-012 — Next Best Actions.** **Type:** P1/P2. **Paths:** `analytics-decision-center.ts`, tests, current UI spec. **Observed:** finite deterministic action candidates, rank by priority/stable key, cap three, conservative owner, evidence/reasons and suggested action; no assignment/notification/mutation; Market Opportunity requires qualified fixed-period snapshot. **Confidence:** High for code behavior.
- **EV-ANL-013 — Current Market Center opportunity.** **Type:** P1/P2/P3. **Paths:** `merchant-analytics.service.ts:personalizedMarketOpportunity`; `DECISION_RECOMMENDATION_FEEDBACK_P0_07.md`; focused H-C04 service tests. **Observed:** optional Libya `LAST_30_DAYS` published snapshot, first five categories and minimum-five unique real/non-test/non-deleted orders plus final/threshold-qualified category cohort; catches errors and omits suggestion. This branch explicitly excludes test records, unlike main cohort path. **Confidence:** High for source boundary; market signal quality/runtime not verified.
- **EV-ANL-014 — Durable recommendations, feedback, Home handoff.** **Type:** P1/P2/P3. **Paths:** `decision-recommendation-feedback.ts`, `merchant-analytics.service.ts:materializeRecommendations`, feedback/outcome methods, Prisma recommendation models, migration `20260930_decision_recommendation_feedback_p0_07`, feedback UI, `DECISION_RECOMMENDATION_FEEDBACK_P0_07.md`. **Observed:** canonical hash/generation identity, unique/replay handling, immutable snapshots, event append and latest feedback projection; Merchant events limited to SHOWN/VIEWED/ACCEPTED/DISMISSED; internal outcome write seam has no evaluator; Home method calls canonical seven-day path and returns at most three safe fields with Analytics URL. **Confidence:** High for source; production migration/runtime and real outcomes not verified.
- **EV-ANL-015 — Internal Admin Platform Analytics.** **Type:** P1/P2/P3. **Paths:** `apps/backend/src/modules/admin-platform-analytics/`, `apps/frontend/src/app/admin/analytics/`, `docs/wossol-system-design/01-system-design/admin-platform/platform-analytics/README.md`. **Observed:** distinct authenticated Admin/permission-gated concrete-Workspace reporting projections and filters; lifecycle outcomes are event-time distinct Orders, unlike Merchant created-cohort current/history view; no company P&L, cross-Workspace aggregation or automated action. Source/spec inspected; no production Admin session. **Confidence:** High for source contract.
- **EV-ANL-016 — Current and historical Analytics contracts.** **Type:** P2/P3. **Paths:** `docs/analytics/MERCHANT_ANALYTICS_FINANCIAL_MODEL.md`, `docs/ui/merchant/MERCHANT_ANALYTICS_UI_SPEC.md`, `docs/engineering/ENG-MERCHANT_ANALYTICS_V1A.md`, historical `Analytics System (v2).md`. **Observed:** current financial/UI specs bound current cohort, completeness, attribution, Decision Center, customer impacts and expenses; old V1A defers finance/AI and uses simpler rate semantics; historical v2 expressly contains superseded statements. Do not use older intent as current behavior. **Confidence:** High for documents' content; product-authority resolution of Test Order inclusion still open.
- **EV-ANL-017 — Schema/migration source.** **Type:** P1/P2. **Paths:** `apps/backend/prisma/schema.prisma`, Analytics migrations and archived migration history. **Observed:** schema defines recommendation/event/outcome, Finance and cost models; Product/Workspace constraints and indices present. Repository migration artifacts exist, but actual target database migration application was not inspected. **Confidence:** High for repository, none for deployment.
- **EV-ANL-018 — Verification observed.** **Type:** P2. **Commands:** focused backend analytics specs (six files) via Node test runner; backend/frontend typechecks. **Observed:** 99 focused Analytics tests pass; both `npm run typecheck` commands exit successfully. A broader backend invocation's observed output showed three failing disabled-write Target B rehearsal cases; the complete full-suite summary was not captured, so no claim that the full repository suite is green is made. No authenticated browser/database/provider integration test. **Confidence:** High for focused test/typecheck results; broad run incomplete.
- **EV-ANL-019 — Filtered Order collections versus selected item costs.** **Type:** P1. **Path:** `apps/backend/src/modules/analytics/merchant-analytics.service.ts` (`orderSelect`, `getMerchantAnalytics`, `profitability`). **Observed:** Product/Variant `itemFilter` narrows returned `orders.items`; COGS queries allocations only for those selected item IDs. Top-level realized `revenue` sums each matching Order's whole Finance collection. Direct Product/Variant breakdown rows separately calculate from OrderItem selling-value snapshots. **Conclusion:** filtered top-level revenue/gross profit can mix full-order realized revenue with selected-item COGS in multi-item Orders. **Confidence:** High for implementation; user interpretation and frequency depend on filters/order mix.

## 28. Contradictions & Uncertainty

### CONTRADICTION-ANL-001 — Test Order cohort eligibility

- **Source A:** `docs/ui/merchant/MERCHANT_ORDERS_UI_SPEC.md` says Test Orders are immutable-purpose validation Orders that require Confirmation and will not be sent to delivery; current Analytics financial model describes a real created-order cohort and actual lifecycle outcomes. Product section intelligence EV-PROD-016–017 further separates Test Product eligibility from commercial Analytics behavior.
- **Source B:** Active `MerchantAnalyticsService` cohort predicate omits `isTestRecord: false`; tests do not assert exclusion. The separate legacy `AnalyticsService` explicitly excludes test records but is not wired in `AnalyticsModule`.
- **Nature of conflict:** Intended/current commercial Analytics eligibility versus active executable query scope.
- **Evidence strength:** P1 active route/query and schema; cross-section P1/P2 Orders evidence; current P3 financial model lacks explicit Test Order treatment. Legacy code is P1 code but inactive for the route.
- **Working conclusion:** Current active code includes Test Orders if they otherwise match query filters. This current-truth statement does not resolve whether that is intended product behavior or an implementation defect.
- **Remaining uncertainty:** No current Analytics contract explicitly states whether test-purpose Orders belong in operational summaries, rates, cohorts, customer impacts and profitability. Product owner must decide; if excluded, test expectations must cover all dependent calculations and recommendation rules.
- **Required verification:** Resolve contract; add explicit fixtures asserting intended test/real behavior across active Merchant Analytics; confirm no deployment-only filter exists.

### CONTRADICTION-ANL-002 — Older engineering scope versus current Analytics contract

- **Source A:** `ENG-MERCHANT_ANALYTICS_V1A.md` describes operational-only Analytics, simpler denominators, and financial/AI/recommendation features as deferred.
- **Source B:** Active code plus current Merchant Analytics financial/UI specifications implement Profitability, Advertising, Decision Center and recommendation persistence.
- **Nature of conflict:** Versioned prior scope is stale relative to current implemented/specification scope, and can mislead future maintainers if read as live contract.
- **Evidence strength:** P1 active implementation, current P2/P3 product specs, older P2 engineering doc.
- **Working conclusion:** V1A is an earlier baseline, not a complete description of current Merchant Analytics. Historical V2 draft also announces superseded sections. Avoid citing either as current capability truth.
- **Remaining uncertainty:** Documentation owner should maintain explicit supersession/version metadata and synchronize the Test Order rule.
- **Required verification:** Product documentation owner confirms canonical current contract and marks V1A superseded where appropriate.

### Other material uncertainty

Production migration application, record quality, provider account compatibility, current permission setup, current label/cost history, merchant decision usefulness, Market Center signal representativeness and real action outcomes were not available for verification.

### CONTRADICTION-ANL-003 — Product/Variant-filtered financial scope

- **Source A:** Product/Variant filters narrow the selected OrderItem projection and cost-allocation evidence; direct Product/Variant unit-economics rows use item-level selling-value snapshots.
- **Source B:** top-level Analytics realized revenue sums each qualifying Order's whole Finance collection, regardless of the narrower item filter.
- **Nature of conflict:** A filtered cohort can combine full-order realized revenue with only selected Product/Variant COGS, yielding a top-level gross-profit measure that is not selected-item profitability.
- **Evidence strength:** P1 active service query and calculation path.
- **Working conclusion:** Do not interpret Product/Variant-filtered top-level Revenue/Gross Profit as Product/Variant unit economics for mixed-item Orders. Direct unit-economics rows are separately calculated; this does not remove the top-level scope mismatch.
- **Remaining uncertainty:** Intended UI semantics and treatment when a matched Order contains multiple Products/Variants are not explicit enough to establish whether this is defect or accepted “orders containing item” reporting.
- **Required verification:** Product authority should choose selected-item value, unmistakably labelled Order-level totals, or fail-closed/unavailable for mixed-item financial rollups; add mixed-item tests.

## 29. Open Questions

1. Are Test Orders intentionally included in the active Merchant Analytics operating/profitability cohort, or is the missing exclusion a defect? What exact semantics should apply per tab and per denominator?
2. Should COGS use current corrected cost-layer unit cost for all historical cohorts, or should allocation-time cost be immutable for period comparisons?
3. Should Product/Variant-filtered top-level financial summaries use full Order collections, selected item value, or an unavailable state when Order-level collection cannot be allocated?
4. Should historical Customer Quality Impact use current status by design, or should point-in-time Merchant Customer status snapshots be preserved?
5. Is the P0-07 outcome schema used by any external evaluator/deployed process outside this repository? None is established in inspected source/docs.
6. Are Analytics, DecisionRecommendation and recent Product input migrations applied to production and backed by representative, privacy-safe data?
7. Do merchants understand and find the distinctions between provisional, incomplete, unavailable, provider purchase, attributed Order and Finance collection evidence useful?

## 30. Methodology Learnings

No general methodology change is proposed. This audit reinforces the existing scope-map and cross-section rules: when a domain labels records as test/validation-only, trace the exclusion predicate in each active consumer rather than relying on a parallel service or an owner-domain promise. Because this is an application-specific verification reminder already covered by Exhaustive Discovery and Cross-Section Analysis, no changelog or retroactive queue change is warranted.

## 31. Retroactive Review Impact

No earlier section requires re-audit. This audit qualifies the Home section's recommendation handoff: it demonstrates bounded Analytics guidance distribution, not proof of accurate commercial inputs, current test-order exclusion, recommendation impact or learning. Existing Home claims should remain limited to its own composition behavior. Record the Test Order Analytics eligibility resolution in the future Analytics/Product decision; do not recursively revise completed sections without a specific affected claim.

## 32. Canonical Section Takeaway

**Analytics is a scoped synthesis and bounded guidance layer, not yet an intelligent decision-and-learning system.** Its strongest defensible current value is combining operational, Finance, Inventory and selected Advertising evidence while exposing missingness and refusing many unsupported allocations; it can produce deterministic recommendations with durable provenance, but those recommendations remain human review prompts. The active Merchant cohort's missing Test Order exclusion, filtered whole-Order revenue versus selected-item cost mismatch, mutable historical cost/customer attributes, unverified runtime data, and absent automatic outcome evaluator materially qualify profitability and decision-intelligence claims. Admin Platform Analytics is a separate, internal operational reporting product. No superiority, improved outcome, real-profit completeness, autonomous optimization, or moat claim is established.
