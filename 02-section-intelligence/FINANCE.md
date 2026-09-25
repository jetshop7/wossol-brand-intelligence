# Finance — Section Intelligence

## 1. Audit Metadata

- **Audit date:** 2026-09-25.
- **Methodology:** `00-methodology/MASTER_INSTRUCTIONS.md` v1.1; `CODEX_OPERATING_PROTOCOL.md` v1.0.
- **Competitive reference:** `01-competitive/WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md` v1.0 (2026-09-09); not freshly verified against competitors.
- **Intelligence source:** `jetshop7/wossol-brand-intelligence`, branch `main`, commit `d66f55052a531a0e9410ccc89c557eb97f19f4d2`, clean and synchronized before audit.
- **Product source:** `jetshop7/wossol-platform`, local workspace `C:\Users\Global Tech\Documents\wossol-platform`, branch `dev/wossol-integration`, HEAD/upstream `76cb3db4116e52df2920d90b863d862ec772429c` (`annalytic`, 2026-09-25). At final verification the worktree contained unrelated uncommitted edits in Shopify app/upsell/messaging/order-capture files, including `apps/frontend/src/app/shopify/app/route.ts`, `apps/frontend/public/shopify-app-home.js`, `apps/backend/src/modules/messaging/messaging-order-capture.service.ts`, `apps/backend/src/modules/orders/orders.service.ts`, Shopify test/spec files and untracked `apps/backend/src/modules/messaging/messenger-business-suite.ts`; these were preserved and not modified. No Finance-path differences from committed HEAD were found.
- **Scope:** Merchant financial ledger/cycles/settlements/withdrawals/payout methods, Admin merchant-finance operations and controls, Finance collection/fee/adjustment functions, Fee Profile dependencies, and External Shipping Carrier Finance payables. This is not an audit of company-wide accounting, taxes, banking infrastructure, or an accounting-system reconciliation.
- **Evidence boundary:** current source/schema and local focused tests/typechecks; no production database, real payout/settlement, bank/provider account, or runtime financial reconciliation was accessed.

## 2. Audit Coverage Map

| Surface | Coverage | Evidence |
|---|---|---|
| Merchant Finance UI and exports | overview, capital lifecycle, current/frozen statements, transaction history, payout requests/methods/evidence, support handoffs, spreadsheet export | EV-FIN-001, 004 |
| Merchant Finance API/access | active Workspace and merchant identity, permissions, store scoping, reads and writes | EV-FIN-002 |
| Financial ledger and cycles | ledger, collection, eligibility, immutable fee assessments, manual adjustments, debt coverage, snapshots | EV-FIN-003–006 |
| Merchant withdrawal and Admin execution | holds, batches, cycle freeze, FX snapshots, retained payment proof, paid debit, merchant receipt acknowledgement | EV-FIN-007–009 |
| Admin Finance screens | collections, withdrawal queue, merchant statement, batch workspace, External Shipping payables | EV-FIN-010 |
| Fee Profile/pricing dependencies | operational/platform-service fees, merchant FX spread and carrier cost schedule | EV-FIN-011 |
| External Shipping Carrier Finance | carton accrual, separate carrier cycles, payment evidence, paid transition, merchant-charge posting | EV-FIN-012 |
| Local Pickup handoff | goods debit at Inventory completion, separate from delivery/operational fee | EV-FIN-013 |
| Schema/history/specification | finance models/enums/migrations, approved Finance and Admin-Fee intent | EV-FIN-014–015 |
| Verification | 92 backend tests pass; both typechecks pass; Finance-related UI source specs have two stale assertions; one direct Node runner failed to resolve an extensionless import | EV-FIN-016 |
| Production operations | provider/bank authority, live permissions, reconciliation, deployment/storage/security configuration, real transaction outcomes | NOT VERIFIED |
| Company books / accounting export / tax | no general ledger, tax filing or independent accounting reconciliation established in audited Finance scope | NOT ESTABLISHED / NOT VERIFIED |

## 3. Executive Section Truth

Finance is a substantial, auditable merchant-balance and payout-operations subsystem, not simply a balance widget. It records Finance-confirmed collection evidence for eligible delivered Orders, credits an idempotent ledger entry, applies configured debits/assessments, computes current-cycle position, supports Workspace financial cycles and frozen statements, holds withdrawal requests, and provides Admin-controlled payout execution with retained proof. A separate Carrier Finance workflow recognizes external-shipping company payables and, when paid, posts an FX-snapshotted Merchant charge.

Its strongest present value is traceable money movement and separated authority: Orders/Tracking establish operational outcomes; Finance authoritatively recognizes collections, ledger effects, settlement eligibility, merchant balances and payout facts; Admin permissions gate high-risk actions; Merchants can inspect scoped summaries, statements, evidence and payout status. The current workflow remains human-operated: collection confirmation, provider settlement request/quote/receipt and actual merchant transfers are not established as end-to-end automated integrations.

Important boundaries remain: the broad P3 “financial brain” language exceeds the concrete debt model found in current P1; P3 says requested withdrawals cannot be edited while P1 allows edits while still REQUESTED in the open batch; and Carrier Finance currently exposes an explicitly QA/non-production wallet identifier. Local tests/typechecks do not establish live balances, correct remittance, completed bank transfers, solvency, accounting completeness, or profitability.

## 4. Scope & Architecture Map

