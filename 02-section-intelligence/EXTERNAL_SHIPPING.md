# External Shipping — Section Intelligence

## 1. Audit Metadata

- **Audit date:** 2026-09-26.
- **Methodology:** `00-methodology/MASTER_INSTRUCTIONS.md` v1.1; `CODEX_OPERATING_PROTOCOL.md` v1.0.
- **Competitive reference:** `WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md` v1.
- **Intelligence source:** `jetshop7/wossol-brand-intelligence`, `main`, clean and synchronized to `e97120b2560ab5adf92949437415e2f9f1507285` before task interpretation.
- **Product source:** `jetshop7/wossol-platform`, `dev/wossol-integration`, `c78be7dfde91c421881db8fe6ec468397cc67e8a`, clean and equal to upstream at the initial source-state check. A final read-only check later found six uncommitted Shopify/COD-form changes in the backend configuration service/spec, frontend public Shopify app home, extension/theme COD runtime, and Shopify test. I did not inspect or modify those edits; findings are scoped to the committed code at the recorded SHA.
- **Evidence limitation:** implementation and automated tests do not verify deployed storage durability, real provider success outside test harnesses, physical shipment outcomes, carrier performance, or realized merchant economics.

## 2. Audit Coverage Map

| Surface | State | Coverage | Evidence |
|---|---|---|
| Merchant UI and routes | INSPECTED | list, prepare/create/edit/delete, quote, label, receipt proof, detail, replacement proof, support link | EV-ES-001 |
| Merchant APIs/authorization | INSPECTED | list/detail, create/edit/delete/submit, upload/view/replace proof, Store and Workspace scope | EV-ES-002–003 |
| Lifecycle and commercial snapshots | INSPECTED | draft/submission lock, receipt review, transit, timeout fallback, arrival verification, inventory-updated marker, cancellation/correction | EV-ES-004–006 |
| Warehouse app and operations | INSPECTED | queues, QR resolution, carton receipt, measurement evidence, absence/partial release, exception alerts, SLA, labels | EV-ES-007–008 |
| Provider pickup and reconciliation | INSPECTED | daily shared Accurate/Mayar General Pickup, uncertain-create recovery, provider polling, aggregate receiving allocation | EV-ES-009 |
| Finance | INSPECTED | merchant price, separate company cost, carrier payable cycles, payment evidence, later merchant charge/FX | EV-ES-010 |
| Schema, permissions, connected domains | INSPECTED | Store/Variant/expected quantity, Inventory boundary, Fees, Finance, Support, Notifications | EV-ES-011–012 |
| Specs/tests | PARTIALLY INSPECTED | External Shipping, recovery, scope, scheduler, PDF, Carrier Finance and permission tests; focused frontend spec runner unavailable | EV-ES-013 |
| Competitor equivalence and production operation | PARTIALLY INSPECTED | stable competitive baseline only; no direct competitor workflow check or production access | §16, §24 |
| Intent/code contradiction | INSPECTED | older V1 exclusions vs current executable workflows | EV-ES-014 |

## 3. Executive Section Truth

External Shipping is an implemented, multi-role inbound supply/logistics workflow: a merchant prepares AIR/SEA carton definitions for a scoped Store, previews a Fee-Profile-based merchant price, labels cartons, supplies Receipt Proof, and submits a locked shipment. Wossol operations review proof and manage transit; warehouse users physically receive and measure cartons; provider pickup evidence can be reconciled; Finance separately records carrier cost/payables and, on the required settlement gate, merchant-facing charges. It is substantially more than a shipment table or merchant form.

Its value is disciplined state and evidence across distinct commercial/physical events—not proof that product stock has been automatically added to the merchant’s inventory. Expected inbound quantities activate at submit but are not actual stock. `INVENTORY_UPDATED` can follow exact provider-evidence reconciliation or a separately authorized Operations completion path; this module does not itself mutate Provider Available inventory. The code must be described as inbound shipping and receipt coordination, not customer delivery tracking, general sourcing, supplier procurement, or automatic inventory truth.

