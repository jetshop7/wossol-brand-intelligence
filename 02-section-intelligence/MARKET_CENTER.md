# Market Center — Section Intelligence

## 1. Audit Metadata

- **Audit date:** 2026-09-26.
- **Methodology:** `00-methodology/MASTER_INSTRUCTIONS.md` v1.1; current operating protocol.
- **Competitive reference:** `01-competitive/WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md` v1.
- **Intelligence source:** `jetshop7/wossol-brand-intelligence`, `main`, `9d0f0b7dacb43696a28a5d987bbbf7a1224e1ea0`, clean and synchronized with `origin/main` before inspection.
- **Product source:** `jetshop7/wossol-platform`, `dev/wossol-integration`, `8600a4cbd1a894579a057b3476db35465289c670`, clean; local source inspected without edits.
- **Evidence basis:** P1 executable source/schema, P2 focused Market Center tests and typechecks with execution status recorded, P3 Merchant Market Center UI specification, P4 system-design/product-execution notes where qualified, and official external source verification for material catalog facts. Static source inspection does not establish deployed behavior, production data volume, runtime latency, merchant adoption, or commercial outcome.

## 2. Audit Coverage Map

| Surface | Coverage | Evidence |
|---|---|---|
| Merchant Market Center UI | route, availability, API request, presentation, loading/error and signals | EV-MKT-001–002 |
| API access and external catalog | authenticated request, active Merchant/Workspace scope, Libya-only catalog, period and response contract | EV-MKT-003–004 |
| Cross-Workspace aggregation | cohort filters, category history, suppression, ordering and safe snapshot | EV-MKT-005–008 |
| Taxonomy, Orders and lifecycle evidence | category assignment/schema history, Order test/deletion and active-line policy | EV-MKT-006, EV-MKT-009 |
| Tests and source state | focused tests/typechecks and source-state verification | EV-MKT-010 |
| External facts and competition | catalog facts checked against official primary sources; available competitor evidence from competitive master | EV-MKT-011–012 |

Not inspected as live fact: production database/cohort counts, production endpoint behavior, merchant usage, current CBL/IMF source changes beyond checked official publications, or competitor private capabilities.

## 3. Executive Section Truth

Market Center is currently two carefully separated things: a static, externally sourced Libya market briefing, and a read-only Wossol-observed activity signal that reports suppressed eligible ordered units by historical product category. The second is genuinely a cross-Workspace aggregate, but not a survey or estimate of Libya-wide demand. Neither part establishes merchant-specific category opportunity, delivered sales, retained revenue, margin, customer preference, market share, or prediction.

The strongest product quality is the narrow publication boundary: authorized active Libya Workspace access; a market-owned Libya period; historical category attribution; exclusion of test and specially deleted orders; centralized diversity/volume suppression before top-12 selection; and a response that omits counts, identities, and raw evidence. Its principal weakness is not the algorithmic boundary but the static external catalog's freshness and traceability: the UI exposes publisher/title/period but no clickable source URL, and several macro values remain tied to a 2025 IMF vintage even though official 2026 IMF context is available.

## 4. Scope & Architecture Map

Merchant navigation and presentation live under `apps/frontend/src/app/merchant/market-center/`. The authenticated endpoint is `/merchant/market-center`; `MerchantMarketCenterService` authorizes the requesting active Merchant user and active Workspace membership, then selects the country-owned catalog. `MarketIntelligenceService` owns the cross-Workspace SQL aggregation and suppression boundary. Analytics may consume only its explicit safe snapshot rather than reproducing the query; Home can surface the separate Analytics recommendation, not the raw Market Center cohort. Products owns category assignment history, Orders owns order/item purpose and lifecycle records, while Market Center owns only the published aggregate projection. No Market Center persistence, worker, external fetch, or scheduled refresh is present in the inspected contract.

## 5. Current Capability Inventory