```text
Orders + Tracking + Inventory + External Shipping
  → eligible order / verified shipment / carton facts
  → Finance fee assessment + collection confirmation + merchant ledger
  → current Workspace cycle and debt/availability projections
  → merchant withdrawal request
  → Workspace Settlement Batch + locked snapshot + provider quote/FX
  → human payout with required private evidence
  → ledger debit + merchant receipt acknowledgement

Verified external-shipping carton
  → separate Carrier payable cycle (Company Cost snapshot)
  → carrier payment proof / PAID operation
  → merchant-priced charge + provider FX + Merchant Fee Profile spread
  → Merchant Finance ledger debit
```

Finance owns its ledger, collection record, cycles, settlement snapshots, balances, payout requests, manual adjustments and Finance audit/timeline facts. Orders owns the canonical Order; Tracking owns provider delivery observations; Inventory owns verified carton completion; Admin Fees owns commercial pricing configuration. Merchant Finance is a user interface/operational department over the Financial System, not company books. Carrier payable cycles are not Merchant settlements, though Carrier Finance PAID can create a separate Merchant charge.

Wallet, cycle and full settlement are Workspace-level for a Merchant; Store is a classification/filter and may scope a partial withdrawal or adjustment, not a second wallet/full settlement. Admin operations are explicitly Workspace-scoped and permission-checked; Merchant access resolves a single active merchant/workspace context.

## 5. Current Capability Inventory

| Capability | Status | Current behavior |
|---|---|---|
| Merchant balance, capital lifecycle and current-cycle detail | LIVE | ledger-derived current-cycle summary, separate in-motion categories, Store-filtered detail while availability/wallet remain Workspace-wide |
| Collection recognition | LIVE, human-confirmed | Admin can confirm once for a delivered Order/current provider Shipment after amount/currency match; this is Finance evidence, not automatic provider-receipt verification |
| Operational and platform-service fee assessments | LIVE | enabled Fee Profile rule snapshots, source-event idempotency, ledger debit for positive amounts; active zero fees may be recorded as assessment without zero-value ledger entry |
| Debt/locked-for-debt projection | PARTIAL / CONTRACT UNCERTAIN | net from current settlement-eligible/unincluded ledger facts (including eligible carry-forward facts) and related collection-time coverage evidence; no general lending/credit facility or broad outstanding-obligation ledger was found in Finance P1 |
| Manual adjustments | LIVE | permissioned credit/debit with internal reason, merchant note, idempotency, ledger/timeline/audit; correction is additive via correction link |
| Withdrawal requests and holds | LIVE | partial/full request, eligibility guards, active-request hold and payout destination; cancellation/edit is limited to REQUESTED/open Current Settlement Batch |
| Merchant Settlement Batch and FX | LIVE, manually operated | automatic enrollment, request/freeze, immutable payout destination/principal snapshots, provider quote and per-Merchant Fee Profile FX spread/rate snapshots |
| Payout execution and payment evidence | LIVE in code; real transfer NOT VERIFIED | manually executed payout with required persisted private proof before Finance can mark paid; paid debit is created idempotently; Merchant can acknowledge receipt; current creation UI supports structured crypto destinations, not a general bank payout rail |
| External Shipping Carrier Finance | LIVE in code; production destination NOT VERIFIED | verified carton cost snapshots, separate carrier payable cycles, proof-gated PAID, then immutable Merchant charge posting if valid FX/profile inputs exist |
| Local Pickup goods purchase | LIVE | distinct Finance debit triggered by Inventory completion, separate from Local Pickup service/delivery fee |
| FX | PARTIAL, operation-specific | indicative Provider FX + explicit Merchant Fee Profile spread produce frozen USDT payout amounts and External Shipping LYD charges; this is not a general multi-currency ledger |
| Accounting/automated remittance/provider transfer | NOT ESTABLISHED | no general ledger export/close, bank reconciliation, automatic provider collection feed or automatic payout execution established |

## 6. Workflow & Lifecycle

1. Operational domain outcomes and Finance-owned pricing rules create assessment and ledger evidence in transaction context. Finance snapshots configured fee amounts/currency and uses source keys to prevent duplicate postings.
2. A Finance-authorized Admin confirms collection only when the Order is `DELIVERED`, the specified Shipment is the current Shipment and has a provider shipment ID, and amount/currency exactly match the Order. The unique Workspace/Order collection and ledger credit are written transactionally; the default source is `ADMIN_MANUAL`. `PROVIDER_REMITTANCE` is a source value, not proof of an API/bank integration.
3. Finance uses eligible ledger facts and final Order outcomes for settlement statements. Unfinished Orders are excluded from settlement candidates; delivered Orders require a Financial Collection. Assessments and ledger facts are reconciled into per-Order fee/detail rows.
4. Merchant may request a partial withdrawal or Workspace full settlement using an active structured payout method. Active partials prevent duplicate/concurrent full requests. Pending requests reduce available-to-request; the withdrawal debit itself is not posted until paid.
5. New withdrawals enter the automatic Workspace Current Settlement Batch. An authorized Admin's provider-settlement request freezes candidate requests, payout destinations, principals and full-settlement cycle snapshots; a successor Current Batch opens. Provider quote/FX is recorded manually. Direct per-withdrawal `startWithdrawalReview` is explicitly retired and returns conflict.
6. Finance stages private proof and marks each withdrawal paid only after frozen Batch FX and payout facts exist, full-settlement snapshot checks pass where applicable, and at least one evidence upload is present. The atomic paid transition creates one debit and records actor/time. Merchant receipt confirmation is a separate later state.
7. Admin may create an additive manual credit/debit only with permission, explicit internal reason and merchant-visible note. The entry, timeline and audit event are created together; correction references another adjustment rather than overwriting it.
8. Warehouse carton verification accrues Company Carrier Cost from frozen verified measurements/rate snapshots. Carrier Finance separately freezes a payable cycle and retains proof. Its PAID transition calls Merchant Finance in the same transaction to calculate/post distinct Merchant charges from final Merchant price and FX/spread evidence.

