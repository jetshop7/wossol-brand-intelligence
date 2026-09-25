# Analytics / Decision Center Review — 2026-09-25

## Review metadata
- Section: Analytics / Decision Center
- Reviewed intelligence commit: `3c5e60dfac1bf4fade89cd716852d4d50514acd1`
- Product evidence final cross-check: `8600a4cbd1a894579a057b3476db35465289c670`
- Reviewer: ChatGPT — Strategic Reviewer / Quality Gate
- Decision: **ACCEPT WITH OPEN PRODUCT ISSUES**
- Full re-audit required: No

## What passes

The audit correctly classifies the current product as a scoped synthesis and bounded guidance layer rather than an autonomous decision-and-learning system. Current recommendations are deterministic, evidence-backed review prompts; persistence, feedback events and provenance do not by themselves establish outcome evaluation, causal learning or automatic optimization.

Targeted Product verification confirms that the active Analytics module registers `MerchantAnalyticsService`. Its main Merchant cohort query does not exclude `isTestRecord`, so the audit correctly records Test Order eligibility as a real current-behavior/product-contract issue rather than assuming the owner-domain Test Order boundary propagates automatically.

The audit also correctly identifies the Product/Variant filtered financial-scope problem. Product/Variant filters narrow selected Order items and therefore selected-item COGS evidence, while top-level realized revenue can still consume the full qualifying Order collection. For mixed-item Orders, top-level filtered Revenue/Gross Profit therefore must not be interpreted as selected Product/Variant unit economics until Product authority defines the intended semantics.

The Decision Center's health and recommendation rules are deterministic threshold logic with explicit minimum denominators and evidence-status gating. That is useful guidance, but the thresholds are not demonstrated as learned, predictive, causally validated or production-optimized. The audit preserves this distinction.

The Finance boundary is appropriately retained: Finance collection facts can anchor realized-revenue calculations in repository logic, but the accepted Finance review did not establish externally reconciled cash truth or complete accounting. Analytics therefore cannot upgrade those inputs into audited profit.

The audit appropriately distinguishes the separate Admin Platform Analytics reporting surface from Merchant Analytics and avoids treating internal reporting as merchant-visible intelligence.

Verification discipline passes: 99 focused Analytics tests and both typechecks are recorded as observed local verification. The broader run's three Target B rehearsal failures are not hidden, but the audit does not claim a complete full-suite result because the final summary was unavailable.

## Open product issues retained

1. **Test Order eligibility:** active Merchant Analytics includes matching Test Orders because the main cohort predicate lacks an explicit test-record exclusion. Product authority must decide intended semantics across operational metrics, profitability, customer impact and recommendation inputs.
2. **Product/Variant filtered financial scope:** whole-Order collection revenue can be combined with selected-item COGS. Product authority must choose selected-item allocation, clearly labelled Order-level totals, or an unavailable/fail-closed state for mixed-item rollups.
3. **Historical financial semantics:** current/corrected cost-layer values can affect historical cohort interpretation; immutable period-cost semantics should be clarified where required.
4. **Historical customer-quality semantics:** current linked customer reputation/block state can be applied to historical cohort Orders; point-in-time versus current-state interpretation must remain explicit.
5. **Recommendation outcome loop:** durable recommendations and feedback exist, but no automatic outcome evaluator/learning loop is established.
6. **Production evidence quality:** deployed migrations, representative cohort quality, advertising compatibility, source completeness and real merchant usefulness remain unverified.
7. **Threshold validation:** deterministic health/recommendation thresholds are implemented but not proven as predictive or outcome-optimizing thresholds.

These issues materially qualify Analytics claims but do not invalidate the intelligence audit.

## Claim / strategic safety

Safe present territory is scoped operational reporting, evidence-aware profitability calculation, explicit missingness, selected advertising evidence, deterministic comparisons, bounded recommendations and durable recommendation provenance/feedback.

Do not claim accurate real profit, complete ad-to-profit attribution, predictive intelligence, AI learning, causal diagnosis, autonomous optimization, proven merchant outcome improvement, national demand intelligence, or a defensible data moat from the current evidence.

## Methodology impact

No methodology change is required.

## Retroactive impact

No prior accepted section requires re-audit. Home recommendation surfaces remain bounded distribution of Analytics guidance, not proof that recommendation inputs are commercially complete or that recommendations learn from outcomes. Product/Test Order and Finance boundaries should remain visible in later synthesis.

## Acceptance

**Analytics / Decision Center passes the Director Quality Gate and is accepted with the open Product issues above.**

No Codex correction or re-audit is required.

The next section in the canonical sequence is **Market Center**.