| Capability | Status | Current behavior |
|---|---|---|
| Libya external market briefing | LIVE, static | Backend-owned version 1.1 catalog for active `LY` Workspaces; explicit data periods and source records |
| Workspace authorization | LIVE in source | Active merchant user/account, active merchant-workspace link, active Workspace and active non-revoked user membership required |
| Store-independent availability/request | LIVE in source | Country is the availability gate; request carries active `workspaceId`, not Store ID |
| Wossol observed category signal | LIVE in source | Eligible observed ordered units by historically assigned canonical category for Libya `LAST_30_DAYS` |
| Privacy publication safeguards | LIVE in source | Minimum eligible lines and independent Workspace/Merchant counts; suppression before sorted top-12; no raw cohort fields in result |
| National demand, market share, benchmark, prediction | NOT IMPLEMENTED | The signal contract expressly disclaims national representativeness and contains no market denominator or forecasting logic |
| Merchant-specific category opportunity | NOT Market Center-owned | Optional Analytics H-C04 consumer may combine the snapshot with a separate merchant cohort; that is not Market Center's raw signal and remains subject to Analytics status/evidence |
| Automatic refresh/source ingestion | NOT IMPLEMENTED | Static catalog; no external fetch, CMS, scheduler, or source refresh path established |

## 6. Workflow & Lifecycle

1. Merchant navigation is available for a Libya Workspace; selected Store does not change availability.
2. Frontend requests `/merchant/market-center?workspaceId=...` and sends no Store selector. Backend authenticates, resolves active Merchant membership, then verifies the Workspace and active membership link.
3. Backend returns the approved Libya catalog only for `LY`; unsupported countries receive unavailable rather than a Libya fallback.
4. It resolves a fixed `LAST_30_DAYS` period in the backend-owned `Africa/Tripoli` market timezone.
5. Read-only aggregation selects active lines on non-test orders created in the period, across active Libya Workspaces, excluding the immutable merchant-delete-before-confirmation marker.
6. Each line joins the Product category assignment valid at item creation and the category lifecycle active then; unassigned historical lines remain unclassified.
7. Category totals are discarded unless line and independent-tenant safeguards pass; the surviving cells are sorted by ordered quantity descending then code ascending and capped at 12.
8. UI presents Wossol-observed activity separately from the external briefing, with backend-provided period end and a safe insufficient-evidence state. It does not calculate suppression.

## 7. Value Recipient Map

| Recipient | Current value |
|---|---|
| Libya merchant | concise sourced macro/payment context plus a qualified view of categories with enough observed Wossol order activity |
| Merchant owner/manager | a bounded, read-only signal without exposing other tenants or the hidden suppression cohort |
| Product/category owner | historical canonical taxonomy attribution, preserving interpretation across later reclassification |
| Analytics | one Market Center-owned, privacy-safe source snapshot for optional downstream evidence; no duplicated aggregation |
| Wossol | potential foundation for evidence-led market context, but no established market-research or commercial-outcome product today |

## 8. Control & Merchant Agency

Merchant agency is low and appropriate to an informational surface: authorized users can view market context but cannot tune the cohort, query a Store, inspect source rows, change category assignment, or trigger a refresh. Store independence avoids falsely implying a local Store sample. This is observation/interpretation, not operational control or an intelligent recommendation engine.

## 9. Transparency & Trust

- External metrics carry source keys, publisher, title and data period; the UI shows source caption and source details. It does not link the source record to a direct URL.
- Wossol signals state that they are eligible activity observed across Wossol and do not represent the whole Libya market; ordered units are not described as fulfilled sales.
- Period is explicit and formatted in the backend-provided market timezone; technical timezone text and suppression counts stay hidden.
- Insufficient data yields only the safe unavailability message; suppressed counts and tenant identities do not appear in the snapshot type.
- Version/review date/freshness note are visible, but the catalog does not refresh itself. These fields cannot guarantee that every source remains current.

## 10. Merchant Value Extraction

For a Libya merchant, the briefing provides convenient macro and payment context without requiring separate research, while the internal signal offers a narrow, privacy-protected sense of which categories have enough recent observed order activity within Wossol. The signal may prompt further investigation; it cannot tell an individual merchant that a category is growing nationally, profitable, deliverable, underserved, or suitable for their Store.