## 7. Value Recipient Map

| Recipient | Current value |
|---|---|
| Merchant owner/operator | scoped balance, current/frozen statements, order/fee breakdown, withdrawal status, payout evidence, export and support context |
| Finance operator | Workspace queue/search, settlement batches, frozen destination/FX, payment-proof review, manual adjustments and auditable payout controls |
| Fees/Finance administrator | manages fee profiles, operational prices, carrier costs and FX spread through controlled configuration surfaces |
| Wossol finance operations | separated Merchant obligations vs company carrier payable evidence and traceable manual payout steps |
| Orders/Tracking/Inventory | downstream Finance recognition of collection, configured fees, return/recovery outcomes and verified goods/shipment costs |
| Customer | no direct Finance surface established; customer payment collection through COD is not a customer financial account in this module |

## 8. Control & Merchant Agency

Merchant control is strongest over payout destination setup/maintenance and request initiation, partial amount/store scope, eligible cancellation or request edits while still in the open batch, and read/acknowledgement of payouts. Merchant does not directly alter ledger entries, collection confirmation, fee rules, settlement FX, payout execution, debt calculations or manual adjustments.

Admin control is partitioned among explicit permissions for collection confirmation, adjustment creation, withdrawal read/review/pay/evidence, batch creation/lock/quote/receipt/incident/prefund, FX policy and carrier settlement actions. Server-side checks exist; hiding a button is not the security boundary. Finance therefore provides operational control (Level 3) to authorized Admin actors and bounded request/configuration/read control to Merchants, not intelligent control (Level 5).

## 9. Transparency & Trust

- Ledger records retain Workspace/Merchant/Store/Order/Shipment relationships where relevant, direction/type, amount/currency, source domain/entity/event key, fee snapshots and merchant-facing description.
- Collection, payout, fee, adjustment, debt-coverage, and settlement-cycle state has separate records/timeline/audit facts rather than being inferred only from one displayed balance.
- Full settlement snapshots preserve represented ledger entries, eligible Orders, amounts, detail data and settlement-eligibility version. Fee and FX snapshots prevent later price/profile edits from silently recalculating historical rows.
- Withdrawal proof is private, permissioned, actor-staged then bound to Workspace/Merchant/Withdrawal at payment. Carrier provider-receipt proof is separately stored and is not Merchant proof.
- Payout destinations are masked in ordinary Merchant Finance projections; Admin execution accesses a frozen destination after Batch FX finalization. Merchant can retrieve its own payout proof.
- Current Merchant summary reports non-additive “capital in motion” categories; UI and API explicitly warn not to sum collected, pending delivery, pending collection, withdrawable and held values as one total.
- These controls establish source-level traceability, not independent evidence that cash moved or that all company/merchant books reconcile to external statements.

## 10. Merchant Value Extraction

Finance consolidates earned/collected value, charges, manual corrections, holds, settlement snapshots and payout progress into a Merchant-facing view. An Order-level fee breakdown can explain why a delivered Order's gross collection does not equal withdrawable balance. Store filters allow contribution analysis without falsely creating Store-owned wallets or settlements. Frozen statements and exports are practical dispute/review tools.

This product evidence supports clearer financial operations, not a claim of faster payment, lower fees, correct external settlement, accurate profit, better cash-flow outcomes or guaranteed payout timing. Actual success depends on verified remittance, fee policy, payout handling and reconciliation processes outside the inspected source boundary.

## 11. Feature Clusters

1. **Outcome → collection → merchant balance:** Orders/Tracking delivery truth + explicit Finance collection confirmation + idempotent earning + cycle ledger. This avoids treating a delivery status alone as money received.
2. **Fee provenance → understandable settlement:** Fee Profile trigger/rule + immutable assessment + linked ledger debit + Order-scoped frozen statement/export.
3. **Safe withdrawal execution:** request hold + Workspace batch freeze + destination snapshot + provider FX/merchant spread + required private payment proof + paid debit + merchant receipt acknowledgement.
4. **Two-sided external shipping economics:** verified carton and Company Cost → carrier payable proof/PAID → separate Merchant-price/FX-based charge. Inputs and ledgers stay distinguishable; this alone is not margin/profit accounting.
5. **Human correction with history:** permissioned adjustment reason + merchant note + actor audit + additive correction link, maintaining a challengeable trail rather than editing a balance.

## 12. Merchant Journey / Old Way vs Wossol Way

| Stage | Plausible alternative (inference) | Current Wossol behavior |
|---|---|---|
| Recognize COD | rely on a status, message or spreadsheet | Finance user confirms delivered/current- Shipment collection and exact Order total |
| Understand balance | separately total COD, fees, manual notes and prior transfers | Merchant sees cycle summary, transaction activity, scoped statement and export |
| Request withdrawal | message Finance with amount/destination | submit an idempotent partial/full request with structured payout method |
| Execute | manually coordinate amount, FX, destination and screenshot in separate channels | Finance batch freezes principal/destination and FX; proof required before paid debit |
| Reconcile | compare response manually | Merchant can inspect proof/status and separately confirm receipt; no external statement reconciliation found |
| Correct | informal note or direct balance change | additive credit/debit adjustment with internal reason, merchant-visible note and audit |

These alternatives are reasonable workflow inferences, not measured merchant research. Admin-side provider settlement still includes manually prepared request/quote and human payment steps.

## 13. Hidden / Non-Obvious Advantages

