# Inventory — Section Intelligence

## 1. Audit Metadata

- **Audit date:** 2026-09-25.
- **Methodology:** `MASTER_INSTRUCTIONS.md` v1.1; current operating protocol.
- **Competitive reference:** `WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md` v1.
- **Intelligence source:** `jetshop7/wossol-brand-intelligence`, `main`, `2066f46`, clean and synchronized before inspection.
- **Product source:** `jetshop7/wossol-platform`, `dev/wossol-integration`, `e3912a967827bde06450d3510228e5a5ca9e78a7`, clean.
- **Evidence basis:** P1 executable code/schema, P2 focused automated tests and typechecks, P3 Final V1 Inventory UI specification, and P4 dated/architecture material only where explicitly qualified. Static evidence does not prove deployment, live provider connectivity, data quality, or merchant adoption.

## 2. Audit Coverage Map

| Surface | Coverage | Evidence |
|---|---|---|
| Merchant UI | scoped list, summary, filters, detail, refresh, recommendation review, cost controls | EV-INV-001–003 |
| Core service | effective availability, operational metrics, restocking decision, freshness, scope and permissions | EV-INV-004–006 |
| Provider and reservation lifecycle | snapshots, movement mirror, leases, reconciliation, locking, release/consume evidence | EV-INV-007–009 |
| Cost provenance | approved-receipt layers, FIFO allocation, controlled corrections | EV-INV-010 |
| Data model and connected domains | mappings, reservations, movements, shipping/pickup/order signals | EV-INV-011–012 |
| Verification and specification comparison | focused tests/typechecks; Final V1 contract comparison | EV-INV-013–014 |

Not inspected as live fact: Accurate/Mayar account behavior, production sync history, production database values, provider latency, warehouse operations, merchant decisions, or commercial outcomes.

## 3. Executive Section Truth

Inventory is an implemented, merchant-scoped operational decision surface. It does more than display a provider quantity: the availability exposed for new orders is provider on-hand less active reservations and consumption still awaiting authoritative provider confirmation. It then combines that protected availability with bounded, workspace-pooled operational signals—eligible inbound, waiting-for-stock demand, confirmed unsent demand, and the last seven days of delivered order history—to produce a freshness-aware stock-cover and replenishment decision.

The decision is deliberately qualified. Unknown or delayed provider evidence prevents a numeric replenishment suggestion; expected stock informs sizing but never becomes available stock; and recording that a merchant reviewed a decision preserves a point-in-time decision snapshot without claiming the decision caused a later outcome. This is credible operational inventory intelligence, not predictive AI, automatic procurement, supplier management, or verified profitability intelligence.

## 4. Scope & Architecture Map

Inventory owns the merchant-facing projection and operational interpretation of stock. Accurate/Mayar snapshots remain raw on-hand truth; the reservation service owns protected effective availability; Orders owns lifecycle/demand state; External Shipping and Local Pickup own inbound records; Products owns Variant identity; and Finance is not made authoritative by Inventory cost layers. A Store chooses which Product/Variant rows are visible in an active Workspace; production stock is a shared Workspace/Variant pool, not a Store-owned pool.

Merchant endpoints are `/inventory/merchant`, `/inventory/merchant/summary`, `/inventory/merchant/:variantId`, `/inventory/merchant/refresh`, and narrowly authorized cost/review endpoints. The Merchant UI routes are `/merchant/inventory` and `/merchant/inventory/detail`.

## 5. Current Capability Inventory

| Capability | Status | Current behavior |
|---|---|---|
| Variant-level stock view | LIVE when provider evidence exists | provider-backed effective availability, never fabricated as zero when unknown |
| Reservation protection | LIVE | ACTIVE and pending-provider-sync consumption reduce new-order availability |
| Provider sync and freshness | LIVE, provider-dependent | scheduled three-hour default, lease/retry/batching, manual cooldown, delayed/unknown states |
| Operational awareness | LIVE | inbound, waiting orders, confirmed demand, in-delivery/delivered quantities remain separate from stock |
| Supply decision | LIVE, derived | 30-day target, 7/21-day boundaries, freshness/data gates, explainable reasons and quantity where safe |
| Decision evidence | LIVE | canonical snapshots and idempotent merchant review evidence; explicitly non-causal outcome display |
| Movement and reservation timeline | LIVE | bounded provider movement, reservation, and cost evidence |
| Receipt-linked cost provenance | LIVE | approved-manifest inbound layers, FIFO allocations, explicit uncovered allocation, auditable unit-cost correction |
| Stock editing or automatic purchasing | NOT IMPLEMENTED | merchant cannot directly change provider stock; no purchase order/supplier/autonomous restock flow found |
| Prediction/profit optimization | NOT IMPLEMENTED | no predictive model, vendor lead-time model, margin or outcome optimizer established |