## 11. Feature Clusters

1. **Country-gated market briefing:** Workspace authorization + Libya catalog + explicit time periods and provenance.
2. **Privacy-safe observed activity:** market-clock cohort + historical taxonomy + real-order filters + centralized safeguards + bounded output.
3. **Clear external/internal separation:** static public facts and Wossol-derived activity use distinct source/provenance families and explicit non-national-demand language.
4. **Downstream evidence boundary:** Analytics may consume the safe snapshot but must preserve the exact evidence it used; Home only surfaces Analytics-owned recommendation output.

## 12. Merchant Journey / Old Way vs Wossol Way

| Stage | Common risk | Current Wossol behavior |
|---|---|---|
| Seek national context | scattered sources with unclear dates | concise Libya catalog with period and publisher labels |
| Interpret Wossol activity | confuse platform data with national demand | labels as observed eligible Wossol activity and explicitly disclaims whole-market representation |
| Compare categories | infer from sparse or single-tenant sample | suppresses cells below line and tenant-diversity safeguards |
| Use old Orders after category edits | current taxonomy rewrites history | category is resolved at OrderItem creation from assignment history |
| Apply to a merchant decision | treat activity as likely profit | Market Center provides no recommendation/profit claim; requires additional evidence and judgment |

## 13. Hidden / Non-Obvious Advantages

- Suppressed categories are removed before top-12, so sparse high-volume raw categories do not consume a visible slot or leak by rank displacement.
- Category membership is time-specific; later Product reclassification or category retirement does not relabel historical orders.
- Test Orders are excluded while legitimate later cancellations/returns remain: the metric is order-intent activity, not fulfillment success.
- Libya period boundaries are fixed independently of a requesting Workspace's timezone, supporting comparable market-level windows.
- Market Center retains one owner for aggregation and safeguards instead of allowing an Analytics consumer to reimplement privacy rules.

## 14. Data & Intelligence Assets

The static catalog carries source keys, values, periods, source kinds, narratives and interpretation text. The observed-activity query derives period, active Workspace, test/deletion, active item and historical category evidence, then returns category code/label/ordered quantity only. Internally it computes line and independent Workspace/Merchant counts for suppression, but the published contract omits them. This is a small, real descriptive aggregate, not a learning loop: there is no Market Center persistence, feedback, outcome attribution, longitudinal trend, national-market denominator, market sizing by category, or causal evaluation.

## 15. Cross-Section Compound Advantages

- **Products × Orders × Market Center:** immutable category assignments connect catalog identity to observed historical ordered units.
- **Orders × Market Center:** real/test purpose and merchant-deletion evidence shape the cohort; status outcomes do not, consistent with the ordered-demand definition.
- **Market Center × Analytics × Home:** an exact privacy-safe snapshot can support a carefully bounded personalized Analytics opportunity, then reach Home only through the recommendation projection. This does not make Home a Market Center consumer or prove the recommendation is causal/profitable.
- **Finance/Tracking × Market Center:** outcome data is not part of Slice 1; Market Center cannot infer delivery, returns, collection, or margin.

## 16. Competitive Analysis

The competitive master supports a limited conclusion: public direct-competitor material covers ordinary merchant analytics, dashboards, market/category guides or operational reports, but it does not verify an equivalent competitor system for suppressed, historically categorized, cross-merchant order activity. CODZOSS-style category guides and general “market insights” are not proof of such aggregation. The master identifies a possible whitespace around real aggregated market outcomes, but this is a candidate territory, not evidence of Wossol superiority or a verified category-first moat. Any comparison remains bounded by public evidence and should not equate competitor silence with absence.

## 17. Marketing Intelligence

**Asset ID:** MKT-01  
**Capability:** sourced Libya context with clearly separated Wossol-observed activity.  
**Evidence IDs:** EV-MKT-001, EV-MKT-003–008, EV-MKT-011. **Evidence status:** GREEN for source-defined static content and inspected implementation; YELLOW for continuing freshness/live runtime.  
**Angle:** “See Libya market context alongside privacy-protected activity observed across Wossol.”  
**Caveat:** Must retain “observed across Wossol” and must not imply national category demand, growth, popularity, market share, performance, or profit.

