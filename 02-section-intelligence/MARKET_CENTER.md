# Market Center — Section Intelligence

## 1. Audit Metadata

- **Audit date:** 2026-09-27 (V1.2 incremental migration).
- **Methodology:** `00-methodology/MASTER_INSTRUCTIONS.md` v1.2; `00-methodology/CODEX_OPERATING_PROTOCOL.md` v1.1.
- **Competitive reference:** `01-competitive/WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md` v1.0 (2026-09-09), used as the existing stable baseline; no new competitor research in this migration.
- **Intelligence source:** `jetshop7/wossol-brand-intelligence`, branch `main`, synchronized/fast-forwarded to HEAD `6190fb10d523acdabc1fb13414667e6a23853809`, clean before this audit.
- **Product source:** `jetshop7/wossol-platform`, `dev/wossol-integration`, HEAD/local `origin/dev/wossol-integration` `46716c433de40fbdbeb023d297d167c49909b380`, working tree clean; no Product changes made. GitHub could not be reached for a fresh Product remote-tip check, so tracking-ref freshness is not independently verified.
- **Migration baseline:** prior Market Center review at Product `8600a4cbd1a894579a057b3476db35465289c670`, accepted by `04-review-history/MARKET_CENTER_REVIEW_2026-09-26.md` with open Product issues. Market Center implementation itself has no diff since that snapshot; this pass checks its current Analytics consumer and newly relevant checkout-origin evidence.
- **Evidence basis:** P1 executable source/schema, P2 focused Market Center tests and typechecks with execution status recorded, P3 Merchant Market Center UI specification, P4 system-design/product-execution notes where qualified, and official external source verification for material catalog facts. Static source inspection does not establish deployed behavior, production data volume, runtime latency, merchant adoption, or commercial outcome.

## 2. Audit Coverage Map

| Surface | Coverage | Evidence |
|---|---|---|
| Merchant Market Center UI | route, availability, API request, presentation, loading/error and signals | EV-MKT-001–002, EV-MKT-014 |
| API access and external catalog | authenticated request, active Merchant/Workspace scope, Libya-only catalog, period and response contract | EV-MKT-003–004, EV-MKT-014 |
| Cross-Workspace aggregation | cohort filters, category history, suppression, ordering, origin semantics and safe snapshot | EV-MKT-005–008, EV-MKT-013–014 |
| Taxonomy, Orders and lifecycle evidence | category assignment/schema history, Order test/deletion, checkout-origin and active-line policy | EV-MKT-006, EV-MKT-009, EV-MKT-013 |
| Analytics downstream consumer | H-C04 uses bounded Market Center snapshot plus a separately scoped merchant cohort, materializes exact evidence | EV-MKT-013 |
| Tests and source state | focused tests/typechecks and Product delta verification | EV-MKT-010, EV-MKT-014 |
| External facts and competition | catalog facts checked against official primary sources; available competitor evidence from competitive master | EV-MKT-011–012 |

Not inspected as live fact: production database/cohort counts, production endpoint behavior, merchant usage, current CBL/IMF source changes beyond checked official publications, or competitor private capabilities.

## 3. Executive Section Truth

Market Center is currently two carefully separated things: a static, externally sourced Libya market briefing, and a read-only Wossol-observed activity signal that reports suppressed eligible canonical OrderItem quantities by historical product category. The second is genuinely a cross-Workspace aggregate, but not a survey or estimate of Libya-wide demand. V1.2 connected-domain review adds an unresolved order-origin caveat: this Market Center query does not filter incomplete-checkout-origin Orders, which may be created before explicit `Order Now`; Analytics' local H-C04 cohort does exclude them. Therefore the network signal and merchant-side evidence used by H-C04 do not currently have identical checkout-origin eligibility. Neither layer establishes merchant-specific category opportunity, delivered sales, retained revenue, margin, customer preference, market share, or prediction.