## 6. Workflow & Lifecycle

1. The active merchant Workspace and Store filter select visible linked Variants; the provider-backed pool stays Workspace/Variant scoped.
2. Sync acquires a durable provider lease, reads bounded Accurate/Mayar product batches with retry, records snapshots, and mirrors immutable product-transaction facts. Manual requests have a cooldown; a held lease returns `BUSY`.
3. Effective availability is raw provider on-hand minus active reservations and consumed stock still pending provider evidence. Unknown provider quantity remains unknown.
4. Before dispatch, outbound commercial order lines are scope-checked, deterministically locked, preflighted in aggregate, then reserved or placed in waiting-for-stock mode. Release and consume transitions append durable evidence.
5. Provider shipment/movement evidence—not a later balance alone—reconciles protected consumption. The implementation fails closed when causal proof is insufficient.
6. Inventory derives stock cover from delivered-order velocity, classifies risk, and emits a live supply decision. Unknown/delayed stock produces data-quality resolution rather than an invented order quantity.
7. Approved warehouse-manifest receipts can create cost layers; authoritative outbound facts allocate FIFO cost or record explicit uncovered quantity. Cost never mutates physical availability.

## 7. Value Recipient Map

| Recipient | Current value |
|---|---|
| Merchant operator | one actionable, scoped view of safe-to-sell stock, inbound context, waiting demand, and evidence quality |
| Merchant owner/manager | reviewable replenishment rationale, delegated read/cost authority, auditable cost changes |
| Order operations | protected allocation before dispatch and safe release/consumption behavior |
| Warehouse/receiving operations | approved inbound movement and receipt provenance visible without reclassifying expected stock as stock on hand |
| Finance/accounting workflows | FIFO-ready acquisition-cost evidence with explicit coverage gaps, not inventory authority |
| Support/internal operations | durable snapshots, transitions, provider movement evidence, and merchant-safe explanations |

## 8. Control & Merchant Agency

Merchants control context, filters, manual sync request, decision review, and—only with owner or `inventory.cost.edit` authority—unit-cost corrections on an in-scope layer. They do not edit stock, force provider reconciliation, change reservations directly, mark expected stock received, or create purchase orders. Read access requires active merchant/workspace scope plus owner or `inventory.view`; Product-only access cannot activate Inventory sync.

## 9. Transparency & Trust

- Available means protected effective availability, not raw provider quantity; expected, demand, and delivery values are separately shown.
- Freshness is explicit: no successful provider timestamp is Unknown and more than two sync intervals is Delayed.
- The recommendation carries inputs, reasons, state, confidence, and evaluation time. A delayed/unknown source produces an insufficient-data decision rather than false precision.
- Provider movements, reservation transitions, cost layers, and high-sensitivity cost-change audit events preserve evidence while raw provider mechanics stay merchant-safe.
- A reviewed decision is captured idempotently; the displayed later outcome explicitly says it is not attributed to that action.

## 10. Merchant Value Extraction

The practical value is fewer unsafe stock decisions: the operator can distinguish a sellable protected quantity from inbound awareness, understand whether evidence is fresh enough to act on, see orders already waiting for stock, and receive a bounded replenishment quantity only when evidence supports it. It turns operational data into an explainable next review, not an opaque forecast.

## 11. Feature Clusters

1. **Protected availability:** provider snapshot plus reservation lifecycle avoids selling the same known unit twice during dispatch/provider lag.
2. **Evidence-gated supply decision:** delivered velocity, inbound, waiting demand, and freshness create an explainable 30-day coverage recommendation.
3. **Truthful operational awareness:** expected inbound and demand pressure inform action but never silently deduct or inflate stock.
4. **Receipt-to-cost evidence:** approved receipts, FIFO allocation, coverage gaps, and auditable cost changes preserve accounting provenance without creating a second stock authority.

## 12. Merchant Journey / Old Way vs Wossol Way