## 18. Surprise Findings

The most strategically meaningful property is not the list of category totals but the conservative evidence contract around them: historic assignment semantics, suppressed small cells, and no contributor identities. The product deliberately tolerates unclassified old lines instead of inventing category histories. Conversely, this rigor can make results incomplete and should not be marketed as full-market coverage.

## 19. Potential Category Reframes

Defensible current framing: **a sourced Libya market briefing with privacy-protected Wossol activity signals**. Avoid “Libya market intelligence,” “demand intelligence,” “category trends,” “market winners,” “benchmark,” “market share,” “what sells in Libya,” or “predict what will sell”; these exceed the current observed cohort and evidence.

## 20. Brand Evidence

- **Trust:** source periods and Wossol provenance are separated; hidden cohort counts do not leak.
- **Restraint:** sparse cells disappear and the user receives no fabricated estimate.
- **Accountability:** taxonomy history and fixed cohort semantics make category attribution inspectable.
- **Relevance:** only the active Libya Workspace is allowed to view the Libya-specific content, while Store selection does not distort cross-Workspace scope.

## 21. Weaknesses / Risks / Gaps

1. **Static catalog freshness:** no ingestion, scheduled review, or source change alert exists in the inspected implementation. Catalog version `1.1` says last reviewed 2026-08-25; IMF has subsequently published 2026 Libya context, while several displayed macro facts remain from the 2025 Article IV data vintage. Values are labeled 2025 projections, but the UI could make their age and projection status more salient as time passes.
2. **Source traceability:** UI source details show publisher, title and period but no direct URL; a user cannot follow the source from the interface. The source keys and titles are code-level provenance, not a robust refresh/review workflow.
3. **Interpretation bound:** ordered quantities include legitimate cancellation/return outcomes and are not fulfilled/delivered units or retained revenue. Even adequate cross-tenant safeguards do not make the cohort representative of Libya or category-level profitability.
4. **Coverage bias:** only active Libya Workspaces and historically assigned, lifecycle-valid categories contribute. Pre-assignment/legacy-bootstrap lines remain unclassified. The signal is Wossol's observable platform cohort, not all Libya commerce.
5. **Suppression policy is static:** minimums of 12 lines, 3 Workspaces and 3 Merchants are explicit code safeguards, but no empirical privacy review or re-identification risk assessment was inspected. Do not describe the numbers as statistical confidence or national-market thresholds.
6. **No runtime proof:** source and focused tests do not prove production dataset quality, query performance, endpoint deployment, merchant comprehension, or source-content adoption.
7. **No validation of payment-to-commerce inference:** POS/card/instant-payment value is payment infrastructure context, not e-commerce sales or addressable digital demand.

## 22. Future Strategic Potential

| Category | Assessment |
|---|---|
| Current foundation | sourced external Libya facts plus narrowly defined, privacy-suppressed Wossol ordered-unit activity |
| Inferred extension | trend comparisons, category/outcome signals or decision context only after stable longitudinal cohorts, explicit coverage/denominators and additional delivery/return/cost evidence |
| Strategic relevance | could become an evidence layer connecting observed commerce and merchant decisions; present Slice 1 is descriptive only |
| Brand relevance | a future “market understanding from real operating evidence” territory is a hypothesis, not a current promise |

## 23. Claim Safety