The strongest product quality remains the narrow publication boundary: authorized active Libya Workspace access; a market-owned Libya period; historical category attribution; exclusion of test and specially deleted Orders; centralized diversity/volume suppression before top-12 selection; and a response that omits cohort counts, identities, and raw evidence. This is a publication safeguard, not a formal anonymization guarantee. Other material limitations are static external-catalog freshness/traceability and an unresolved checkout-origin cohort boundary: the market signal accepts all otherwise-eligible Orders irrespective of immutable checkout origin, while H-C04 local evidence applies a narrower completed-origin population.

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
5. Read-only aggregation selects active lines on non-test Orders created in the period, across active Libya Workspaces, excluding the immutable merchant-delete-before-confirmation marker. It does not filter `checkoutCaptureOrigin`, so eligible incomplete-checkout-origin Orders are included if they otherwise match.
6. Each line joins the Product category assignment valid at item creation and the category lifecycle active then; unassigned historical lines remain unclassified.
7. Category totals are discarded unless line and independent-tenant safeguards pass; the surviving cells are sorted by ordered quantity descending then code ascending and capped at 12.
8. UI presents Wossol-observed activity separately from the external briefing, with backend-provided period end and a safe insufficient-evidence state. It does not calculate suppression or generate a Market Center recommendation.

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

For a Libya merchant, the briefing consolidates a small set of macro/payment facts and publisher/date references in one Workspace-gated view, reducing some initial source-search and source/date reconstruction. The internal signal provides a narrow, suppression-qualified account of category quantities present in eligible Wossol Orders; an individual Merchant could not recreate the cross-Workspace count from their own account. This may prompt further investigation, but it does not remove market-fit judgment or research, and does not tell the Merchant that a category is growing nationally, profitable, deliverable, underserved, or suitable for their Store. External source detail remains non-clickable, the static catalog needs governance/refresh outside the product, and the signal's incomplete-checkout origin mix is not labelled or excluded.

## 11. Feature Clusters

1. **Country-gated market briefing:** Workspace authorization + Libya catalog + explicit time periods and provenance.
2. **Privacy-safe observed activity:** market-clock cohort + historical taxonomy + non-test/deletion filters + centralized safeguards + bounded output. Checkout-origin is not part of the current exclusion contract.
3. **Clear external/internal separation:** static public facts and Wossol-derived activity use distinct source/provenance families and explicit non-national-demand language.
4. **Downstream evidence boundary:** Analytics may consume the safe snapshot but must preserve the exact evidence it used; Home only surfaces Analytics-owned recommendation output.

## 12. Merchant Journey / Old Way vs Wossol Way

| Stage | Common risk | Current Wossol behavior |
|---|---|---|
| Seek national context | scattered sources with unclear dates | concise Libya catalog with period and publisher labels |
| Interpret Wossol activity | confuse platform data with national demand or assume every canonical Order is explicit checkout intent | labels as observed eligible Wossol activity and disclaims whole-market representation; checkout-origin is not surfaced or filtered |
| Compare categories | infer from sparse or single-tenant sample | suppresses cells below line and tenant-diversity safeguards |
| Use old Orders after category edits | current taxonomy rewrites history | category is resolved at OrderItem creation from assignment history |
| Apply to a merchant decision | treat activity as likely profit | Market Center provides no recommendation/profit claim; requires additional evidence and judgment |

## 13. Hidden / Non-Obvious Advantages

- Suppressed categories are removed before top-12, so sparse high-volume raw categories do not consume a visible slot or leak by rank displacement.
- Category membership is time-specific; later Product reclassification or category retirement does not relabel historical orders.
- Test Orders and special pre-confirmation merchant deletions are excluded while legitimate later cancellations/returns remain. However, `INCOMPLETE_CHECKOUT` origin is not excluded, so “order-intent activity” requires qualification until Product defines whether timeout-finalized Orders belong in this cohort.
- Libya period boundaries are fixed independently of a requesting Workspace's timezone, supporting comparable market-level windows.
- Market Center retains one owner for aggregation and safeguards instead of allowing an Analytics consumer to reimplement privacy rules.