| Stage | Common risk | Current Wossol behavior |
|---|---|---|
| See stock | raw or stale quantity looks sellable | protected availability and explicit freshness/unknown state |
| Allocate an order | concurrent final-unit sales or premature release | deterministic locks, scoped reservations, evidence-gated reconciliation |
| Plan replenishment | inbound/demand are mentally combined or double-counted | separate signals with a reproducible 30-day calculation |
| Respond to uncertainty | an apparent recommendation hides weak data | resolve-data-quality state with no fabricated quantity |
| Understand cost | receipt and outbound cost relationship is lost | approved receipt layers, FIFO allocation, explicit uncovered evidence |

## 13. Hidden / Non-Obvious Advantages

- Pending provider-sync consumption remains protected, so a successful dispatch is not immediately re-offered merely because the provider cache has not caught up.
- A later provider balance alone cannot consume a reservation; exact shipment/product movement and reflected balance conditions protect against accidental causal inference.
- All Stores does not sum or arbitrarily select per-store provider quantity. Conflicting development fixtures become Unknown rather than an invented aggregate.
- The recommendation snapshot is separate from the read projection: persistence failure does not make stock visibility fail.

## 14. Data & Intelligence Assets

Current durable assets include Variant provider mappings and confirmed quantities, provider snapshots and immutable movements, reservation/transition histories, merchant alert state, external-shipment and local-pickup expected items, order lifecycle history, recommendation snapshots/review evidence, receipt-linked cost layers, FIFO allocations, and cost-change/audit events. These are an unusually useful future decision foundation because their scopes and authority boundaries are explicit.

They are not yet proof of demand forecasting, supplier intelligence, inventory valuation, margin optimization, or replenishment outcomes.

## 15. Cross-Section Compound Advantages

- **Products × Inventory:** exact Variant identity and Store visibility make stock decisions interpretable without making Products a stock authority.
- **Orders × Inventory:** reservations, waiting-for-stock, confirmed demand, and delivered velocity connect execution to supply review while Test Orders are excluded from Inventory metrics.
- **External Shipping/Local Pickup × Inventory:** eligible inbound is visible as awareness and can reduce a safe replenishment suggestion without becoming available stock.
- **Warehouse/provider evidence × Inventory costs:** approved receipt and outbound movement facts create accountable cost provenance without altering availability.
- **Home × Inventory:** Home can route attention to Inventory, but Inventory remains the authoritative decision surface.

## 16. Competitive Analysis

The competitive master identifies warehouse visibility, low-stock warning, replenishment, multi-country stock, inbound handling, and cost provenance as relevant expectations. Wossol currently demonstrates a stronger-than-basic candidate in reservation-aware, freshness-gated, evidence-explained supply review and receipt-to-cost provenance. The master does not establish that competitors lack equivalent mechanisms; that is absence of evidence, not a superiority claim.

Wossol is not yet evidenced as a full warehouse management system, multi-warehouse optimizer, supplier network, automated procurement tool, or predictive inventory platform.

## 17. Marketing Intelligence

**Asset ID:** INV-01
**Capability:** Evidence-aware replenishment review
**Evidence:** EV-INV-004–006, EV-INV-013. **Status:** GREEN for implemented calculation; YELLOW for live data and business outcomes.
**Problem:** operators act on stock numbers that may be stale, already committed, or confused with inbound.
**Angle:** “Review what to replenish with stock, inbound, demand, and data freshness shown together.”
**Qualification:** recommends review; it does not purchase, predict, or guarantee availability.

**Asset ID:** INV-02
**Capability:** Protected availability through provider lag
**Evidence:** EV-INV-007–009, EV-INV-013. **Status:** GREEN for tested executable behavior; YELLOW for production reliability.
**Angle:** “Keep confirmed stock protected while dispatch evidence catches up.”
**Qualification:** provider truth/connectivity and actual avoidance outcomes are unverified.

## 18. Surprise Findings

The meaningful differentiator is not the stock table. It is the refusal to infer that a changed provider balance caused a particular order consumption, together with the refusal to size replenishment when freshness or velocity cannot support it. The cost layer is likewise intentionally evidence/accounting-oriented rather than a hidden stock override.

## 19. Potential Category Reframes

Current defensible framing: **inventory decisions with evidence**, **stock you can act on**, or **protected operational availability**. Avoid “AI inventory manager,” “real-time prediction,” “automated replenishment,” “warehouse operating system,” “inventory valuation,” or “profit-optimized stock.”

## 20. Brand Evidence

- **Control:** merchants can review decisions and narrowly correct cost evidence, while unsafe stock mutation remains outside the UI.
- **Transparency:** freshness, uncertainty, inbound, demand, and reasons are visible instead of merged into a single quantity.
- **Accountability:** reservations, provider movements, receipt cost layers, and high-sensitivity cost changes preserve traceability.
- **Reliability orientation:** locking, leases, idempotency, bounded replay, and fail-closed reconciliation prioritize trustworthy operations over apparent immediacy.