The strongest feature cluster is immutable declared data + physical carton verification + explicit discrepancy evidence + delayed finance realization. Important limits remain: the ten-day transit fallback is time-based rather than a carrier event; General Pickup uses aggregate Variant evidence that can require deterministic allocation among shared shipments; production provider/storage reliability is not established; and current runtime exceeds exclusions in an older V1 specification.

## 4. Scope & Architecture Map

External Shipping owns merchant shipment preparation, carton/Variant contents, labels and versions, receipt-proof artifacts, declared merchant price, lifecycle, physical measurement/receiving records, receiving exceptions, and shipment timelines. Fees owns merchant-price profiles/rates and a separate Company Cost configuration; Finance owns carrier payable cycles and merchant ledger charges. Products owns Variants, Store provides scoped commercial identity, and Inventory/provider evidence owns stock truth. The `accurate_mayar` integration is used for a shared General Pickup lifecycle and provider-linked receiving evidence. Support can link a ticket to the shipment; Notifications surface proof requests/lifecycle events. This flow is distinct from last-mile customer delivery/shipping in Orders and Tracking, and from Local Pickup’s separately audited inbound request workflow.

## 5. Current Capability Inventory

| Capability | Status | Current behavior |
|---|---|---|
| Merchant preparation | LIVE | Workspace + active Store, assigned-Fee-Profile origin, AIR/SEA, carton, Variant quantities and required measurements. |
| Draft quote | LIVE, advisory until submit | AIR uses kg, SEA CBM; server derives the preview from assigned active Merchant Pricing rates. |
| Carton labels | LIVE | Current version generated before submission; A4 or 100×150 mm PDF; no commercial rate/price on labels. Draft edits can version labels; history retained. |
| Proof and submission | LIVE | Actor-bound private staged upload, then transaction rechecks scope/contents/rate, stores proof, freezes declared price, activates expected lines, and marks `submittedAt`. |
| Submitted shipment control | LIVE, locked | Merchant cannot edit or delete submitted contents; proof replacement only when Operations requests it while still CREATED. |
| Operations lifecycle | LIVE | Proof accept/request replacement, manual transit with required reference, ten-day automatic fallback, arrival review/recovery, Inventory Updated confirmation, cancellation and bounded corrections. |
| Warehouse receiving | LIVE | Separate Warehouse role/app supports scoped queues, QR identity, per-carton physical receipt, weight/dimension verification, private evidence, partial/missing-carton exceptions and release. |
| General Pickup integration | LIVE implementation; production NOT VERIFIED | Daily shared Accurate/Mayar pickup batches, durable retry/recovery and status polling; provider aggregate movement/manifest evidence is reconciled to shipment Variant expectations with explicit limitations. |
| Merchant price vs carrier cost | LIVE, separate | Merchant declared estimate/final price snapshots are distinct from Company Cost/carrier rate snapshots and payables. |
| Carrier settlement / merchant debit | LIVE implementation; real settlement NOT VERIFIED | Verified carton accrual → carrier cycle review/payment proof/PAID → Finance merchant charge using applicable FX/fee logic. |
| Inventory mutation | NOT OWNED HERE | This module preserves expected and received evidence; it does not create canonical Provider stock or claim a provider stock delta. |
| Supplier/procurement intelligence | NOT FOUND AFTER SEARCH | No supplier master, supplier choice, purchase order, supplier terms/payment, or sourcing recommendation established in this flow. |

## 6. Workflow & Lifecycle

