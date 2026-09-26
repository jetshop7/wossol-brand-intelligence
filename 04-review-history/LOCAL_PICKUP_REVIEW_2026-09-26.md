# Local Pickup Review — 2026-09-26

## Review metadata
- Section: Local Pickup
- Reviewed intelligence commit: `0115ed8ba9a3d6caddafcebf6d96912ee99dcc06`
- Product evidence commit: `020593219001f857cace7bf80ad80fe2930319d4`
- Reviewer: ChatGPT — Strategic Reviewer / Quality Gate
- Decision: **ACCEPT WITH OPEN PRODUCT ISSUES**
- Full re-audit required: No

## What passes

The audit correctly treats Local Pickup as a Store-scoped local inbound declaration/reconciliation workflow for already-selected Products/Variants, not as supplier sourcing, procurement, courier booking or physical warehouse receiving.

Targeted Product verification confirms the merchant/operations boundary. The merchant declares scoped cartons/Variants and pickup details; an authorized internal operator can mark the request as requested, but the executable action explicitly records that the provider was contacted manually. No provider dispatch/acceptance API is established by this transition.

The Inventory authority boundary is accurately preserved. Local Pickup does not write actual stock. Current completion is driven by persisted positive Inventory provider movements whose shipment or warehouse-manifest reference matches the pickup code and whose Variant belongs to the declared set.

The completion predicate is especially important and is correctly documented: every expected Variant must have a receiving line, but received quantity does not have to equal expected quantity. A short or over quantity opens a `QUANTITY_MISMATCH` alert yet the request can still transition to `INVENTORY_UPDATED`, deactivate expected rows and post enabled Finance effects in the same transaction. The audit does not misdescribe this as exact fulfillment.

The Finance boundary passes. The service fee is conditional on the snapshotted charging setting; an optional whole-pickup goods value can create a separate goods-purchase debit. Neither amount establishes verified supplier invoice economics, unit acquisition cost, cash settlement or true landed cost.

The two P1/P3 contradictions are material and correctly preserved:
1. Final V1 documents `by_courier` and `supplier_direct_delivery`, while current persistence/controller/service has no pickup-mode or supplier-specific execution branch.
2. Final V1 says `inventory_updated` may also result from operator verification, while current executable transition requires matching provider-movement evidence and exposes no manual completion route.

The audit correctly avoids resolving either contradiction in favor of the implementation. Product authority must decide whether the Final V1 contract is incomplete in P1 or requires revision/supersession.

The audit also appropriately separates evidence association from physical causality. Matching pickup-code reference + Variant in provider movements is executable association evidence; it does not independently prove physical collection, supplier identity, exclusive causal linkage or provider-reference quality in production.

Strategically, the section supports bounded Control/Accountability through declared-vs-observed quantities, scoped identity, alerts and transaction-linked Finance effects. It does not establish a sourcing network, automated local logistics, supplier intelligence or autonomous replenishment.

Verification discipline passes: the canonical audit records 35/35 backend service/scheduler tests passing and explicitly records that frontend source specs were inspected but not executed because `ts-node/register` was unavailable.

## Open product issues retained

1. **Pickup-mode contract:** reconcile Final V1 `by_courier` / `supplier_direct_delivery` with the current mode-less schema and workflow.
2. **Completion authority:** decide whether provider movement is the sole completion authority or whether evidenced operator verification remains approved scope.
3. **Mismatch policy:** explicitly decide whether short/over quantities should be allowed to finalize and trigger service/goods Finance postings before exception resolution.
4. **Provider reference integrity:** verify uniqueness, collision handling, delayed/corrected movements, reversals and production data quality for pickup-code matching.
5. **Physical collection evidence:** current request and movement evidence does not establish courier/provider acceptance or physical collection proof.
6. **Goods-value truth:** optional whole-pickup goods value lacks supplier invoice/unit-cost verification and must not become a procurement/landed-cost claim.
7. **Runtime/deployment:** scheduler topology, live provider movement behavior, production Finance effects, adoption and outcomes remain unverified.
8. **Competitive depth:** no direct feature-equivalence research establishes that this workflow is unique or superior.

## Claim / strategic safety

Safe present territory is Store-scoped local inbound declaration, carton labels, manually recorded pickup request, expected-vs-provider-evidenced Variant reconciliation, mismatch visibility and conditional Finance posting after evidence completeness.

Do not claim automated courier booking, supplier-direct executable mode, supplier sourcing, physical pickup verification, exact-quantity fulfillment, Local Pickup-owned stock updates, supplier payment, landed cost, supplier intelligence or measured operational improvement.

## Strategic implication

Together with External Shipping, Local Pickup strengthens a recurring Wossol pattern around inbound goods: declared intent and expected quantities remain distinct from later provider/physical evidence, while Inventory and Finance retain separate authority.

The two workflows should not be collapsed into one capability: External Shipping has materially richer physical warehouse/measurement and provider-pickup operations; Local Pickup is a lighter local declaration/evidence-reconciliation path. Their common strategic value is evidence-linked inbound control, not a sourcing or logistics-network claim.

## Methodology impact

No methodology change is required.

## Retroactive impact

No prior accepted section requires correction. Sourcing / Network remains correctly non-established. Inventory remains owner of actual stock/provider movements. Finance retains ledger authority. External Shipping remains a separate, richer inbound workflow.

## Acceptance

**Local Pickup passes the Director Quality Gate and is accepted with the open Product issues above.**

No Codex correction or re-audit is required.

The next required coverage section is **Support / Internal Chat**.