## 21. Weaknesses / Risks / Gaps

1. Provider truth is cached and provider-dependent; code cannot establish that production refreshes are timely or complete.
2. Supply sizing uses only seven days of delivered velocity and a fixed 30-day target. It has no verified lead time, seasonality, supplier constraint, margin, or service-level model.
3. There is no supplier, purchase order, automatic replenishment, stock transfer, barcode, or merchant stock-adjustment workflow.
4. Cost layers are usable provenance, not valuation/profit intelligence; missing cost remains explicitly uncovered.
5. The Final V1 specification calls the detail surface read-only and says automatic restock suggestions are deferred, while P1 implements a separately permissioned cost correction and a live derived recommendation. These must not be silently treated as the specification having been superseded (see contradictions).
6. The specification says expected External Shipping begins only after receipt confirmation, whereas P1 includes submitted `CREATED` shipment records in expected stock. This changes awareness/sizing—not available stock—but remains an approved-contract contradiction.
7. The UI label `Refresh` triggers a manual provider sync request; the Final V1 contract says a sync-triggering label must be explicit. The backend protects scope and cooldown, but the product wording remains unresolved.

## 22. Future Strategic Potential

| Category | Assessment |
|---|---|
| Current foundation | protected availability, causal movement evidence, incoming-stock awareness, runnable supply decision, cost provenance |
| Inferred extension | lead-time-aware, demand/seasonality-aware replenishment with measured outcomes and supplier constraints |
| Strategic relevance | could connect stock, acquisition cost, delivery/return outcomes, and channels into qualified availability and profit decisions |
| Brand relevance | may eventually support “know what to stock next and why”; it does not support autonomous or predictive claims today |

## 23. Claim Safety

| Claim | Safety | Reason |
|---|---|---|
| See inventory at Variant level with freshness and risk context | GREEN, provider-qualified | implemented projection; live data quality unverified |
| Protect stock during dispatch/provider synchronization | GREEN, outcome-qualified | tested reservation/reconciliation rules; production outcomes unverified |
| Get an explainable replenishment review quantity when evidence is fresh | GREEN, qualified | deterministic live-derived rule, not a forecast or order automation |
| Keep inbound and demand distinct from available stock | GREEN | explicit P1 boundaries and tests |
| Automatically replenish or purchase inventory | RED | not implemented |
| Predict future demand or optimize profit | RED | no qualifying model/outcome projection found |
| Run a warehouse or multi-location inventory network | RED | Store is visibility scope, not independent production inventory ownership |

## 24. Commercial Magnitude

**High operational magnitude.** Inventory shapes whether a merchant can accept and fulfil orders safely, and its uncertainty signals can prevent costly action on stale data. The direct commercial appeal is strongest for merchants operating a provider-backed delivery/COD flow. Differentiated commercial value depends on proven live freshness, provider reliability, and better replenishment outcomes.

## 25. Strategic Classification

| Area | Classification |
|---|---|
| Variant stock visibility, low-stock status, manual refresh | TABLE STAKES / MUST MATCH |
| Expected inbound and order-awareness separation | PARITY / MUST MATCH |
| Reservation-aware availability and causal reconciliation | WOSSOL STRONGER / potential differentiator |
| Freshness-gated, evidence-explained supply review | POTENTIAL DIFFERENTIATOR |
| Receipt-to-cost provenance with explicit uncovered allocation | POTENTIAL DIFFERENTIATOR |
| Supplier/procurement/forecast optimization | WHITESPACE / future |

## 26. Action Register

| Priority | Action | Rationale |
|---|---|---|
| MUST FIX | Resolve or explicitly amend the Final V1 contract for `Refresh`, expected-shipment eligibility, read-only detail, and deferred automatic suggestions. | P3/P1 contradictions affect what merchants and marketing can safely expect. |
| MUST MATCH | Add merchant-safe remediation for delayed/unknown provider inventory and measurable sync health. | Current uncertainty is truthful but resolution path and production evidence are limited. |
| MUST BEAT | Validate decision outcomes before evolving the fixed policy into stronger replenishment guidance. | Avoid converting a transparent rule into an unproven promise. |
| WORTH ADOPTING | Add explicit inbound ETA/receipt-confidence only where provider evidence supports it. | Current `eta` is null; timing claims need provenance. |
| DO NOT COPY | Do not fake real-time forecasts or collapse expected/demand values into stock. | Would undermine the strongest current trust boundary. |
| POTENTIAL MOAT | Preserve causal provider movement, reservation, decision, and cost evidence as the operational graph expands. | The evidence chain is harder to recreate than a stock dashboard. |