## 14. Data & Intelligence Assets

The static catalog carries source keys, values, periods, source kinds, narratives and interpretation text. The observed-activity query derives period, active Libya Workspace, test/deletion, active item and historical category evidence, then returns category code/label/ordered quantity only. It does not filter or expose checkout-origin composition. Internally it computes line and independent Workspace/Merchant counts for suppression, but the published contract omits them. This is a cross-Workspace descriptive platform aggregate, not a representative national-market intelligence set or learning loop: no Market Center persistence, feedback, outcome attribution, longitudinal trend, national-market denominator, market sizing by category, or causal evaluation was found.

## 15. Cross-Section Compound Advantages

- **Products × Orders × Market Center:** immutable category assignments connect catalog identity to observed historical ordered units.
- **Orders × Market Center:** non-test purpose and merchant-deletion evidence shape the cohort; ordinary status outcomes do not. Checkout-origin currently does not shape it, although incomplete-checkout timeout Orders may precede explicit `Order Now`; this weakens an unqualified “shopper ordered demand” interpretation.
- **Market Center × Analytics × Home:** an exact privacy-safe snapshot can support a bounded Analytics H-C04 suggestion, then reach Home only through the Analytics recommendation projection. H-C04 filters its merchant-local cohort to non-test, non-incomplete-origin Orders and requires merchant/category sample and lifecycle-quality thresholds; the published market ranking may still include incomplete-origin quantities. Analytics preserves the exact snapshot in materialized evidence, but this population difference can affect candidate ordering. This does not make Home a Market Center consumer or prove the recommendation is causal/profitable.
- **Finance/Tracking × Market Center:** outcome data is not part of Slice 1; Market Center cannot infer delivery, returns, collection, or margin.

## 16. Competitive Analysis

The competitive master supports only a bounded baseline: public direct-competitor material covers ordinary analytics, dashboards, market/category guides, operational reports, and COD Mastery's claimed/marketed access to seller benchmarks. It does not establish that those products use the same suppressed, historically categorized, cross-Workspace canonical OrderItem aggregate, but neither does the material establish Wossol superiority. CODZOSS-style category guides and general “market insights” are not proof of equivalent aggregation. A privacy-bounded platform-activity signal remains a possible implementation distinction, not a proven category differentiator or moat. No competitor capability was freshly tested in this migration; competitor silence is not absence.

## 17. Marketing Intelligence

**Asset ID:** MKT-01  
**Capability:** sourced Libya context with clearly separated Wossol-observed activity.  
**Evidence IDs:** EV-MKT-001, EV-MKT-003–008, EV-MKT-011. **Evidence status:** GREEN for source-defined static content and inspected implementation; YELLOW for continuing freshness/live runtime.  
**Angle:** “See sourced Libya context alongside qualified category activity observed across Wossol.”
**Caveat:** Must retain “observed across Wossol”; explain that output is eligible canonical OrderItem quantity, not whole-market demand, sales, growth, popularity, market share, performance or profit. Until the incomplete-checkout policy is resolved, do not imply every counted Order followed explicit shopper `Order Now`. “Privacy-protected” means safeguards/output minimization only, not formal anonymization.

## 18. Surprise Findings

The most strategically meaningful property is not the list of category totals but the conservative publication contract around them: historic assignment semantics, suppressed small cells, and no contributor identities. The product deliberately tolerates unclassified old lines instead of inventing category histories. V1.2 also surfaces an unexpected boundary: the raw cross-Workspace signal's Order-origin rule is broader than Analytics' merchant-local H-C04 population. This is a data-definition inconsistency to reconcile, not a competitive advantage. The suppression and historic taxonomy rigor can make results incomplete and should not be marketed as full-market coverage.

## 19. Potential Category Reframes

