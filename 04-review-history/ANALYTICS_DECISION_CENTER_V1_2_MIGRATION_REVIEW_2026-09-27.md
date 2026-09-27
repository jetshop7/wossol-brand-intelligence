# Analytics / Decision Center V1.2 Migration Review — 2026-09-27

## Review metadata
- Section: Analytics / Decision Center
- Reviewed intelligence commit: `804da6ed418cdefc73db1f395fd1e6272c936c51`
- Product evidence commit: `46716c433de40fbdbeb023d297d167c49909b380`
- Prior authoritative review: `04-review-history/ANALYTICS_DECISION_CENTER_REVIEW_2026-09-25.md`
- Methodology: V1.2
- Reviewer: ChatGPT — Strategic Reviewer / Quality Gate
- Decision: **ACCEPT WITH OPEN PRODUCT ISSUES**
- Full re-audit required: No
- Retroactive queue: RR-V12-003 completes V1.2 Quality Gate

## Prior accepted truth preserved

The migration preserves the core accepted classification:

**Analytics is a scoped synthesis and bounded guidance layer, not yet an intelligent decision-and-learning system.**

Current recommendations remain deterministic, threshold/evidence-driven review prompts. Durable recommendation identity, materialization and feedback do not establish causal outcome evaluation, personalization, predictive intelligence, automatic execution or a learning loop.

The prior financial-scope, historical-cost, historical-customer-state, threshold-validation and production-evidence issues remain valid.

## Product truth delta

The major connected-domain delta is explicit checkout-origin separation.

Current Analytics now distinguishes:
- standard operational/business-performance Orders, excluding `INCOMPLETE_CHECKOUT`;
- a separate incomplete-checkout recovery cohort;
- a broader economic population that can include canonical Orders across checkout origins.

This preserves upstream Shopify/Orders provenance instead of silently merging incomplete recovery into ordinary checkout performance.

The migration also correctly identifies a continuing Test Order population defect across active Merchant Analytics.

## V1.2 merchant job / friction reduction

Analytics reduces merchant reconstruction/calculation effort by assembling selected operational and economic evidence into one scoped surface.

Depending on evidence availability, the merchant does not need to manually:
- count/order lifecycle outcomes for the selected cohort;
- calculate basic rates and period comparisons;
- reconstruct selected profitability inputs from several owner-domain records;
- separately derive selected customer-quality impact summaries;
- manually translate deterministic thresholds into the same review prompts each time;
- merge selected Advertising evidence with operational outcomes for the supported view.

This is real calculation/reconciliation reduction.

It is not equivalent to eliminating judgment. The merchant must still interpret bounded evidence, resolve missing inputs, decide whether a recommendation is appropriate and execute actions in owner workflows.

## Tool / process consolidation

Analytics consolidates selected read-side evidence from Orders, Finance, Inventory/FIFO cost evidence, Customers, Advertising and Market Center into a merchant-scoped synthesis.

This can reduce some spreadsheet/export/manual comparison work.

It does not replace:
- complete accounting;
- external financial reconciliation;
- attribution tooling;
- experimentation;
- BI across arbitrary data;
- merchant judgment;
- owner-domain execution.

## Context continuity / provenance

A strong V1.2 pattern is now visible:

**source/capture provenance → canonical Order → operational lifecycle → selected financial/economic evidence → scoped Analytics calculation → deterministic guidance.**

The checkout-origin split demonstrates good provenance discipline:
- completed/standard checkout performance remains separate;
- incomplete recovery is reported separately;
- canonical recovered Orders can still participate in appropriate economic calculations.

This prevents one source distinction from being erased merely because all paths eventually produce an Order.

## Material Test Order issue

Director source verification confirms the migrated audit's finding.

The active `MerchantAnalyticsService` base `orderWhere` does not include `isTestRecord: false`.

That base population feeds current and prior operational calculations.

The incomplete-recovery and broader economic populations derive from the same predicate after removing only the standard `checkoutCaptureOrigin` exclusion. They likewise do not add a Test Order exclusion.

The resulting operational/profitability evidence is then passed into `buildDecisionCenter`, so eligible Test Orders can influence not only displayed metrics but also deterministic health/guidance outputs.