## 27. Evidence Register

**EV-INV-001 — Merchant Inventory list UI**
**Type:** P1. **Path:** `apps/frontend/src/app/merchant/inventory/page.tsx`.
**Observed:** Workspace/Store context, server-filtered bounded list, summary cards, explicit stock-cover/demand/recommendation presentation, data-quality state, detail/product routes, and manual refresh control. **Confidence:** High.

**EV-INV-002 — Client Inventory boundary**
**Type:** P1. **Path:** `apps/frontend/src/app/merchant/inventory-data.ts`.
**Observed:** typed scoped reads, refresh/review/cost requests, presentation-only decision wording, and no client-side supply calculation. **Confidence:** High.

**EV-INV-003 — Detail and cost controls**
**Type:** P1. **Paths:** `apps/frontend/src/app/merchant/inventory/detail/page.tsx`, `inventory-cost-layers.tsx`, `inventory-cost-validation.ts`.
**Observed:** merchant detail/timeline/decision-review and conditional cost-layer correction affordance. **Confidence:** Medium-high; route/component behavior is further validated by typecheck.

**EV-INV-004 — Inventory projection and supply decision**
**Type:** P1. **Path:** `apps/backend/src/modules/inventory/inventory.service.ts`.
**Observed:** scoped candidate projection, pooled operational metrics, 7-day delivered velocity, freshness, risk states, 30-day suggested quantity, expected-stock and waiting-demand treatment, decision snapshots/reviews, and non-causal learning outcome. **Confidence:** High.

**EV-INV-005 — Merchant API and authority boundaries**
**Type:** P1. **Paths:** `inventory.controller.ts`, `inventory.service.ts`, `permissions.service.ts`.
**Observed:** access-token guard, active merchant/workspace/store checks, owner or `inventory.view` read access, owner or `inventory.cost.edit` correction access, and authorization before manual sync. **Confidence:** High.

**EV-INV-006 — Classification policy**
**Type:** P1. **Paths:** `inventory-supply-policy.ts`, `inventory-alert-condition.ts`, `inventory-sync-interval.ts`.
**Observed:** 30/21/7-day policy, merchant-safe low/out state, and three-hour default sync interval with one-hour floor. **Confidence:** High.

**EV-INV-007 — Effective availability and allocation**
**Type:** P1. **Path:** `inventory-reservation.service.ts`, `inventory-reservation-policy.ts`.
**Observed:** provider cache/fixture source selection, ACTIVE/pending-consumption subtraction, deterministic Variant locks, aggregate preflight, scoped reservation creation, waiting mode, releases, and evidence. **Confidence:** High.

**EV-INV-008 — Provider sync and movement evidence**
**Type:** P1. **Paths:** `inventory-provider-sync.service.ts`, `inventory-provider-sync.scheduler.ts`, `inventory-provider-evidence.signal.ts`.
**Observed:** durable lease, batch/retry/cooldown, snapshot-before-movement ordering, bounded overlapping movement replay, idempotent provider facts, and guarded reconciliation delegation. **Confidence:** High; live provider behavior unverified.

**EV-INV-009 — Reservation reconciliation safeguards**
**Type:** P1/P2. **Paths:** `inventory-reservation.service.ts`, `inventory-reservation.service.spec.ts`.
**Observed:** pending consumption is not released by a later balance alone; exact movement identity/timing/reflection conditions govern confirmation and replay is idempotent. **Confidence:** High.

**EV-INV-010 — Cost provenance**
**Type:** P1/P2. **Path:** `inventory-cost.service.ts`, cost specs.
**Observed:** only approved-manifest inbound creates a layer; FIFO outbound allocations are locked, uncovered quantity is explicit, and scoped cost correction produces audit/domain evidence without provider-stock mutation. **Confidence:** High.

**EV-INV-011 — Durable data model**
**Type:** P1. **Path:** `apps/backend/prisma/schema.prisma` (Inventory cost, decision, alert, mapping, reservation, snapshot, movement, fixture models).
**Observed:** durable Workspace/Merchant/Store/Variant-scoped evidence and idempotency structures. **Confidence:** High.