1. Merchant selects a Store and prepares an unsubmitted shipment. AIR requires positive weight; SEA additionally requires positive centimeter dimensions. Cartons carry exact Store-eligible Variants and quantities. Draft labels and server price preview can be reviewed.
2. Merchant prints/attaches labels, sends cartons, stages a supported Receipt Proof file, and submits. The server moves the actor-bound upload into shipment scope and transactionally revalidates definition, scoped Store/Variants, fee assignment and price. It locks a declared billable amount/rate/currency and activates expected inbound rows.
3. Operations confirm proof (`CREATED → RECEIPT_CONFIRMED`) or request valid replacement while CREATED. Confirming receipt also snapshots eligible Company Cost for carrier payable accounting. Merchant replacement changes proof only; it does not unlock shipment contents or revise price.
4. Operations can mark the receipt-confirmed shipment IN_TRANSIT with a required confirmation reference. A background scheduler also advances it after ten days from receipt confirmation if it is still there. The automatic path records SYSTEM provenance but is a timeout fallback, not evidence of an observed carrier event.
5. Physical Warehouse users verify carton receipt and measurements. Differences require a merchant-safe reason and private evidence. Complete arrival review atomically records carton verification/correction/label version, computes the final merchant shipping price from the locked rate and verified billable amount, and transitions to ARRIVED. Partial arrival and missing-carton classifications/releases are separately represented.
6. Eligible verified cartons enter the daily shared General Pickup process. Provider state is polled and uncertainty is recovered through a durable batch rather than blindly creating another pickup. When provider completion exposes aggregate ProductTransaction/manifest evidence, exact Variant totals are allocated to member shipments in stable persisted order. Shortages/overages remain explicit and may block clean auto-completion.
7. Exact provider aggregate evidence can reconcile eligible receiving lines and transition qualifying shipments to `INVENTORY_UPDATED`; an authorized manual action can record a verification reference, or accept a persisted shortage with a reason. Neither path mutates Provider Available inventory or rewrites provider movements. Carrier financial payables are distinct: only appropriately snapshot-backed received cartons accrue; cycle review/payment proof and PAID state gate later Finance merchant charges. Cancellation and corrections are state/permission bounded and audited.

## 7. Value Recipient Map

| Recipient | Current value |
|---|---|
| Merchant / supply operator | Structured shipment prep, Store-specific Variant/carton expectations, labels, scoped receipt workflow and clear estimate/final price state. |
| Wossol Operations | Proof review, controlled lifecycle, discrepancy/cancellation authority and explicit evidence/notifications. |
| Warehouse receiving operator | Actionable Workspace queue, carton QR/labels, independent physical receipt/measurements and evidence-linked exceptions. |
| Carrier/Finance operator | Snapshot-backed carton costs, auditable carrier cycles, proof of payment and scoped settlement workflow. |
| Inventory/order operations | Expected inbound and provider receipt evidence can be reconciled without calling expectation actual stock. |
| Customer | No direct consumer delivery/status value established; this is upstream supply movement, not the customer shipment lifecycle. |

## 8. Control & Merchant Agency

The merchant controls preparation, Store, origin/method, declared carton contents and submitting after physical preparation/proof. After submit, they cannot alter the commercial/physical contract; they may only replace proof after an Operations request. Wossol staff control operational proof/lifecycle decisions, and Warehouse operators control recorded physical receipt/measurement. Finance controls settlement review/payment evidence/ledger effects through separate permissions. The merchant has meaningful Level 3 preparation control plus status/price transparency, but no arbitrary post-submit correction or carrier-routing control. This separation reduces silent changes at the cost of requiring staff escalation for legitimate post-submit changes.

## 9. Transparency & Trust

Strong evidence practices include authoritative `submittedAt` separate from lifecycle status; locked declared price/rate/profile provenance; preserved declared vs verified dimensions/weight; current and historical label versions; private evidence; actor, timestamp, reason, audit and merchant-safe timeline; separate internal alerts; deterministic transaction gates; and notification deep links for lifecycle/proof actions. The merchant sees estimate vs final price and receiving differences without staff identity, internal notes, provider secrets, or Finance internals.

Transparency is not equivalent to independent proof. Manual transit records a human reference; automatic transit records a ten-day timeout; neither necessarily proves a carrier scan. General Pickup movement evidence may be aggregate by Variant rather than carton-specific. Storage currently resolves under local `private-uploads`; durable shared storage across deployments/instances was not proven and is a deployment concern. UI status/timeline and test behavior do not establish physical or provider truth outside the stated evidence sources.

## 10. Merchant Value Extraction

The functional value is reducing ambiguity in the inbound shipment job: what Store/Variant/quantity is expected, which carton goes where, what proof was submitted, what rate was fixed, what physical measurements were verified, what is short/over, and what finance stages have (or have not) occurred. Labels and evidence reduce reliance on informal identifiers; immutable estimates plus observed measurements make price changes explainable; provider-backed aggregate reconciliation can connect completed pickup evidence to expected stock without pretending it identifies a physical carton where it does not.