By contrast, `personalizedMarketOpportunity` explicitly applies `isTestRecord: false`.

Therefore adjacent Analytics/Market evidence can currently use inconsistent Test Order semantics.

This is a material Product/data-quality issue, not merely a UI issue.

Required Product resolution: define one explicit Test Order policy for Merchant operational metrics, recovery, profitability/economic calculations, Advertising-related Analytics and recommendation inputs, then enforce and test it consistently.

Until resolved, Analytics claims must remain qualified for production-data purity.

## Incomplete-checkout recovery semantics

The separate recovery panel calculates captured Orders and Confirmation-state outcomes, including `recoveredConfirmedOrders` and `recoveryRate = recoveredConfirmed / captured`.

That is a valid operational recovery-state metric.

It does **not** prove:
- explicit shopper purchase intent;
- incremental conversion;
- incremental revenue;
- collected cash;
- profitability;
- causal effectiveness of timeout recovery.

The upstream Shopify policy issue is controlling: an eligible `COLLECTING/orderReady` session can timeout-finalize before explicit Order Now.

Therefore “recovered confirmed Order” must not be promoted into “recovered sale” without additional evidence.

## Operational → economic → decision chain

Analytics currently reaches farther than most owner domains because it combines:
- operational cohort facts;
- selected economic calculations;
- period comparison;
- evidence-qualified deterministic rules;
- bounded recommendation materialization.

This is legitimate **Merchant Intelligence + bounded deterministic Decision Support**.

It still stops before full Decision Intelligence as defined by the project because current evidence does not establish robust interpretation/prioritization learned from outcomes, causal recommendation validation or closed-loop action guidance.

The distinction is important:
- calculation is not interpretation;
- threshold guidance is not learned recommendation;
- recommendation persistence is not outcome learning;
- feedback intent is not measured business consequence.

## Decision effort reduction

Current Analytics does reduce some decision preparation effort.

It can answer bounded questions such as:
- how selected operational rates changed;
- whether selected evidence crosses a deterministic watch threshold;
- whether enough evidence exists to calculate selected economics;
- which bounded review prompt is currently applicable.

It does not establish that the recommended action is optimal or that acting on it improves outcomes.

Thus the strongest present claim is **less manual calculation/reconciliation before a merchant review decision**, not “Wossol makes the decision.”

## Advertising boundary

Analytics can consume selected Advertising reporting evidence and Wossol-attributed operational outcomes under the chosen connection/account scope.

It does not reconstruct provider attribution or establish causality.

Provider purchases, Wossol matched outcomes, confirmed/delivered ROAS-style projections and reporting-evidence status must retain their distinct semantics.

Messenger exact-Ad identity evidence likewise cannot be upgraded into causal advertising performance merely by entering Analytics.

## Finance / profitability boundary

Analytics can calculate evidence-aware profitability projections from current repository inputs, but it cannot upgrade incomplete Finance truth into audited accounting.

Prior Finance limitations remain controlling:
- external settlement/cash truth is not complete;
- some costs/expenses require merchant declaration or bounded repository evidence;
- historical cost semantics can change with current/corrected cost layers.

The Product/Variant filtered financial-scope issue also remains: selected-item COGS can be combined with whole-Order realized revenue for mixed-item Orders.

Therefore filtered Product/Variant outputs must not be marketed as precise unit economics until Product resolves allocation/scope semantics.

## Market Center boundary

The current Market opportunity seam is useful because it combines a published Market Center snapshot with merchant-local qualifying evidence.

It explicitly excludes Test Orders, unlike the main Analytics populations.

This is both:
- a useful example of Market Intelligence being consumed by Merchant Analytics;
- evidence that Test Order policy is inconsistent across adjacent calculations.

Market Center evidence remains descriptive/sourced; Analytics consumption does not make it predictive national demand intelligence.

## Control / provenance / trust

Useful controls include:
- merchant/workspace/Store scoping;
- explicit periods;
- evidence availability/status;
- provider-vs-Wossol Advertising distinctions;
- deterministic recommendation thresholds;
- durable recommendation provenance;
- bounded feedback capture.

These improve inspectability and reduce silent reinterpretation.