| Claim | Safety | Reason |
|---|---|---|
| View a Libya market briefing with cited publishers and periods | GREEN / source-qualified | static catalog includes publisher/title/period; no runtime freshness guarantee |
| See eligible category ordered units observed across Wossol in last 30 days | GREEN / qualify | source contract and UI show period, observed Wossol activity, and units |
| Identify the most popular or fastest-growing categories in Libya | RED | cohort is not nationally representative and has no growth series/market denominator |
| See what sells or delivers best | RED | counts are ordered units; legitimate cancellations/returns remain and no fulfillment outcome is included |
| Discover profitable categories or market opportunities | RED for Market Center alone | no margin, retained revenue, causality or merchant-specific decision logic in Market Center |
| Privacy-protected aggregate | YELLOW / precise | safeguards and output omission are implemented; do not claim formal anonymization or zero re-identification risk |
| Current/latest Libya data | RED without refresh qualification | static catalog, review date, and old IMF vintage; no live source update |

## 24. Commercial Magnitude

**SUPPORTING / POTENTIAL.** The external briefing offers convenience and context, while Slice 1 is an informative signal for a limited Libya Wossol cohort. The signal's immediate commercial magnitude is bounded: it does not itself change a merchant action or prove lift. Strategic value could rise if it matures into a validated outcome-aware decision layer without diluting the current privacy and provenance discipline.

## 25. Strategic Classification

| Area | Classification |
|---|---|
| Static national statistics/payment context | TABLE STAKES / content utility |
| Bounded category activity dashboard | CATEGORY PARITY / useful descriptive view |
| Historically attributed and privacy-suppressed cross-Workspace output | POTENTIAL DIFFERENTIATOR in implementation depth; commercial distinctiveness unvalidated |
| National demand/market share/profit signals | WHITESPACE / not implemented |
| Source freshness and longitudinal evidence governance | MUST IMPROVE before “current market intelligence” positioning |

## 26. Action Register

| Priority | Action | Rationale |
|---|---|---|
| MUST FIX / GOVERN | Establish a review/update cadence and owner for catalog facts; revisit IMF-vintage presentation and stale data indicators | Static macro content ages even when its original source was correct |
| MUST IMPROVE | Provide direct source links or a traceable source-detail path where source terms permit | Titles alone make independent merchant verification harder |
| MUST MATCH | Keep period, Wossol-observed label, and national-market disclaimer adjacent to signals | Prevent overreading category order volume |
| MUST VERIFY | Observe production authorization, query behavior, suppression and merchant UI in a non-destructive authenticated environment | Static source/tests do not establish deployed behavior |
| MUST BEAT | Only add outcomes/trends after canonical outcome attribution, adequate coverage and privacy review | Protects against presenting order intent as commercial success |
| DO NOT COPY | Do not turn external payment rails or category totals into e-commerce/TAM claims | Payment values and Wossol cohorts are not sales-market denominators |
| POTENTIAL MOAT | Preserve category-history, cohort identity and suppression contract across Analytics consumption | Trustworthy historical comparability may compound if maintained |

## 27. Evidence Register

**EV-MKT-001 — Merchant Market Center page.** **Type:** P1. **Path:** `apps/frontend/src/app/merchant/market-center/page.tsx`. **Observed:** Libya external briefing, separate Wossol Market Signals area, ordered unit labeling/disclaimer, explicit period end, metadata, source details, safe insufficient-data state, loading/error handling. **Confidence:** High for source behavior.

**EV-MKT-002 — Market Center presentation/request/availability.** **Type:** P1 with P2 source. **Paths:** `market-center-presentation.ts`, `market-center-availability.ts`, their `*.spec.ts` files. **Observed:** only active workspaceId is requested; availability is LY-only and Store-independent; dates use backend timezone without displaying technical zone. **Confidence:** High; execution recorded under EV-MKT-010.

**EV-MKT-003 — Authenticated controller and scoped catalog service.** **Type:** P1. **Paths:** `apps/backend/src/modules/market-center/market-center.controller.ts`, `merchant-market-center.service.ts`. **Observed:** authenticated user; active merchant user/account, active workspace link/membership; country-specific static catalog; Libya market period resolved in `Africa/Tripoli`; internal snapshot delegated to Market Center owner. **Confidence:** High for source behavior.