No prior merchant workflow or reduction in freight cost, loss, delay, data-entry time, or disputes was measured. Benefits should be framed as structure and traceability, not proven efficiency or reliability outcomes.

## 11. Feature Clusters

1. **Prepare → submit → lock:** Store/Variant validation + carton measurements + labels + private receipt proof + server-side rate snapshot creates a stable auditable shipment contract.
2. **Declared → physically verified → priced:** immutable merchant declaration + evidence-backed Warehouse measurement + discrepancy reason/evidence + locked unit rate yields an explainable final price rather than overwriting the original estimate.
3. **Partial receiving → shared provider movement → reconciliation:** carton-level Wossol receipt + shared daily pickup + durable provider status/retry + aggregate Variant allocation + shortage/overage exceptions links physical operation to evidence without invented per-carton attribution.
4. **Operational cost → settlement → merchant ledger:** receipt-time carrier cost snapshot + verified physical quantity + carrier cycle + payment proof + Finance-owned FX/fee charge separates Company Cost from Merchant Pricing and prevents early/unverified charges.

## 12. Merchant Journey / Old Way vs Wossol Way

| Stage | Plausible manual alternative (not universal/verified) | Current Wossol evidence |
|---|---|---|
| Prepare supply shipment | Messages/sheets with item and carton lists | Scoped Store, Variant quantities, dimensions/weight, generated labels |
| Agree on shipping price | Recalculate or preserve informal quote | Server Fee Profile quote; submit-time immutable estimate/rate |
| Prove handover | Send receipt image in a separate channel | Actor-bound staged Receipt Proof and current replacement request flow |
| Receive and resolve differences | Manual carton/quantity check | Warehouse carton evidence, private measurement images, shortage/overage/absence exceptions |
| Reconcile provider movement | Compare a general manifest manually | Durable General Pickup evidence; deterministic shared-Variant allocation with known attribution caveat |
| Know actual cost | Mix merchant quote and carrier bill | Separate Merchant Pricing, Company Cost, final verified price, carrier payable and PAID-gated merchant debit |

The table describes implemented path vs plausible alternatives, not verified user history or measured savings. The system adds process/role coordination; it does not eliminate physical work, provider dependency, or exception handling.

## 13. Hidden / Non-Obvious Advantages

- `submittedAt`, not status label, is the authoritative preparation/submission boundary; a CREATED draft is not an operationally submitted shipment.
- Active expected items are activated only on submit; merchant declared quantity is not actual inventory.
- Merchant price and Company Cost have different owners/snapshots and different financial timing.
- Receipt confirmation freezes a carrier-cost rate snapshot; later fee changes cannot rewrite historical payable basis.
- Shared provider manifest quantities are allocated by durable membership order; this is a Wossol allocation policy, not provider proof of which carton physically arrived.
- The auto-transit fallback and human-confirmed transit share state gating, so only one evidence transition can win; however, timeout advancement should not be marketed as a carrier scan.
- Only received/eligible cartons enter pickup even if a sibling carton is absent; this prevents an absent carton from holding physically received cartons indefinitely.

## 14. Data & Intelligence Assets

Records include Store/Workspace/Merchant shipment identity; declared carton and Variant quantities; submission and rate snapshots; private proof and label versions; physical carton measurement/receipt and correction evidence; shipment lifecycle/audit/timeline; absence/receiving alerts; pickup batches/memberships/provider status; aggregate movement IDs/manifest references and allocated quantities; and carrier rate/payable/cycle/payment proof/Finance charge links.

This is potentially useful operational and cost evidence, but it is not a complete supplier/product landed-cost or inventory history. Missing/limited evidence includes supplier identity/PO/invoice, carrier service-level and route performance across comparable shipments, reliable per-carton provider attribution when manifests are aggregate, and proof that Inventory consumption joins every receiving fact into current stock. Data presence does not establish predictive delay, sourcing, or profitability intelligence.

## 15. Cross-Section Compound Advantages