- Finance only recognizes collection against the delivered Order's current provider Shipment and exact Order amount/currency; an old/recreated Shipment cannot confirm the active Order's collection.
- Serializable transactions, advisory locks, unique source keys, idempotency keys and explicit duplicate handling protect high-risk collection/withdrawal/adjustment workflows against repeats and races.
- Settlement Batch membership is automatically assigned; arbitrary manual membership is rejected. Locking creates a successor batch and freezes member destination/principal facts.
- Partial withdrawals reserve availability without writing a paid-debit fact; full settlements lock the entire cycle and create a snapshot.
- Merchant FX rate snapshots derive from Provider quote and the assigned active Fee Profile spread, with eight-decimal payout rounding; later indicative FX updates do not mutate a finalized batch.
- Carrier Company Cost is versioned/scheduled separately from Merchant commercial pricing; verified carton dimensions/weight and rate snapshots preserve their own company payable calculation.
- Zero-valued enabled fee assessments remain visible as evidence without creating a meaningless zero ledger debit.

These are implementation safeguards. They do not prove external provider accuracy, financial audit opinion, or deployment integrity.

## 14. Data & Intelligence Assets

Finance captures ledger direction/type/amount/currency, source and event identity, merchant/store/order/shipment links, fee and FX snapshots, collection actor/reason/reference, payout method and destination snapshots, withdrawal states/times/actors, payment evidence metadata, settlement batch/member incidents, cycle eligibility and statements, adjustments and coverage evidence, carrier measurements/rate versions, and timeline/audit records.

This data can support later cash-flow, service-fee, payout-latency, debt-coverage, carrier-cost and delivered-margin analyses if reconciled to actual cash and scoped consistently. Current P1 does not establish independent bank/provider statements, broad financing obligations, mature accounting close, chart-of-accounts, taxes, transaction matching, actual company gross margin, order acquisition spend, advertising attribution or a recommendation/learning loop. A ledger snapshot is financial evidence, not automatically audited financial truth.

## 15. Cross-Section Compound Advantages

- **Orders × Tracking × Finance:** delivery and current Shipment evidence define collection eligibility; Finance's explicit collection then separates operational delivery from financial recognition.
- **Finance × Fees × Orders/Confirmation/Tracking:** trigger-scoped fee assessment uses configured price snapshots and attaches costs to canonical Order/Shipment events.
- **Finance × Inventory × External Shipping:** verified carton facts support Company Carrier Cost accrual; carrier cycle payment triggers distinct Merchant commercial debit only after required FX/profile data validates.
- **Finance × Customers:** refund/return/reputation implications may correlate with financial outcomes, but the inspected Finance system does not establish cross-customer prediction or automatically settle customer refunds.
- **Finance × Advertising/Analytics:** the Finance ledger is a necessary possible downstream economic source, but no ad spend/attribution-to-delivered-profit graph or learning decision system was established here.

## 16. Competitive Analysis

The 2026-09-09 Competitive Intelligence Master describes basic COD collection and payout/settlement execution as category operating infrastructure. It cites Fufills as a strong operational benchmark with public settlement/reconciliation claims and Delivered with weekly payout positioning; these remain dated competitor-master evidence, not fresh product/account verification. The master itself flags uneven settlement-depth verification across competitors.

Wossol's defensible candidate depth is the combination of order-linked, fee-snapshotted records; Workspace/Store semantics; held requests vs paid debits; frozen payout/FX evidence; and auditable manual controls. This is a potential operational-trust differentiator, not a verified competitive advantage or moat. No evidence here establishes lower fees, faster payouts, greater solvency or more accurate accounting than competitors.

## 17. Marketing Intelligence

**Asset FIN-01 — Explainable Merchant statement.** **Evidence:** EV-FIN-003–004. **Eligibility:** SUPPORTING PROOF ONLY / SALES-USEFUL. **Angle:** show how eligible Order collections, configured deductions, adjustments and payouts roll into a current or frozen statement. **Qualification:** current-cycle Finance calculation, not an independent accounting reconciliation or profit statement.

**Asset FIN-02 — Traceable payout operations.** **Evidence:** EV-FIN-007–010. **Eligibility:** SALES-USEFUL. **Angle:** demonstrate requested, frozen, executed, proof-retained and merchant-acknowledged stages. **Qualification:** human-operated workflow; no automatic payout or service-time guarantee.

**Asset FIN-03 — Order-linked fee explanation.** **Evidence:** EV-FIN-005, 011. **Eligibility:** SUPPORTING PROOF ONLY. **Angle:** show configured fee snapshots tied to canonical Order/Shipment events. **Qualification:** pricing is profile-dependent; Finance has not proven outcome/profit improvement.

Avoid “real-time balance,” “automatic COD settlement,” “instant payouts,” “audited books,” “true profit,” “debt/credit scoring,” “multi-currency finance platform,” or “automated carrier remittance.”

## 18. Surprise Findings

Most notable: Finance distinguishes a provider's delivery observation, a Wossol Finance user's collection confirmation, a Merchant withdrawal request, a finalized provider settlement Batch quote/receipt, and proof-backed Merchant payout. Those are separate control/evidence stages. It also has two settlement vocabularies—Merchant financial cycles/batches and External Shipping Carrier Financial Cycles—that must never be collapsed into a single settlement claim.

The P1 system is much more than its simple overview cards, but its rigor is mainly procedural and evidentiary. The broad “financial brain” design language should not obscure the missing independent cash-reconciliation and full liability-ledger work.

## 19. Potential Category Reframes

Current truth can be framed as **a traceable merchant payout and financial-history workflow** or **Order-linked money records with controlled payout operations**. Finance is not currently proven as a full accounting system, cross-border banking network, automated settlement rail, profitability intelligence engine or financing/risk platform.

## 20. Brand Evidence