**EV-MKT-004 — Static external catalog and response contract.** **Type:** P1/P3. **Paths:** `merchant-market-center.service.ts`, `market-center.types.ts`, `docs/wossol-system-design/01-system-design/core-systems/LIBYA_MARKET_CENTER_V1.md`, `docs/ui/merchant/MERCHANT_MARKET_CENTER_UI_SPEC.md`. **Observed:** version 1.1, review date 2026-08-25, static Libya values, publisher/title/data period references, no URL/fetch/refresh, and distinct `WOSSOL_OBSERVED_ACTIVITY` snapshot. **Confidence:** High for implementation/contract; source continued freshness is separate.

**EV-MKT-005 — Cross-Workspace aggregate and safe publication.** **Type:** P1. **Path:** `apps/backend/src/modules/market-center/market-intelligence.service.ts`. **Observed:** active Libya Workspace cohort, fixed period, active item, test exclusion, special deleted-before-confirmation exclusion, historical assignment/category interval, aggregate counts, centralized suppression, stable sort/top-12, response omits contributor counts. **Confidence:** High for inspected query/source; runtime database behavior unverified.

**EV-MKT-006 — Product taxonomy history.** **Type:** P1/P4. **Paths:** `apps/backend/prisma/schema.prisma` (`ProductCategory`, `ProductCategoryAssignment`); `docs/wossol-system-design/01-system-design/core-systems/PRODUCT_TAXONOMY_P0_10.md`. **Observed:** assignment stores assigned/superseded time and current state; category has active/retired window; SQL resolves assignment at OrderItem creation rather than current Product category. **Confidence:** High for schema/query contract.

**EV-MKT-007 — Publication suppression policy.** **Type:** P1/P2. **Paths:** `market-intelligence-suppression-policy.ts` and `.spec.ts`. **Observed:** minimum 12 active eligible sold lines, 3 independent Workspaces, and 3 Merchants. Explicitly a publication safeguard, not a statistical confidence/national-market threshold. **Confidence:** High for code; wider privacy assessment not established.

**EV-MKT-008 — Frontend/backend separation.** **Type:** P1/P2. **Paths:** `market-center.types.ts`, `market-center-presentation.ts`, `market-intelligence-presentation.spec.ts`, `page.tsx`. **Observed:** UI receives backend status, labels, totals and period; does not implement thresholds; insufficient state hides numeric values. **Confidence:** High for source; frontend specs were not executable in its package as attempted because `ts-node/register` is absent.

**EV-MKT-009 — Order purpose/deletion evidence.** **Type:** P1. **Paths:** `apps/backend/prisma/schema.prisma` (`Order.isTestRecord` and `OrderStatusHistory`); `apps/backend/src/modules/orders/merchant-order-delete-lifecycle.ts`; query in EV-MKT-005. **Observed:** purpose is persisted; special merchant pre-confirmation delete has exact source marker; ordinary later statuses are not broadly filtered by the market query. Legitimate cancellation/return activity therefore remains consistent with an order-intent metric. **Confidence:** High for inspected source.

**EV-MKT-010 — Focused verification and source state.** **Type:** P2. **Observed:** Market Center backend focused suite: 22 tests passed, 0 failed (controller, suppression, intelligence, merchant catalog/service); backend `typecheck` passed; frontend `typecheck` passed. Attempted frontend focused Node tests did not start because frontend has no `ts-node/register`; no frontend spec pass is claimed. Product working tree remained clean at recorded commit. **Confidence:** High for command output; no full suite or production integration run claimed.