- **Products / Stores:** Store-scoped active Variants and identity define declared inbound contents; Store does not itself own physical inventory.
- **Inventory:** Expected inbound is an operational signal; provider-backed actual availability remains separate. This module's `INVENTORY_UPDATED` is not a direct stock mutation.
- **Fees:** merchant shipping prices and company carrier cost are separate configurations and snapshots.
- **Finance:** handles carrier cycles/payment evidence and delayed merchant charges/FX; shipment detail's final price is not itself a posted ledger event.
- **Local Pickup / Sourcing:** both concern inbound movement and provider evidence, but the current flows have different models/processes. External Shipping is not supplier discovery/procurement; do not collapse workflows without evidence.
- **Orders / Tracking / Delivery:** downstream customer shipment and COD outcomes are not this upstream origin-country shipment. Do not call this multi-carrier last-mile delivery or order tracking.
- **Support / Notifications:** shipment-linked support and proof/lifecycle notifications can route attention, but do not by themselves establish SLA resolution quality.

## 16. Competitive Analysis

International/inbound stock movement and physical logistics are category infrastructure in the competitive master’s market-entry layer. COD Network and other integrated providers are described as having sourcing, warehousing, fulfillment, tracking, and payout breadth; that high-level reference does not establish equivalent carrier pricing snapshots, carton evidence, shared pickup recovery, or Finance controls. No capability-by-capability competitor comparison was verified for this audit, so do not claim Wossol superiority or uniqueness. Current implementation depth is meaningful, but network reach, active lanes/countries, provider performance, fulfillment coverage, and real merchant usage are unverified.

## 17. Marketing Intelligence

**Current truthful territory:** prepare and track an inbound shipment from declared Store/Variant cartons through proof, operational receipt and measurement review, with estimates and verified shipping values kept distinct. Qualify availability and workflow roles; clarify that actual inventory, supplier sourcing, customer last-mile delivery and production reliability are separate.

**Potential territory:** evidence-linked inbound supply operations and landed-cost accountability. Not yet eligible as a broad claim because supplier, customs, full landed cost, provider performance and downstream inventory truth are not established end-to-end.

## 18. Surprise Findings

1. Scope is materially wider than “merchant creates a shipment”: distinct Merchant, Admin Operations, Warehouse and Finance/Carrier Finance workflows are implemented.
2. A successful arrival can depend on all carton measurements and private evidence; the merchant-facing final price is not just an editable quote.
3. Provider General Pickup batches can include shared Variants across shipments; reconciliation is deterministic but system-allocated, not physically carton-attributed provider evidence.
4. The older system design explicitly excludes provider APIs, QR/scanning and Finance posting even though current source implements bounded General Pickup, QR carton identity and carrier Finance paths.
5. Transit may be advanced by a 10-day timer even without a carrier status observation; this is operational fallback, not transport proof.

## 19. Potential Category Reframes

“Evidence-linked inbound supply operations” is a plausible future framing, not a validated category. It becomes stronger only if supply origin/supplier, reliable transit/receiving, current stock, actual costs and outcome analysis form a complete merchant-operable chain. Avoid “sourcing network,” “global fulfillment,” or “end-to-end supply chain” on this section alone.

## 20. Brand Evidence

Current source supports careful, process-oriented, accountable, economically explicit operations: declared values remain distinguishable from observed values; sensitive evidence is permission-gated; correction reasons and histories persist; Merchant Pricing is not conflated with Company Cost. The breadth also reveals operational complexity and provider dependence. No evidence establishes effortless sourcing, guaranteed delivery, frictionless automation, or intelligent procurement. This evidence can inform but not determine brand identity.

## 21. Weaknesses / Risks / Gaps