**EV-INV-012 — Connected operational inputs**
**Type:** P1. **Path:** `inventory.service.ts` (`getOperationalMetrics`) and related schema relations.
**Observed:** eligible external-shipment/local-pickup expected items and non-test Orders feed awareness/recommendation, while none changes effective availability. **Confidence:** High.

**EV-INV-013 — Verification**
**Type:** P2. **Commands:** focused backend inventory specs using `node --test -r ts-node/register -r tsconfig-paths/register`; `pnpm --filter @wossol/backend typecheck`; `pnpm --filter @wossol/frontend typecheck`.
**Observed:** 116 focused backend assertions passed; both typechecks passed. A direct `node --test` invocation without the repository’s TypeScript preloads failed module resolution, then the configured preload invocation passed. Standalone frontend Inventory specs could not use that backend-local preload because the frontend package does not declare it; no pass/fail assertion is claimed for them. **Confidence:** High for recorded checks.

**EV-INV-014 — Final V1 specification comparison**
**Type:** P3. **Path:** `docs/ui/merchant/MERCHANT_INVENTORY_UI_SPEC.md`.
**Observed:** approved intent establishes read-only stock awareness, Workspace/Store context, separation of available versus awareness values, and no automatic restock/prediction claim. Material P3/P1 differences remain recorded below; P1 does not silently supersede the contract. **Confidence:** High for classification and contradiction identification.

## 28. Contradictions & Uncertainty

1. **CONTRADICTION-INV-001 — Replenishment contract:** P3 calls automatic restock suggestions deferred, while P1 returns a live-derived `INVENTORY_SUPPLY_DECISION_V1`, including suggested quantity when its evidence gates pass. It is a merchant review recommendation, not automation, but approved intent must be reconciled before broader product/marketing claims.
2. **CONTRADICTION-INV-002 — Detail mutability:** P3 says Detail must remain read-only unless a future owner-system action is approved. P1 exposes permission-gated unit-cost correction, with no stock mutation. This may be an intentional financial-evidence exception, but it is not documented as such in the Final V1 contract.
3. **CONTRADICTION-INV-003 — Expected-shipment start:** P3 makes receipt-confirmed/in-transit/arrived eligible and excludes `CREATED`; P1 includes submitted `CREATED` External Shipping rows in expected stock. It does not change availability, but it can influence the displayed inbound amount and replenishment quantity.
4. **CONTRADICTION-INV-004 — Refresh wording:** P3 requires a sync-triggering action to be labeled `Request Sync`; P1 UI labels the action `Refresh` while its POST invokes provider synchronization. Scope/cooldown controls exist, but wording is unresolved.
5. **CONTRADICTION-INV-005 — Cached language:** P3 describes cached Smart Stock and `last calculated`; P1 computes a live backend-derived decision and evaluation time. The implementation is more explicit about this than the contract, but the contract remains unamended.
6. No runtime product/provider/database observation was performed. Implemented status does not demonstrate data accuracy, scheduled-job health, live latency, or fulfillment outcomes.

## 29. Open Questions

1. Does product authority approve the current live recommendation as the intended replacement for the deferred P3 automatic-suggestion language, and what merchant wording is allowed?
2. Is cost-layer correction an approved exception to read-only Detail, and should the Final V1 specification state its financial-evidence boundary?
3. Which External Shipping status should legitimately count as expected inbound, and why?
4. What production freshness/SLA, provider failure rate, and reconciliation outcomes support merchant-facing reliability claims?
5. What lead-time, supplier, seasonal, service-level, and margin evidence would be required before stronger supply guidance?
6. Which alerts/remediation actions are merchant-safe without exposing provider implementation detail?

## 30. Methodology Learnings

No reusable methodology change identified. The existing hierarchy was sufficient: P1 establishes executable behavior; P3 preserves Final V1 intent; P2 supports tested behavior; and P3/P1 differences remain explicit instead of being converted to a “by design” conclusion.

## 31. Retroactive Review Impact

No methodology change and no retroactive queue entry. Inventory findings should inform future audits of External Shipping, Local Pickup, Finance, and provider integration, but they do not justify changing completed section records.

## 32. Canonical Section Takeaway

Inventory is Wossol’s evidence-aware operational stock decision surface: it protects provider-backed availability through reservation and synchronization lag, keeps inbound/demand awareness distinct from stock, and offers an explainable replenishment review only when evidence is fresh enough. Its strongest current claim is disciplined inventory decision support—not prediction, autonomous procurement, or warehouse control—and its Final V1 contract contradictions need resolution before the capability is marketed more broadly.
