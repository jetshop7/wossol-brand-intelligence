# PRODUCTS — QA Investigation: Test Orders and Cost Allocation Coverage
**Study status:** Independent source inspection complete for specific code paths; integrated acceptance pending.  
**Date:** 2026-10-09  
**Current code basis:** jetshop7/wossol-platform, dev/wossol-integration, repository tree bf2d82f46d3189250e2a0ced6e6010d85c4de699.  
**Classification:** Brand Intelligence working evidence; NOT production verification, NOT final PRODUCTS.md.

## Executive findings
1. **Test/Real isolation gap in Merchant Analytics — source-level verified.** MerchantAnalyticsService.getMerchantAnalytics builds standard and economic Order queries without an isTestRecord:false filter. It does not select the flag into the reporting projection, and its operational method increments Order, Product, Variant, Store and Confirmation counters for every returned Order. The separate AnalyticsService *does* require isTestRecord:false. Prior blanket statements about universal analytics Test isolation are therefore incorrect.
2. **Cost completeness gap — source-level verified with integrated risk pending test.** calculatePersistedCogs receives an array of persisted allocations. On an empty array it returns amount 0.00, status COMPLETE, no reasons. The function does not establish expected Order Item cost-coverage quantities. Real FIFO allocation only runs after qualifying provider movement and reservation/balance reconciliation. Thus absence of rows is not logically proof that an eligible consumed Item cost is zero. Integrated reproduction and prevalence remain unverified.
3. **Canonical Finance collection narrows partial-payment concern.** FinanceService.confirmCollection requires a DELIVERED Order with current provider Shipment and full amount matching totalToCollect in matching currency, with one collection per Order. Do not portray routine partial collection as a supported path without separate evidence.
4. **Test Orders have actual operational boundaries and potential costs.** Test Orders are Wossol Confirmation intent only. ConfirmationDispatch rejects dispatch with TEST_ORDER_DISPATCH_FORBIDDEN; Orders does not reserve inventory for tests. Finance has configurable TEST_ORDER_CREATED operational fee, tested with a nonzero amount. Therefore Test expense may be real even when Test shipment/revenue is normally not.

## A — Products × Orders × Confirmation × Finance × Analytics

**Feature:** Purpose-based Product/Order classification, Test confirmation, distinct Finance creation fee, Merchant performance/profitability projections.

**Merchant Problem:** A product test is not a real commercial sale; including it in standard order counts, units ordered or confirmation-rate denominators can mislead campaign/replenishment decisions. Equally, any actual Test fee should not disappear from accounting.

**Mechanism observed:** The canonical Order enforces Product Test/Real purpose and prevents mixed Orders. MerchantAnalyticsService.getMerchantAnalytics omits the Test exclusion in its standard orderWhere and derived economicOrderWhere. Its operational(orders) counts every passed Order and active Item; wossolConfirmationEligible and matching outcome fields also have no Test guard. Test creation may generate a fee assessed to the Order.

**Manual Work Impact:** A merchant might have to manually distinguish Test volume or Test charges to understand real demand/operational performance, instead of relying on the intended separation.

**Control, Transparency, Safety:** Product isTestProduct and Order isTestRecord are persisted. But the studied Merchant Analytics read does not enforce the separation. The sister AnalyticsService explicitly excludes Test; a Market Opportunity query in MerchantAnalyticsService does also, underlining inconsistency.

**Current Product Strength:** Test purpose is protected in Orders, reservation and dispatch.  
**Cross-System Classification:** VERIFIED inconsistent inter-section enforcement; not an approved Compound Product Advantage.

**QA demonstration:** Create separate eligible Real and Test Products/Variants in the same Store/period; record a Real Order and a Test Order with correct intent; include a fee profile that generates a nonzero TEST_ORDER_CREATED fee. Compare Merchant Analytics standard count, product units, confirmation rate, fee and unit-economics cohort versus separate AnalyticsService behavior. Tests cannot combine Test and Real Products in one Order. Do not fabricate a delivered Test Order through canonical shipment flow.

**Marketing & Website:** HOLD all claims saying every Test Order is excluded from standard metrics or Product economics. Safe narrow wording: Test Products use distinct order/stock/Shopify-COD controls in verified paths; not all Merchant Analytics calculations have demonstrated purpose isolation.

**Brand relevance:** Honest performance evidence and data trust require purpose-consistent reporting; the absence of a filter can negate an otherwise strong Test/Real differentiation story.