1. **Timeout transit ambiguity:** auto-advancing after 10 days can make state read more certain than physical/provider evidence supports; UI claim copy and downstream eligibility should distinguish timeout from scan/confirmation.
2. **Shared aggregate evidence:** Variant-level manifest quantities do not identify a carton/shipment; deterministic allocation policy may differ from physical truth and overage blocks clean completion.
3. **Inventory boundary:** shipment status/receiving lines do not establish that provider available stock was updated correctly; current manual `INVENTORY_UPDATED` action remains an attestation/reference.
4. **Private file durability:** storage uses local `private-uploads`; shared/persistent deployment storage across instances, backups, retention and recovery were not verified.
5. **Provider dependency/operability:** Mayar API availability, webhook vs polling cadence, rate limits, real recovery behavior, active carrier service/country support and measured SLA are unverified.
6. **No direct carrier tracking for international shipment:** provider batch status covers General Pickup operation, not continuous linehaul location or ETA.
7. **Pricing completeness:** merchant charge at submit/final arrival and company carrier payable use separate rates; taxes, customs, insurance and full landed cost are not shown as integrated total.
8. **Physical exception resolution:** missing-carton/partial-release flow needs timely authorized operators and evidence; no measured dispute-resolution outcome.
9. **Cross-domain naming:** “External Shipping” can be mistaken for customer last-mile shipping or generic carrier integration; keep inbound context clear.
10. **Architecture drift:** older V1 architecture’s exclusions no longer describe current implementation and need authoritative version/status reconciliation.

## 22. Future Strategic Potential

An evidence-complete supply operation could join supplier/order provenance, declared freight, customs/fees, verified receipt, current Inventory, Store demand and real selling/delivery outcomes into reliable landed-cost and replenishment decisions. The audited current foundation supports only some shipment/measurement/cost pieces. Supplier comparison, procurement recommendations, guaranteed logistics, inventory automation, lane benchmarking, and predictive lead time are **IDEA / OPPORTUNITY**, not current or approved capabilities.

## 23. Claim Safety

| Safe / qualified | Unsupported / unsafe |
|---|---|
| Merchant can prepare a Store-scoped AIR/SEA carton shipment, submit receipt proof and follow its Wossol operational lifecycle. | “International shipping to any country,” guaranteed arrival, or proven speed/reliability. |
| Wossol Warehouse records physical carton receipt and verifies measurements; discrepancies preserve reason/evidence. | “Inventory automatically updates from every shipment” or “provider-confirmed per-carton receipt” where aggregate allocation is used. |
| Draft/declared and verified shipping prices are kept distinct; carrier cost is separately snapshotted. | “True landed cost/profit” including unverified customs, supplier, tax, or all downstream costs. |
| Current code includes bounded Accurate/Mayar General Pickup and carrier Finance workflows. | “Global carrier network,” continuous tracking, complete sourcing/procurement, competitor superiority, or measured merchant savings. |
| Ten-day fallback can move a receipt-confirmed state to In Transit. | Describe fallback as a carrier-confirmed scan or proof of physical departure. |

## 24. Commercial Magnitude

Potential magnitude is high for merchants who use this inbound shipment/warehouse service: it touches stock access, physical loss/variance and shipping cost. Realized magnitude is **UNCERTAIN** because live markets, volumes, merchant usage, delivery times, loss rates, cost accuracy, and effect on sellable stock were not provided. The integration/operational breadth itself is not proof of adoption or positive unit economics.

## 25. Strategic Classification

- **Product status:** LIVE multi-role operational workflow; provider/deployment outcomes NOT VERIFIED.
- **Category role:** TABLE STAKES / infrastructure in an integrated cross-border COD market.
- **Wossol strength:** POTENTIAL DIFFERENTIATOR in evidence/snapshot separation and cross-domain finance controls; not externally validated.
- **Control depth:** Merchant Level 3 before submit; role-separated operational/warehouse/finance control after submit.
- **Data moat:** potential foundation only; current evidence lacks completeness, scale, and learning use.
- **Copyability:** forms, labels, and state machine are copyable; accumulated auditable physical/financial evidence plus reliable reconciliation would be harder, but that moat is not established.

## 26. Action Register