- **Accountability:** actor-attributed permissioned actions, private proof records, append-only ledger/events and correction trails.
- **Clarity:** Merchant sees scoped Order/fee/settlement facts and can inspect current vs frozen statements.
- **Careful control:** available funds are held against pending requests; full cycles and FX are frozen before payout execution.
- **Connectedness:** Orders, Tracking, Fees, Inventory and External Shipping outcomes feed explicitly bounded Finance facts.
- **Restraint:** delivery status alone does not become cash; zero/default FX fallbacks are rejected; provider/merchant/company costs remain distinct.

These are product qualities evidenced in code, not final brand positioning. No performance or emotional outcome is measured here.

## 21. Weaknesses / Risks / Gaps

1. Collection confirmation remains a privileged human action; source value `PROVIDER_REMITTANCE` does not establish provider settlement integration or independent reconciliation.
2. P1 debt calculation is based on settlement-eligible ledger net/coverage and the inspected Finance schema has debt-coverage evidence but no generic loan/credit-obligation model; broad P3 financing/debt promises are not established.
3. P3 Finance Operations says a request cannot be edited after creation; current Merchant endpoint allows changes to amount (partial only), payout method and note while status remains REQUESTED and batch OPEN.
4. Admin Merchant payout operations do not execute an integrated transfer; they record the human-executed payout after requiring evidence. Proof proves a file was retained, not authenticity or successful receipt by itself.
5. Carrier Finance wallet API labels the destination “QA / non-production wallet” and defaults to `QA-CARRIER-WALLET-001`. Production wallet/transfer capability is NOT VERIFIED; this surface must not be presented as live carrier remittance.
6. Finance reporting is cycle-ledger-derived. No bank-statement matching, provider remittance reconciliation, company accounting close, tax output, or independent general ledger was established.
7. Admin Fee Profile configuration and current Finance source are operationally rich; production profile completeness, correct assignment, carrier rate quality and real fees were not checked.
8. Current Merchant payout UI supports structured Binance/RedotPay/other crypto internal-account or TRC20/BEP20 destinations; a general active bank-transfer execution path is NOT ESTABLISHED.
9. Merchant dashboard capital-in-motion categories are deliberately non-additive; users could still mistake operational/pending values for cash already collected or available if wording/context is ignored.
10. Latest account of payout UI/flow and old Finance/Operations “Final V1” documents include historical or amended requirements; product/spec contract should be maintained together.
11. Finance-related source tests include two brittle Company Cost UI assertions that fail on exact old text/implementation expectations; they do not by themselves establish a user-facing defect. No live browser run occurred.

## 22. Future Strategic Potential

| Category | Assessment |
|---|---|
| Current foundation | Order-linked collection, ledger and fee snapshots, financial cycles, controlled adjustments, frozen payout FX/destination and retained proof |
| Inferred potential | reconciled delivered contribution, payout predictability, capital-in-motion decisions and External Shipping gross-margin visibility |
| Dependencies | actual remittance/bank feeds, accountable reconciliation, complete liabilities/costs, source-data quality, deployed payout operations and metric definitions |
| Brand relevance | could support “know where each payout stands” and traceable money operations after external validation |
| Not current truth | true delivered profit, automated reconciliation, reliable cash forecasting, credit scoring/lending or payout SLA |

## 23. Claim Safety

| Claim | Safety | Boundary |
|---|---|---|
| Merchant sees current/frozen cycle, line items and payout statuses | GREEN for implemented projections | authorization/scope and current data still apply |
| Collection is Finance-confirmed against a delivered current Shipment | GREEN for backend rule | manual confirmation; not independently verified remittance |
| Fees are stored against source events and pricing snapshots | GREEN for implemented configured cases | does not prove production fee configuration or profitability |
| Payouts are automatically sent or instant | RED | manual provider/human execution remains |
| Payment proof means payment is valid/received | RED | retained artifact plus separate Merchant acknowledgement; no independent proof validation |
| Wossol gives a complete debt/credit picture | RED / contract uncertain | no broad liability facility found; current summary is cycle-based |
| Carrier settlement is production-connected | RED / not verified | displayed QA/non-production wallet |
| Accurate delivered profit or audited financial statements | RED | external cost/revenue reconciliation and accounting close not established |

## 24. Commercial Magnitude

**FOUNDATIONAL.** Trustworthy settlement and payout operations are core to a COD commerce-service promise. The execution safeguards are valuable, but merchant cash outcomes depend on real provider remittance, correct fees, actual payout and reconciliation beyond repository evidence.

## 25. Strategic Classification

| Area | Classification |
|---|---|
| Order-linked collection, charges and merchant cycle statement | TABLE STAKES / FOUNDATIONAL TRUST |
| Frozen batch, payout destination/FX and private proof workflow | POTENTIAL DIFFERENTIATOR in operational control |
| Automatic collection/remittance/payout | NOT ESTABLISHED |
| Broad debt financing and general accounting | P3 intent exceeds inspected P1 |
| Carrier payable and Merchant charge connection | useful operational foundation; not profit or reconciliation |
| Reconciled delivered margin and decision intelligence | WHITESPACE / future opportunity |

## 26. Action Register