Defensible current framing: **a sourced Libya market briefing alongside suppression-qualified category quantities observed in eligible Wossol Orders**. Avoid “Libya market intelligence,” “demand intelligence,” “category trends,” “market winners,” “benchmark,” “market share,” “what sells in Libya,” “spot the opportunity” as a product-result promise, or “predict what will sell”; these exceed the current observed cohort and evidence. The actual UI subtitle's opportunity language is broader than the current Market Center capability and should be read as aspiration, not a delivered recommendation.

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
8. **Checkout-origin population gap:** Market Center includes eligible `INCOMPLETE_CHECKOUT`-origin OrderItems, while Analytics H-C04 excludes them from merchant-local outcomes used to qualify the same top-ranked category candidates. No representative origin mix or test demonstrates intended alignment. This can affect rank interpretation and the downstream suggestion; it is a Product/data-contract question, not proof the Market Center number is numerically incorrect.
9. **Opportunity-copy mismatch:** the UI says “Spot the opportunity. Make better business decisions,” but Market Center supplies no merchant-specific recommendation, explanation or action path. H-C04 is an optional Analytics-owned deterministic suggestion and inherits source-population limitations.

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
| See eligible canonical OrderItem quantities observed across Wossol in last 30 days | YELLOW / qualify | code and UI show period and Wossol provenance; incomplete-checkout-origin Orders are included, and the signal is not whole-market demand |
| Identify the most popular or fastest-growing categories in Libya | RED | cohort is not nationally representative and has no growth series/market denominator |
| See what sells or delivers best | RED | counts are ordered units; legitimate cancellations/returns remain and no fulfillment outcome is included |
| Discover profitable categories or market opportunities | RED for Market Center alone | no margin, retained revenue, causality or merchant-specific decision logic in Market Center |
| Privacy-safeguarded aggregate | YELLOW / precise | publication thresholds and output minimization are implemented; no formal anonymization or zero re-identification-risk assessment |
| Current/latest Libya data | RED without refresh qualification | static catalog, review date, and old IMF vintage; no live source update |

## 24. Commercial Magnitude

**SUPPORTING / POTENTIAL.** The external briefing offers convenience and context, while Slice 1 is an informative signal for a limited Libya Wossol cohort. The signal's immediate commercial magnitude is bounded: it does not itself change a merchant action or prove lift. Strategic value could rise if it matures into a validated outcome-aware decision layer without diluting the current privacy and provenance discipline.

## 25. Strategic Classification

| Area | Classification |
|---|---|
| Static national statistics/payment context | TABLE STAKES / content utility |
| Bounded category activity dashboard | PARITY / useful descriptive view |
| Historically attributed and privacy-suppressed cross-Workspace output | POTENTIAL DIFFERENTIATOR in implementation depth; commercial distinctiveness unvalidated |
| National demand/market share/profit signals | WHITESPACE / not implemented |
| Source freshness and longitudinal evidence governance | MUST IMPROVE before “current market intelligence” positioning |

## 26. Action Register

| Priority | Action | Rationale |
|---|---|---|
| MUST FIX / GOVERN | Establish a review/update cadence and owner for catalog facts; revisit IMF-vintage presentation and stale data indicators | Static macro content ages even when its original source was correct |
| MUST IMPROVE | Provide direct source links or a traceable source-detail path where source terms permit | Titles alone make independent merchant verification harder |
| MUST MATCH | Keep period, Wossol-observed label, and national-market disclaimer adjacent to signals | Prevent overreading category order volume |
| MUST MATCH | Resolve whether `INCOMPLETE_CHECKOUT`-origin Orders belong in Market Center's ordered-activity cohort; align P1 query, P3 Slice 1 contract, UI wording and Analytics H-C04 population semantics | A canonical recovered Order may precede explicit shopper `Order Now`; market rank currently uses this broader population than H-C04 merchant evidence |
| MUST IMPROVE | Qualify or revise the “Spot the opportunity” subtitle unless a current Market Center recommendation/decision workflow is actually established | Current section is descriptive; no Market Center-owned action guidance exists |
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