**EV-MKT-011 — Official external fact checks.** **Type:** P5 primary-source spot-check. **Sources:** [World Bank Libya population series](https://databank.worldbank.org/reports.aspx?country=LBY%2CBDI&series=SP.POP.TOTL&source=2) (2025 value 7,458,555), [World Bank urban population series](https://data.worldbank.org/indicator/SP.URB.TOTL.IN.ZS?locations=LY) (2024: 88%), [Central Bank of Libya electronic-payment publication](https://cbl.gov.ly/en/cash-liquidity-electronic-payments-foreign-currency-sales-and-the-development-of-banking-services-highlight-key-areas-discussed-at-the-meeting-between-the-governor-and-deputy-governor-of-the-central-bank-of-libya-and-the-general-managers-of-major-commercial/) (Jan–Jul 2026: LYD 252B LYPay/OnePay, LYD 33B POS, 270,000+ terminals), [IMF 2025 Article IV report](https://www.imf.org/en/publications/cr/issues/2025/06/25/libya-2025-article-iv-consultation-press-release-and-staff-report-568035) ($47.2B nominal GDP, $6.8K GDP per capita and 16.1% real growth for 2025 table vintage), and [IMF Libya country page](https://www.imf.org/en/countries/lby) (2026 consultation concluded; report not published with authorities' consent; current country-page 2026 GDP growth projection is 6.7%). Values checked are supported in those source vintages; change control/freshness not established. **Confidence:** High for observed official source publications.

**EV-MKT-012 — Competitive evidence boundary.** **Type:** competitive master/public-source synthesis, not new competitor test. **Path:** `01-competitive/WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md`, relevant Market Center whitespace and competitor capability entries. **Observed:** common reports/category insights exist; no public evidence in the master establishes an equivalent privacy-thresholded, historical-category cross-merchant order aggregate. **Confidence:** Medium; absence in reviewed public material is not proof of competitor absence.

## 28. Contradictions & Uncertainty

1. **CONTRADICTION-MKT-001 — “Market Center” name versus signal scope:** The screen's national Libya briefing is externally sourced; Wossol category signals represent eligible activity observed in Wossol only. The UI and contract qualify the signal, but the broad product name can invite a national-demand inference. Preserve the adjacent disclaimer in any derivative presentation.
2. **CONTRADICTION-MKT-002 — static data versus freshness language:** Version 1.1 says information is reviewed periodically and shows periods, but implementation has no refresh/update path. The external values checked do resolve to official publications, yet the user-facing “market guide” is not live. IMF's 2025 projection vintage is now old context; the 2026 consultation occurred but the IMF states the report was not published. Treat existing values as dated estimates/projections, not latest macro truth.
3. **CONTRADICTION-MKT-003 — ordered intent versus performance language:** Query counts ordered quantity and deliberately keeps legitimate cancellations/returns. Any description as sold, delivered, successful, popular, or profitable would conflict with the actual metric.
4. Runtime/production access, live output, sample coverage, suppression frequency, and endpoint performance were not verified. Product source and tests support implementation logic only.

## 29. Open Questions

1. Who owns periodic source review, catalog version changes, and stale-data handling; should time-sensitive metrics be removed or visibly age out?
2. Can the source detail UI expose direct official-source links without violating the product's no-invented-URL contract?
3. Should historically unclassified lines remain omitted indefinitely, or is any approved backfill policy possible without rewriting classification evidence?
4. What privacy threat model supports current suppression thresholds and output shape as cohort/category volumes grow?
5. What production coverage and stability evidence is required before showing signals more broadly or using them in downstream recommendations?
6. How should a future Market Center distinguish current ordered activity from delivery, returns, collection and retained margin without making attribution ambiguous for multi-category Orders?

## 30. Methodology Learnings

No methodology change identified. Existing source hierarchy, temporal data lineage, suppression interpretation and explicit scope labels adequately distinguish a descriptive platform cohort from market-wide intelligence. This audit reinforces that official-source validity and source recency are separate checks.

## 31. Retroactive Review Impact

No methodology change and no retroactive queue entry. The audit adds cross-domain evidence for future Advertising and Sourcing/Network reviews, while Analytics / Decision Center remains governed by its own 2026-09-25 review record and open issues.

## 32. Canonical Section Takeaway

Market Center currently combines a static, source-attributed Libya briefing with a narrow, privacy-suppressed view of canonical-category order activity observed across eligible Wossol Libya Workspaces. Its historical attribution and suppression are materially careful; its scope is not national demand, and its units are not fulfilled sales or profit. Static source freshness and direct source traceability are the key content-governance gaps. The defensible value is qualified market context and descriptive platform activity—not “market intelligence” in the predictive or representative sense.