| Priority | Action | Rationale |
|---|---|---|
| MUST CLARIFY | Reconcile Finance debt/financing contract with the settlement-eligible ledger-net and debt-coverage implementation. Define included liabilities and cross-cycle behavior. | Prevents “debt”, “locked” and “available” from implying an unimplemented loan ledger. |
| MUST CLARIFY | Decide whether a REQUESTED withdrawal is immutable or editable before batch lock; align P3 Operations wording with the P1 route/UI. | Material contract and audit-trail expectation conflict. |
| MUST FIX before production claim | Replace/disable the QA Carrier Finance wallet path for production, or hard-block mark-paid outside explicitly non-production configuration. | Current code exposes a QA/non-production destination as the Carrier payout target. |
| MUST MATCH | Document which Finance collection source is human evidence vs provider-integrated, and implement external statement/remittance reconciliation only under a verified authority. | A source enum is not an integration. |
| MUST VERIFY | Reconcile Merchant payouts against actual transfer records and proof/receipt lifecycle; verify storage retention, access and operational payment controls. | Source tests do not verify cash movement, proof authenticity or deployment. |
| WORTH ADOPTING | Update two brittle Company Cost UI source assertions to test current scheduling and per-method editor contracts; resolve the extensionless-import Node harness issue or use the repository's intended runner. | Current source-level verification is noisy/incomplete. |
| MUST BEAT | Define an evidence-complete delivered-margin denominator combining acquisition cost, collection, fees, inventory and actual payout/provider cost. | Do not market profitability from partial ledger facts. |
| WHITESPACE | Add provider-remittance reconciliation and finance exception decisions before forecasting, scoring or autonomous finance actions. | History becomes useful only when tied to external cash truth. |

## 27. Evidence Register

**EV-FIN-001 — Merchant Finance UI and data client.** **Type:** P1/P2. **Paths:** `apps/frontend/src/app/merchant/finance/page.tsx`, `cycles/[cycleId]/page.tsx`, `settlements/[settlementId]/page.tsx`, `finance-statement.tsx`, `finance-data.ts`. **Observed:** Workspace-aware overview, current/previous statement routes, request/edit/cancel/confirm UI, structured crypto destination and QR, support links, export and evidence display. **Confidence:** High for source; no browser acceptance.

**EV-FIN-002 — Finance APIs and authorization.** **Type:** P1/P2. **Paths:** `apps/backend/src/modules/finance/{finance.controller.ts,merchant-finance-access.service.ts,admin-carrier-finance-access.service.ts,carrier-finance.controller.ts}`, `apps/backend/src/modules/permissions/permission-catalog.ts`. **Observed:** Merchant active membership/scope and finance permissions; Admin active Workspace and operation-specific permissions for collections, adjustments, batches, proof, payout and carrier finance. **Confidence:** High for inspected controller/service code and focused access/controller specs.

**EV-FIN-003 — Ledger, collection and balance calculations.** **Type:** P1/P2. **Paths:** `finance.service.ts` (`confirmCollection`, `summary`, `balance`, `ensureOpenCycle`), schema `FinancialCollection`, `FinancialLedgerEntry`, `FinancialDebtCoverage`, `FinancialCycle`. **Observed:** delivered/current Shipment and exact amount/currency guards, unique per Workspace/Order collection, serializable transaction, one earning credit/source key, fee trigger and timeline/audit; current summary derives cycle credits/debits, debt and pending holds. **Confidence:** High for source and selected passing tests; no production reconciliation.

**EV-FIN-004 — Merchant statement/detail/export.** **Type:** P1/P2. **Paths:** `finance.service.ts` (`merchantOverview`, `currentCycleDetail`, `settlementStatement`, `statementWorkbook`), `FinancialSettlementSnapshot`, `FinancialSettlementOrder`, `FinancialSettlementLedgerEntry`. **Observed:** cycle eligibility and frozen itemized snapshots, fee/adjustment/activity breakdown, paginated Merchant statements and XLSX exports. **Confidence:** High for source and focused tests.

**EV-FIN-005 — Operational fee assessment.** **Type:** P1/P2. **Paths:** `finance.service.ts` (`chargeOperationalFeeWithClient`, `chargePlatformServiceFeeWithClient`), `FinancialOperationalFeeAssessment`, `FinancialPlatformServiceAssessment`; call sites in Orders/Confirmation/Tracking/External Shipping/Local Pickup. **Observed:** Fee Profile active rule lookup, trigger-bound immutable assessment/ledger and deterministic source-event keys; COD can be fixed or percentage of confirmed collection; selected zero rules remain assessment-only. **Confidence:** High for source/tests.

**EV-FIN-006 — Manual adjustment and audit trail.** **Type:** P1/P2. **Paths:** `finance.service.ts` (`createManualAdjustment`, `audit`, `timeline`), `FinancialManualAdjustment`; Admin Merchant Finance statement page. **Observed:** permissioned credit/debit, merchant note plus internal reason, same-workspace Merchant/Store checks, idempotency, ledger/timeline/high-sensitivity audit and correction reference. **Confidence:** High for source and focused tests.

**EV-FIN-007 — Merchant withdrawals, methods, holds.** **Type:** P1/P2. **Paths:** `finance.service.ts` (`requestWithdrawal`, `updateWithdrawal`, `cancelWithdrawal`, payout method methods, `summary`), `MerchantWithdrawalRequest`, `MerchantPayoutMethod`. **Observed:** active structured destinations, duplicate key, debt/available guards, at most one active partial, hold before paid debit, store partial vs workspace full; REQUESTED/open-batch edit behavior. **Confidence:** High for source and focused tests.

**EV-FIN-008 — Current Settlement Batch/FX workflow.** **Type:** P1/P2. **Paths:** `finance.service.ts` (`requestProviderSettlement`, `prepareWithdrawalForBatch`, `recordSettlementBatchQuote`, receipt/incident/prefund methods), `MerchantSettlementBatch*`, `MerchantSettlementBatchPanel.tsx`. **Observed:** auto enrollment, provider-request freeze, successor batch, cycle snapshots, destination/principal freeze, per-Merchant active Fee Profile spread and quote-derived USDT rates; direct withdrawal review is retired. **Confidence:** High for source and focused tests.