| Priority | Action | Owner/domain | Reason |
|---|---|---|---|
| P1 | Reconcile/version older External Shipping V1 exclusions against current Admin, Warehouse, General Pickup, QR and Carrier Finance implementation. | Product architecture | P3 intent and current P1 source materially conflict. |
| P1 | Keep ten-day auto-transit presentation and downstream rules explicitly identified as timeout fallback, not observed carrier transit. | External Shipping + UX | Prevent status certainty exceeding evidence. |
| P1 | Define/verify the post-`INVENTORY_UPDATED` stock ownership/mutation contract across External Shipping, Local Pickup, Inventory and provider data. | Inventory + External Shipping | Status confirmation is not stock mutation. |
| P1 | Verify private artifact persistence/backup/recovery in production and move to supported shared private storage if multi-instance deployment requires it. | Platform operations | Current code uses local private-filesystem paths. |
| P2 | Add production acceptance and reconciliation monitoring for General Pickup create uncertainty, active-batch blocking, provider polling, aggregate allocation and shortages/overages. | Integrations + Operations | Unit tests cannot establish provider/runtime operation. |
| P2 | Report shipment estimate-to-final variance, end-to-end time, missing/short cartons and costs only with defined denominators/provenance. | Finance + Analytics | Could convert trace evidence into useful metrics without conflating them. |
| P2 | Clarify merchant-visible scope: inbound shipment to operational receiving, not customer delivery tracking/supplier sourcing. | Product + Marketing | Avoid category ambiguity. |

## 27. Evidence Register

| ID | Evidence / source | Supports | Strength / limitation |
|---|---|---|---|
| EV-ES-001 | `apps/frontend/src/app/merchant/external-shipping-ui.tsx`, `external-shipping-data.ts`, routes `/merchant/external-shipping/**` | Merchant list, prepare/edit, labels, proof submit, detail, receiving, support link | P1 UI/client |
| EV-ES-002 | `apps/backend/src/modules/external-shipping/external-shipping.controller.ts` merchant routes | Protected list/create/quote/detail/edit/delete/submit/proof APIs | P1 route; service auth in EV-ES-003 |
| EV-ES-003 | `external-shipping.service.ts`: `assertMerchant`, `assertMerchantStoreScope`, create/list/edit/delete/submit; permission catalog | Merchant/Workspace/Store/Variant scope and state/permission gates | P1 |
| EV-ES-004 | `external-shipping.service.ts`: `createDraft`, `quote`, `submitMerchantShipment`, `editMerchantShipment`, `deleteMerchantDraft` | Draft and server-revalidated immutable submission/rate/expected-item activation | P1 |
| EV-ES-005 | `confirmReceipt`, `markInTransit`, `autoTransit`, `cancel`, `correctCartonItemQuantity` in `external-shipping.service.ts` | Lifecycle and timeout/manual provenance, cancellation/corrections | P1 |
| EV-ES-006 | `markArrived`, `verifyCartonMeasurements`, `completeWarehouseCarton`, shipment/carton models in `schema.prisma` | Per-carton evidence/measurement gates and final price before arrival state | P1 |
| EV-ES-007 | `external-shipping.controller.ts` warehouse routes; `apps/frontend/src/app/warehouse/page.tsx`; `apps/frontend/src/app/admin/external-shipping/**` | Separate Warehouse and Admin operational applications | P1 |
| EV-ES-008 | `classifyWarehouseCartonAbsence`, `releaseReceivedCartons`, warehouse queue/SLA/QR methods in service | Partial physical receipt, exceptions, role-gated recovery | P1 |
| EV-ES-009 | `syncGeneralPickups`, `createDailyPickupBatch`, `recoverUncertainPickupBatches`, `eligiblePickupShipments`, `reconcileDailyPickupBatchReceiving` | Shared provider batch/recovery/status/reconciliation | P1 implementation; real provider behavior not validated |
| EV-ES-010 | `carrier-finance.service.ts`, `finance.service.ts` (`postExternalShippingChargesForPaidCarrierCycleWithClient`), Admin Fees and Warehouse Finance UI | Separate carrier payable and later merchant debit gates | P1 |
| EV-ES-011 | `apps/backend/prisma/schema.prisma` models `ExternalShipment`, expected/receiving items, proof/evidence, pickup batch/memberships, carrier payable/cycle | Persisted scope, snapshots, evidence, links and lifecycle | P1 |
| EV-ES-012 | `docs/wossol-system-design/03-operational-systems/external-shipping-system.md`; Merchant/Admin/Warehouse UI specs; Notifications, Support, Inventory and Finance sources | Aligned intent and cross-domain boundaries (with caveats below) | P2/P3, not proof where conflicting with P1 |
| EV-ES-013 | P2: executed backend focused specs (107 pass); backend and frontend typechecks pass | Spec coverage for service/recovery/scope/scheduler/PDF/finance/permissions | Frontend receiving spec could not be run through the available TypeScript/ESM runner |
| EV-ES-014 | `external-shipping-system.md` V1 “Security, evidence, and non-scope” exclusions vs current P1 source | Some exclusions (Admin/Warehouse UI, QR, General Pickup API, Finance posting) are contradicted; other exclusions remain unverified/not found | P1 prevails for current truth; approval/rollout history unresolved |