They do not cure incomplete upstream evidence or Test Order contamination.

## Claims strengthened / weakened / unchanged

**Strengthened:** Analytics is stronger evidence for cross-domain calculation/reconciliation reduction and provenance-aware synthesis.

**Strengthened:** checkout-origin separation demonstrates that connected data can remain semantically distinct downstream.

**Strengthened:** current evidence supports bounded deterministic Decision Support, not merely dashboarding.

**Unchanged:** no predictive intelligence, autonomous optimization, causal diagnosis, complete attribution, audited profit or learning loop.

**Weakened/qualified:** data-quality confidence in active Merchant Analytics remains bounded because Test Orders can enter operational, recovery, economic and recommendation populations.

## Verification assessment

Recorded verification:
- 101/101 focused Analytics backend tests passed;
- backend typecheck passed;
- frontend typecheck passed.

This is strong source-level regression evidence for the inspected paths.

No DB integration, deployed migration verification, authenticated browser acceptance, representative production cohort audit or measured merchant outcome validation is established.

The Product commit is directly readable for Director source challenge; lack of a fresh Codex-side remote tip check does not alter the reviewed commit evidence.

## Open Product issues

1. Define and enforce a consistent Test Order exclusion/inclusion policy across operational, recovery, economic, Advertising-related and recommendation populations.
2. Resolve Product/Variant filtered financial scope for mixed-item Orders.
3. Define immutable/point-in-time historical cost semantics where historical profitability requires them.
4. Define point-in-time vs current-state customer reputation/block semantics for historical cohorts.
5. Preserve checkout-origin semantics and resolve upstream incomplete-checkout intent/consent policy.
6. Validate deterministic thresholds against real outcomes before predictive/optimization claims.
7. Establish recommendation action/outcome joins before Learning Intelligence claims.
8. Verify deployed migrations, representative data quality, source completeness and production usefulness.
9. Preserve Advertising provider-vs-Wossol evidence boundaries and avoid causal attribution inflation.
10. Establish complete financial truth before “real profit” claims.

## Claim / marketing safety

Safe current framing:
**Wossol brings selected operational, economic, customer, advertising and market evidence into one scoped Analytics view, performs bounded calculations/comparisons, and surfaces deterministic review guidance while preserving important evidence boundaries.**

A stronger V1.2 merchant-value framing is:
**Wossol can reduce some of the exporting, merging and calculation a merchant would otherwise perform before reviewing operational and commercial performance.**

Do not claim accurate audited profit, clean production-only cohorts until Test Order policy is fixed, incremental checkout recovery, complete ad-to-profit attribution, predictive intelligence, AI learning, causal diagnosis, autonomous optimization or proven outcome improvement.

## Strategic / brand implication

Analytics is important evidence for the working hypothesis around:
- Connected Commercial Truth;
- Reduced Merchant Work;
- Decision Support.

Its current strategic strength is **bringing selected operational and economic evidence together while preserving some provenance boundaries, then reducing calculation/reconciliation effort before a decision.**

That is stronger than “dashboard/reporting,” but it is still below a true decision-and-learning system.

The current Product supports a credible trajectory toward accumulating intelligence, but the brand must not claim the future layer before outcome measurement and learning exist.

## Methodology impact

No methodology change required. V1.2 correctly distinguishes:
- connected data from intelligence;
- calculation from interpretation;
- deterministic guidance from learning;
- operational recovery from incremental commercial recovery;
- canonical economic inclusion from shopper intent;
- recommendation persistence from outcome feedback.

## Retroactive impact

RR-V12-003 has completed its V1.2 Quality Gate.

The Test Order issue is cross-domain and must remain visible in later Market Center, Advertising and synthesis work.

Shopify/Orders incomplete-checkout intent semantics remain controlling for recovery interpretation.

Home continues to distribute bounded Analytics guidance and inherits the quality limits of its Analytics inputs.

No prior accepted section requires correction from this migration because the affected boundaries are already preserved as open Product issues.

## Final decision

**ACCEPT WITH OPEN PRODUCT ISSUES.**

Analytics / Decision Center is V1.2-complete for intelligence purposes. No Codex correction or re-audit is required before proceeding to Market Center.