**EV-FIN-009 — Merchant payout proof and paid ledger.** **Type:** P1/P2. **Paths:** `finance.service.ts` (`stageWithdrawalPaymentEvidence`, `markWithdrawalPaid`, `confirmWithdrawalReceipt`), `finance-payment-evidence-storage.service.ts`, `MerchantWithdrawalPaymentEvidence`. **Observed:** proof required server-side, staging-owner/scope binding, paid-state guards, one withdrawal debit, actor/time, read scopes and independent Merchant receipt acknowledgment. **Confidence:** High for source/tests; no transfer was made during audit.

**EV-FIN-010 — Admin Finance operations UI.** **Type:** P1/P2. **Paths:** `apps/frontend/src/app/admin/finance/{page.tsx,CurrentSettlementBatchPanel.tsx,merchants/[merchantId]/page.tsx,merchants/[merchantId]/cycles/[cycleId]/page.tsx}`. **Observed:** withdrawal queue, Merchant settlement/profile/statement, batch/provider-FX panel and distinct External Shipping settlement tab; current direct withdrawal-review action is absent. **Confidence:** High for source.

**EV-FIN-011 — Fee Profile and commercial settings.** **Type:** P1/P2/P3. **Paths:** `apps/backend/src/modules/admin-fees/{admin-fees.service.ts,admin-fees.controller.ts}`, `apps/frontend/src/app/admin/fees/**`, Admin Fees UI/implementation specifications. **Observed:** workspace-scoped profiles, operational/platform service rules, external-shipping Merchant rates and scheduled/versioned Company Carrier Cost; Finance requires valid active fee assignment/spread for relevant posting. **Confidence:** High for source; production assignment completeness not checked.

**EV-FIN-012 — External Shipping Carrier Finance.** **Type:** P1/P2. **Paths:** `apps/backend/src/modules/finance/{carrier-finance.service.ts,carrier-finance.controller.ts}`, `CarrierFinancialCycle`, `CarrierPayableLine`, `CarrierSettlementPaymentProof`, Admin Finance carrier panel. **Observed:** unique carton accrual from verified measurement and frozen company rate; cycle freeze/proof and PAID check; invokes Merchant Finance external-shipping charge in same transaction. Wallet service returns explicit QA/non-production label/default. **Confidence:** High for source and focused tests; production use not verified.

**EV-FIN-013 — Local Pickup.** **Type:** P1/P2. **Paths:** `finance.service.ts` (`chargeLocalPickupGoodsPurchaseWithClient`, `chargeLocalPickupWithClient`), Inventory/Local Pickup integration and tests. **Observed:** goods-purchase debit at inventory completion is a separately classified ledger item, distinct from destination-based service fee. **Confidence:** High for source/tests.

**EV-FIN-014 — Finance persistence and lifecycle schema.** **Type:** P1. **Path:** `apps/backend/prisma/schema.prisma` Finance models/enums and `apps/backend/prisma/migrations/*finance*`, `*settlement*`, `*carrier*`. **Observed:** typed ledger, collection, cycles/snapshots, withdrawals, proof, FX, manual adjustments, debt coverage, carrier cycles and payable lines. No generic loan obligation/financing model was identified in Finance schema/module search. **Confidence:** High for inspected schema/search; not proof of no external accounting system.

**EV-FIN-015 — Finance intent/contract documents.** **Type:** P3/P4. **Paths:** `docs/wossol-system-design/01-system-design/admin-platform/merchant-finance/{Financial System (Final — Complete).md,Operations Workspace.txt,Transactions, Adjustments, Timeline & Audit.txt,Overview & Module.md}`, `docs/ui/merchant/MERCHANT_FINANCE_UI_SPEC.md`, `docs/ui/admin/ADMIN_FEES_UI_SPEC.md`, `docs/engineering/ADMIN_FEES_V1A_IMPLEMENTATION_SCOPE.md`. **Observed:** Workspace ledger ownership, debt/withdrawal/settlement intent, post-appended payment-evidence amendments, operational Fee Profile direction, and merchant/admin views; older text is not uniformly synchronized with P1. **Confidence:** High for the documents' content as intent, not executable truth.

**EV-FIN-016 — Verification.** **Type:** P2. **Observed:** backend selected Finance/Carrier Finance/Admin Fees suite completed with 92 tests, 0 failures; `@wossol/backend` and `@wossol/frontend` typechecks passed. Selected frontend Finance/Fees source checks had 21 passing and 2 failing exact-text/source-structure assertions in `carrier-cost-company-cost-ui.spec.ts` (old lowercase validation text and removed `setFormMessage` expectation). Attempted `carrier-settlement-workspace.spec.ts` separately could not load because direct Node ESM resolution did not resolve its extensionless `admin-data` import; treat as runner limitation, not app behavior. No live/browser/database test. **Confidence:** High for local commands only.

## 28. Contradictions & Uncertainty