## 28. Contradictions & Uncertainty

**CONTRADICTION ID: ES-CONTRA-001 — V1 scope exclusions vs current implementation**

- **Source A:** `docs/wossol-system-design/03-operational-systems/external-shipping-system.md` currently describes later General Pickup/Finance behavior, but its residual “V1 excludes” paragraph still lists no Admin Operations UI, carrier APIs/tracking, QR/scanning, finance posting/refunds, and price-correction workflow.
- **Source B:** current P1 source has Admin and Warehouse applications/routes, QR carton identities, Accurate/Mayar General Pickup API creation/status/recovery, carrier Finance payable/cycle/payment proof, and Finance merchant charge posting. Privileged carton quantity/measurement corrections exist, but do not establish a price-correction workflow.
- **Nature:** some older V1 exclusions are stale relative to current implementation; others remain accurate or only partly superseded. The code may reflect later approved phases not recorded as a versioned contract.
- **Evidence strength:** P1 is authoritative for current executable product truth; P3 establishes intended earlier boundary only.
- **Working conclusion:** report only the specific code-backed paths. Do not imply continuous international shipment tracking, road shipping, air volumetric pricing, refunds, or price correction; do not claim the old spec was formally superseded or workflows are live in production.
- **Remaining uncertainty / required verification:** product architecture owner should version the current system contract and indicate rollout/configuration/approval state.

Additional uncertainty: production shared file storage; active merchant countries/fee profiles and actual carrier lanes; provider General Pickup guarantees and manifest granularity; exact Inventory update responsibility; transit fallback appropriateness; settlement/FX acceptance; measured service/outcomes; detailed competitor parity.

## 29. Open Questions

1. Which document/version now authoritatively supersedes the V1 exclusions, and which of the newer flows are enabled for merchants/operations in production?
2. What evidence, if any, besides elapsed time proves transit in the fallback path, and should it alter Warehouse/provider-pickup eligibility?
3. What exact event/action makes stock “Inventory Updated,” and which system is authoritative for resulting sellable availability?
4. How are local private artifacts persisted/backed up and served across production instances?
5. What correction and settlement path applies to a final merchant price or carrier charge after physical receipt/payment?
6. Which country lanes/carrier rates, active merchants, shipment volumes and measured operating outcomes can be independently verified?

## 30. Methodology Learnings

No methodology change required. The audit applied current P1 precedence over a historical P3 “not in V1 scope” list while preserving the unresolved version/rollout question. It also keeps physical observation, aggregate provider evidence, timeout inference, merchant price, carrier cost and inventory truth separate.

## 31. Retroactive Review Impact

No methodology-driven retroactive re-audit is required. The new Director route-coverage review at `04-review-history/MERCHANT_SURFACE_COVERAGE_REVIEW_2026-09-26.md` explicitly requested this dedicated External Shipping audit before synthesis. Sourcing/Network, Inventory, Tracking/Delivery, Finance, Orders, Stores and Integrations were treated as dependency boundaries; this audit does not revise them. Local Pickup remains a distinct follow-on coverage item.

## 32. Canonical Section Takeaway

External Shipping is a real multi-role inbound shipment, warehouse-receiving, provider-pickup and carrier-finance workflow—not customer last-mile tracking or sourcing/procurement. Its defensible strength is controlled, evidence-linked progression from Store-scoped declared cartons and estimated merchant price to physical verification, provider-linked receiving evidence, final price and separately gated Finance effects. It does not itself make expected quantities actual inventory, and its ten-day transit fallback is not a carrier observation. P1 implementation materially exceeds older V1 scope exclusions; current architecture/version and production enablement need reconciliation before broader claims.