**Code evidence:**
- https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/analytics/merchant-analytics.service.ts#L194-L375
- https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/analytics/merchant-analytics.service.ts#L709-L916
- https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/analytics/analytics.service.ts#L123-L150
- https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/orders/orders.service.ts#L3825-L3853
- https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/confirmation/confirmation-dispatch.service.ts#L1664-L1713
- https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/finance/finance.service.spec.ts#L76-L95

## B — Products × Inventory Costs × Orders × Analytics

**Feature:** COGS and gross profit from persisted outbound FIFO Cost Allocations and current cost-layer unit cost.

**Merchant Problem:** Missing stock-cost evidence must not look like a free-to-acquire product. There is a crucial difference between confirmed zero cost, an uncovered quantity, and cost allocation that has not been reconciled yet.

**Mechanism observed:** InventoryCostService records explicit UNCOVERED allocations when it runs and finds too few inbound Cost Layers. However, allocation only follows independently qualifying outbound provider movement and reservation/balance matching. Analytics reads whatever allocation rows exist and calculates COGS from them, without checking per-Order Item expected quantity coverage. calculatePersistedCogs([]) returns COMPLETE 0.

**Manual Work Impact:** A merchant may have to compare provider movements, Inventory cost allocations and Analytics independently when costs have not yet been reconciled.

**Control/Transparency/Safety:** For *recorded* allocations, missing unit cost and UNCOVERED quantity are transparently marked INCOMPLETE and can link to Inventory. For *absent* allocations, the studied aggregator contains no expected-coverage evidence or warning.

**Current Product Strength:** Auditable FIFO allocation and explicit UNCOVERED preservation once reconciliation occurs.  
**Cross-System Classification:** CONDITIONAL success verified; missing-allocation completeness guarantee NOT VERIFIED; potential financial evidence integrity risk.

**QA demonstration:** 
- A fully covered outbound Order Item with known cost → COMPLETE expected total.
- An explicitly UNCOVERED quantity → INCOMPLETE with affected Variant.
- A missing unit cost on a known layer → INCOMPLETE.
- A genuinely cost-requiring Item with *no* allocation rows → check current COMPLETE-zero result vs expected PENDING/INCOMPLETE.
- A provider movement that arrives later and passes exact reservation/balance reconciliation → update costs and compare old/new profitability status.
- A valid zero-cost / non-cost-requiring situation → ensure it is not falsely treated as missing.
Do not claim a proven production financial misstatement until realistic integrated fixtures reproduce it.

**Marketing & Website:** HOLD unqualified 'always accurate COGS', 'true real-time Product profit', or 'no missing acquisition costs'. Safe wording limited to explicitly recorded and qualified source evidence.

**Brand relevance:** Evidence-based clarity only works if absence of evidence is not itself interpreted as evidence of zero cost.

**Code evidence:**
- https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/analytics/analytics-financial.ts#L211-L268
- https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/analytics/merchant-analytics.service.ts#L1834-L2055
- https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/inventory/inventory-cost.service.ts#L120-L184
- https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/inventory/inventory-provider-sync.service.ts#L270-L306
- https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/inventory/inventory-reservation.service.ts#L995-L1115

## C — Finance collection rule and corrected research assumption

The FinanceService.confirmCollection implementation checks current DELIVERED Shipment identity, Order currency and exact full totalToCollect amount before creating a collection; it checks for an existing collection and replays without recharging. Therefore the earlier concern that standard admin Finance can record arbitrary partial received amounts is not supported. This does NOT establish that every possible legacy writer has been inspected.

Source: https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/finance/finance.service.ts#L297-L380

## Final status and next product engineering acceptance gate

**Verified by current source inspection:** MerchantAnalytics Test-purpose filter omission; source-level unconditional aggregation; explicit Finance Test creation fee; canonical Test dispatch prohibition; COGS empty-array COMPLETE-zero; FIFO allocation conditional reconciliation; exact canonical Finance collection requirement.

**Pending executable QA:** reproducible before/after Merchant Analytics Test cohort numerical effect; realistic delivered/collected Order with pending cost allocations; safeguard for expected per-Item cost quantities; comprehensive downstream reporting and deployed behavior.

**Priority:** HIGH for both reporting integrity gates before publicly claiming reliable full-product financial intelligence or universal Test analytics isolation. Risk classification only, not a prediction about customer data.

**Change discipline:** No modification of wossol-platform software was requested or made. No repository tests were executed in this research pass. No final PRODUCTS.md created. Historic Brand Intelligence reconciliation remains reserved until independent Products study is complete.