1. **CONTRADICTION-FIN-001 — Debt/financing scope.** **Source A (P3):** `Financial System (Final — Complete).md` defines debt as all outstanding obligations, includes product financing/future loans, and describes earnings-first coverage. **Source B (P1):** Finance model/module search identifies ledger, cycles and `FinancialDebtCoverage`, but no generic loan/credit obligation source; `summary()` computes `debt = max(0, -net)` from `settlementCandidates()`—un-included facts in the current OPEN cycle plus eligible carry-forward facts from prior settlement snapshots—and current-cycle detail aggregates only that cycle's recorded coverage. **Nature:** broad financing/debt intent exceeds inspected current calculation and source entities. **Evidence strength:** High for inspected P1 scope and tests; P3 documents are intent. **Working conclusion:** call the displayed calculation a settlement-eligible ledger-net/coverage projection; do not assert a general debt/financing engine. **Remaining uncertainty:** liabilities may exist in other domains/deployed systems. **Required verification:** reconcile intended debt universe and cross-cycle handling with Finance product authority and trace every debt source.
2. **CONTRADICTION-FIN-002 — Withdrawal immutability.** **Source A (P3):** `Operations Workspace.txt` and Finance UI intent state no edit after request; cancel and create anew. **Source B (P1):** `POST merchant/finance/withdrawals/:id/edit` and Merchant UI permit partial amount, payout method or note edits only while REQUESTED and in the open Current Batch; edit is rejected after freeze. **Nature:** pre-freeze mutability policy differs. **Evidence strength:** High current source vs P3 intent. **Working conclusion:** actual behavior is editable before Batch lock; do not call requests immutable. **Required verification:** product authority chooses contract; reconcile UI/API/spec and audit event semantics.
3. **CONTRADICTION-FIN-003 — Carrier Finance destination.** **Source A (P1/UI):** carrier settlement has review/proof/mark-PAID workflow and UI wallet panel. **Source B (P1):** `qaWallet()` explicitly labels the destination `QA / non-production wallet` and falls back to `QA-CARRIER-WALLET-001`; Admin carrier payment updates internal status and cross-posts a Merchant charge. **Nature:** operation-looking workflow is paired with a non-production payment destination. **Evidence strength:** High current controller/service/UI. **Working conclusion:** current code establishes a controlled QA/test carrier-payable workflow, not production carrier remittance. **Remaining uncertainty:** production external process may be separate. **Required verification:** establish intended deployment/env guard and actual company payment rail before claiming live settlement.
4. **CONTRADICTION-FIN-004 — Payment-proof lifecycle.** **Source A (earlier P3):** Operations Workspace Final V1 says no payout proof upload and labels it V2. **Source B (later P3 amendment and P1):** later appended Payment Evidence V1 correction requires private proof before PAID; current schema/service/UI/tests implement proof. **Nature:** historic limitation has a later explicit correction. **Working conclusion:** follow the later amendment and current source; earlier no-proof text is stale, not a present implementation defect. The retained proof is still not independent proof of successful transfer.
5. **CONTRADICTION-FIN-005 — Human collection vs provider source label.** **Source A (P1):** `FinancialCollectionSource` includes `PROVIDER_REMITTANCE`. **Source B (P1):** Admin collection endpoint accepts the enum and optional reference, but inspected code only validates Order/Shipment/amount and records user confirmation; no provider remittance API match/reconciliation appears in the inspected Finance path. **Nature:** source taxonomy can be mistaken for integration. **Working conclusion:** collection is manually confirmed Financial evidence; a provider source value/reference alone does not prove external reconciliation. **Required verification:** inspect any separate provider remittance integration and establish its authority if one exists.
6. **UNCERTAINTY-FIN-006 — Real money and accounting.** No production database, bank/wallet records, provider remittance, transfer execution, Finance storage configuration, Merchant assignment/profile completeness, external reconciliation, production accounting close, tax/accounting output, or deployed permissions were checked. **Working conclusion:** local P1/P2 establishes code paths only; cash truth and production readiness remain NOT VERIFIED.
7. **CONTRACT CLARIFICATION — Financial currency.** The older P3 limitation says no multi-currency engine. P1 ledger uses active Workspace currency, while payout execution computes USDT from manually recorded Provider quote and Merchant spread, and External Shipping applies FX to LYD. This is operation-specific FX, not a general multi-currency ledger; the distinction should be explicit in current contract.

## 29. Open Questions

1. What exact liabilities comprise Finance “debt,” and how are prior-cycle liabilities/coverage carried or settled?
2. Should a requested partial withdrawal's amount, payout method and note remain editable until provider Batch freeze?
3. Is Carrier Finance QA-only in current deployable product? What blocks non-production wallet configuration from production payout use?
4. Does a separate Accurate/Mayar provider remittance integration exist, or is all collection confirmation an operator-posted fact?
5. How are proof authenticity, storage retention, external transfer reference, failed/reversed payment and dispute resolution handled operationally?
6. What production reconciliation joins Finance ledger to provider statements, merchant transfers, company carrier expenses, acquisition costs and inventory costs?
7. What tests/runtime establishes active fee-profile assignment, current payout FX inputs, private storage configuration and actual payment timing?

## 30. Methodology Learnings

No general methodology change identified. Section-specific: trace every “settlement” noun to its entity, owner, money direction and external authority. Wossol has Merchant withdrawal settlements, Workspace settlement batches, financial-cycle snapshots and separate External Shipping carrier payable cycles; similar labels do not imply one workflow or cash rail.

## 31. Retroactive Review Impact

No methodology change and no retroactive queue entry. The Tracking/Delivery review explicitly requires Finance to own fees, settlement and ledger truth while treating delivery as evidence input; the audit carries that boundary into EV-FIN-003/005 and the workflow. Other accepted sections are not altered. No Finance review record existed at synchronization.

## 32. Canonical Section Takeaway

Finance has a substantial current foundation for attributable Merchant ledger facts, Order-linked collection confirmation, fee assessment, cycle statements and controlled human payouts, with a distinct External Shipping payable/charge path. It is not yet proven to be complete accounting, broad lending/debt management, automatically reconciled collection, production carrier remittance, automated payouts or delivered-profit intelligence. Reconcile the debt and withdrawal-edit contracts, make the Carrier QA/non-production boundary unmistakable, and validate real external money flows before stronger financial claims.