**EV-MKT-010 — Prior focused verification and source state (historical).** **Type:** P2. **Product snapshot:** `8600a4c…`. **Observed:** prior audit reported Market Center backend focused suite 22/22 and both typechecks passing; frontend spec attempt did not start because frontend lacked `ts-node/register`. This is historical verification, not the current-source run. No production integration was claimed.

**EV-MKT-011 — Official external fact checks.** **Type:** P5 primary-source spot-check. **Sources:** [World Bank Libya population series](https://databank.worldbank.org/reports.aspx?country=LBY%2CBDI&series=SP.POP.TOTL&source=2) (2025 value 7,458,555), [World Bank urban population series](https://data.worldbank.org/indicator/SP.URB.TOTL.IN.ZS?locations=LY) (2024: 88%), [Central Bank of Libya electronic-payment publication](https://cbl.gov.ly/en/cash-liquidity-electronic-payments-foreign-currency-sales-and-the-development-of-banking-services-highlight-key-areas-discussed-at-the-meeting-between-the-governor-and-deputy-governor-of-the-central-bank-of-libya-and-the-general-managers-of-major-commercial/) (Jan–Jul 2026: LYD 252B LYPay/OnePay, LYD 33B POS, 270,000+ terminals), [IMF 2025 Article IV report](https://www.imf.org/en/publications/cr/issues/2025/06/25/libya-2025-article-iv-consultation-press-release-and-staff-report-568035) ($47.2B nominal GDP, $6.8K GDP per capita and 16.1% real growth for 2025 table vintage), and [IMF Libya country page](https://www.imf.org/en/countries/lby) (2026 consultation concluded; report not published with authorities' consent; current country-page 2026 GDP growth projection is 6.7%). Values checked are supported in those source vintages; change control/freshness not established. **Confidence:** High for observed official source publications.

**EV-MKT-012 — Competitive evidence boundary.** **Type:** competitive master/public-source synthesis, not new competitor test. **Path:** `01-competitive/WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md`, relevant Market Center whitespace and competitor capability entries. **Observed:** common reports/category insights exist; no public evidence in the master establishes an equivalent privacy-thresholded, historical-category cross-merchant order aggregate. **Confidence:** Medium; absence in reviewed public material is not proof of competitor absence.

## 28. Contradictions & Uncertainty

1. **CONTRADICTION-MKT-001 — “Market Center” name versus signal scope:** The screen's national Libya briefing is externally sourced; Wossol category signals represent eligible activity observed in Wossol only. The UI and contract qualify the signal, but the broad product name can invite a national-demand inference. Preserve the adjacent disclaimer in any derivative presentation.
2. **CONTRADICTION-MKT-002 — static data versus freshness language:** Version 1.1 says information is reviewed periodically and shows periods, but implementation has no refresh/update path. The external values checked do resolve to official publications, yet the user-facing “market guide” is not live. IMF's 2025 projection vintage is now old context; the 2026 consultation occurred but the IMF states the report was not published. Treat existing values as dated estimates/projections, not latest macro truth.
3. **CONTRADICTION-MKT-003 — ordered activity versus performance language:** Query counts ordered quantity and deliberately keeps legitimate cancellations/returns. Any description as sold, delivered, successful, popular, or profitable would conflict with the actual metric.
4. **CONTRADICTION-MKT-004 — incomplete-checkout origin semantics:** Market Center counts canonical OrderItems without filtering `checkoutCaptureOrigin`. Current Shopify COD timeout recovery may create an `INCOMPLETE_CHECKOUT`-origin Order before explicit `Order Now`. In contrast, Analytics standard performance and its H-C04 merchant-local qualification exclude this origin while Analytics economic calculation can include eligible canonical Orders. Therefore the published category rank and the local outcome evidence that can consume it have different intent/cohort boundaries.
   - **Evidence strength:** P1 Market Center SQL/schema and Analytics query; current P1 Shopify finalization path; accepted 2026-09-27 Orders/Analytics V1.2 review records.
   - **Working conclusion:** Keep the Market Center count described as eligible canonical OrderItem activity observed in Wossol, not unqualified shopper demand. Product must decide whether incomplete-origin Orders should remain included, be excluded, or be separately represented before strengthening order-intent claims.
   - **Remaining uncertainty:** The policy intent for `INCOMPLETE_CHECKOUT` and downstream Market Center treatment is not defined; no production mixture or merchant comprehension evidence is available.
   - **Required verification:** Align Product/UI/data contract and tests for Market Center and H-C04 cohort boundaries, then inspect deployed data/migration and source-origin mix.
5. Runtime/production access, live output, sample coverage, suppression frequency, and endpoint performance were not verified. Product source and tests support implementation logic only.

## 29. Open Questions

1. Who owns periodic source review, catalog version changes, and stale-data handling; should time-sensitive metrics be removed or visibly age out?
2. Can the source detail UI expose direct official-source links without violating the product's no-invented-URL contract?
3. Should historically unclassified lines remain omitted indefinitely, or is any approved backfill policy possible without rewriting classification evidence?
4. What privacy threat model supports current suppression thresholds and output shape as cohort/category volumes grow?
5. What production coverage and stability evidence is required before showing signals more broadly or using them in downstream recommendations?
6. How should a future Market Center distinguish current ordered activity from delivery, returns, collection and retained margin without making attribution ambiguous for multi-category Orders?
7. Should `INCOMPLETE_CHECKOUT` timeout-origin Orders count in the cross-Workspace Market Center signal? If yes, should the signal disclose origin composition or should Analytics H-C04 use a matched origin population?
8. Who owns the “Spot the opportunity” UI framing given that Market Center itself supplies no recommendation or merchant-specific action?

## 30. Methodology Learnings

No methodology change identified. Existing source hierarchy, temporal data lineage, suppression interpretation and explicit scope labels adequately distinguish a descriptive platform cohort from market-wide intelligence. This audit reinforces that official-source validity and source recency are separate checks.

## 31. Retroactive Review Impact

No methodology change and no new retroactive queue entry. RR-V12-004 is updated by this migration; Director V1.2 Quality Gate is pending. The incomplete-checkout cohort boundary is now documented in the current Analytics V1.2 migration and Orders V1.2 review, and should remain visible to any downstream advertising or synthesis claim that uses market activity. This finding does not by itself require changing an earlier section beyond already-recorded open policy issues.

## 32. Canonical Section Takeaway

Market Center currently combines a static, source-attributed Libya briefing with a narrow, publication-safeguarded view of historical-category OrderItem quantities observed across eligible Wossol Libya Workspaces. Its historical attribution and suppression are materially careful; its scope is not national demand, and its quantities are not fulfilled sales or profit. V1.2 adds a material unresolved origin caveat: `INCOMPLETE_CHECKOUT` Orders may enter the Market Center signal even though the Analytics H-C04 merchant-local outcome cohort excludes them. Until Product aligns these semantics, describe the aggregate as Wossol-observed canonical Order activity, not unqualified shopper demand. Static source freshness, direct source traceability, and the “Spot the opportunity” aspiration without Market Center-owned guidance remain content/claim gaps. The defensible value is qualified market context and descriptive platform activity—not predictive or representative market intelligence.

## 33. V1.2 Incremental Migration / Delta Review

**Prior accepted product truth retained:** Market Center is a hardcoded Libya external-data briefing plus one read-only, cross-Workspace aggregate of eligible active OrderItem quantities, attributed to Product categories historically valid at line creation and published only after volume/diversity suppression. It is not national demand, sales success, profit, a general benchmark or a recommendation engine. The 2026-09-26 Director review accepted that truth with the existing static-source, traceability, representativeness, privacy-governance and runtime issues open; no issue is considered fixed by this migration.

**Product truth delta:** Market Center-owned UI/controller/service/query/type/suppression files have no Product diff between the accepted snapshot (`8600a4c…`) and current local Product HEAD (`46716c4…`). The connected Analytics consumer has materially advanced: H-C04 calls only the published Market Center snapshot, selects candidates from its top-five published categories, then tests a separate merchant-local cohort. That local query excludes Test Orders, merchant-delete-before-confirmation and `INCOMPLETE_CHECKOUT`; it requires at least five unique eligible merchant Orders and a final cohort plus minimum outcome denominators/rates. The exact Market Center snapshot and selected Merchant evidence are copied into the immutable Analytics recommendation materialization. Thus Market Center continues to own the cross-Workspace aggregation, while the downstream recommendation belongs to Analytics.

**V1.2 second-pass value synthesis:**

| Capability / cluster | Merchant job removed or reduced | Tool / process / friction | Context, truth and decision value | Proof / limit |
|---|---|---|---|---|
| Static Libya briefing | Reduces some initial searching/collecting of selected population, payment and macro facts | Curates a small bundle of World Bank, IMF and CBL values in one authorized Workspace page; no replacement for market research or direct source verification | Publisher/title/period metadata preserves which source family supports each fact; information is descriptive context only | Prior source spot-check exists; no current source-refresh workflow or clickable URL; no sales/TAM inference |
| Wossol observed-category snapshot | Merchant cannot assemble a cross-Workspace view from their own records alone; Market Center provides a narrowly suppressed shared-platform observation | Aggregates in one backend query; avoids exposed source-row switching, but does not replace external market tools or tell a merchant what to sell | Market timezone + historical category assignment + active-line rules + test/deletion filter + diverse-tenant safeguards preserve a qualified platform signal | Current signal is ordered quantity, not delivered outcome or nationally representative demand; incomplete-origin policy is unresolved; safeguards are not formal anonymity |
| Market Center → Analytics H-C04 | Reduces some manual comparison of published categories with the Merchant's own qualifying operational outcomes | Market Center offers one safe snapshot; Analytics performs the joining, eligibility checks, ranking/recommendation and evidence persistence | Exact snapshot is preserved in recommendation materialization, but H-C04 local cohort excludes incomplete-origin Orders while the cross-Workspace signal does not; candidate rank may reflect a broader origin mix | Current deterministic review suggestion, not a Market Center recommendation, prediction or proven business outcome; candidate thresholds are unvalidated |

**Connected value chain:** official-source facts → static Libya catalog; canonical OrderItem + historical ProductCategoryAssignment + Order purpose/status → period-scoped cross-Workspace category aggregation → suppression and bounded publication → optional exact snapshot consumption by Analytics → merchant-local evidence and deterministic bounded H-C04 review suggestion → human action in another workflow. Current progress reaches **DATA CAPTURED → CONNECTED → CALCULATED / SUPPRESSED → DESCRIPTIVELY PRESENTED** in Market Center. Interpretation is limited; recommendation/action/outcome/learning are not owned here. Analytics advances one optional path to deterministic Decision Support, not closed-loop Learning Intelligence.

**Intelligence layers:** the external catalog is sourced macro/market context; the Wossol signal is an internal platform-observed cross-Workspace fact, not a national-market measure. A Merchant-specific H-C04 recommendation is created only in Analytics after applying local evidence. No Market Center layer explains why a national market is attractive, forecasts a category, scores merchant fit, guides an action, observes outcome or learns.

**Merchant agency, trust and accumulation:** the view is read-only and permission/context scoped. No Merchant can alter the sample, category history, Store scope or thresholds. Suppression, omitted identities and temporal category attribution are meaningful integrity/privacy design evidence; they do not prove formal de-identification, representative coverage or accuracy in production. More Wossol participation may make the signal available for more categories, but higher quantity alone does not establish better insight; any compounding advantage depends on origin/outcome quality, stable taxonomies, adequate coverage and a privacy threat model.

**Competitive and commercial reading:** basic market guides, dashboards and category advice are already present in the dated competitor baseline; COD Mastery has benchmark claims, so do not position “shared data” alone as unique. The implementation's historical category attribution and privacy-safeguarded cross-Workspace output may be a **potential implementation differentiator**, not demonstrated superiority or a moat. The most defensible current merchant value is convenient sourced context plus a qualified Wossol platform signal. It does not remove market selection judgment or prove time savings, revenue, profitability or lift.

**Marketing and demonstration boundary:** demonstrate the external/observed sections separately; keep “observed across Wossol,” ordered units, period and non-national-market disclaimer adjacent. “Understand the market. Spot the opportunity. Make better business decisions.” is current P1 UI copy, but Market Center itself supplies no recommendation. Treat “spot the opportunity” as aspiration or qualify it; do not present it as a current outcome promise. Do not use data graphics to imply national category popularity or performance.

**Current vs future:** current = versioned static source catalog, Libya-only access, safe category aggregate, and optional exact-snapshot Analytics consumption. Future/inferred = managed source refresh/links, clearly defined checkout-origin cohort, reviewed privacy threat model, longitudinal outcomes, representative coverage, market/context explanation and tested decision recommendations. These future capabilities are not current product claims.

## 34. V1.2 Evidence Additions

**EV-MKT-013 — Market Center checkout-origin and Analytics H-C04 population mismatch.** **Type:** P1 with P2/P3 supporting review/contract evidence. **Repository/branch/commit:** Product `jetshop7/wossol-platform`, `dev/wossol-integration`, `46716c433de40fbdbeb023d297d167c49909b380`. **Paths/symbols:** `apps/backend/src/modules/market-center/market-intelligence.service.ts` (`categorySignals` SQL); `apps/backend/prisma/schema.prisma` (`OrderCheckoutCaptureOrigin`, `Order.checkoutCaptureOrigin`); `apps/backend/src/modules/shopify/shopify-cod.service.ts` (`processDueCheckoutSessions`, `finalizeCheckoutSession`); `apps/backend/src/modules/analytics/merchant-analytics.service.ts` (`personalizedMarketOpportunity`). **Supporting review:** `04-review-history/ORDERS_V1_2_MIGRATION_REVIEW_2026-09-27.md`, `04-review-history/ANALYTICS_DECISION_CENTER_V1_2_MIGRATION_REVIEW_2026-09-27.md`. **Observed:** Market Center SQL filters time, active Libya Workspace, `is_test_record=false`, active OrderItem and the special pre-confirmation merchant-deletion marker; it has no checkout-origin predicate and groups/sums every qualifying item. Shopify's incomplete timeout path can finalize a `COLLECTING`/`orderReady` session as an Order with `INCOMPLETE_CHECKOUT` provenance without `Order Now`. Analytics H-C04 consumes the Market Center top-five snapshot but its local Order query excludes both test and incomplete-origin records, then applies its own sample/finality/outcome thresholds. The recommendation evidence records the Market snapshot and Merchant evidence. **Conclusion:** different origin cohorts feed the cross-Workspace category rank and its optional Merchant-local qualification; the origin mixture may affect candidate position. **Confidence:** High for source behavior; production frequency, intended Market Center policy and material ranking effect are unknown. Do not infer that every incomplete-origin Order lacks all shopper intent; the stronger safe statement is that source does not establish explicit `Order Now` intent.

**EV-MKT-014 — Current Product-state and Market Center verification.** **Type:** P1/P2. **Product:** local branch `dev/wossol-integration`, HEAD and local tracking ref `46716c433de40fbdbeb023d297d167c49909b380`, clean and unmodified; GitHub could not be reached for a remote-tip check. `git diff 8600a4c…..46716c4…` has no Market Center-specific code/UI changes; relevant current delta is in Analytics consumer. Focused Market Center backend specs: **22 passed, 0 failed**; backend and frontend typechecks passed. Frontend focused specs were not run because the frontend package has no configured test script/`ts-node/register` harness. No production DB, deployment, endpoint, source refresh or merchant session was verified. **Confidence:** High for local source and command results, not remote freshness/runtime.
