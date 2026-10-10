# WOSSOL — PRE-LAUNCH RISK & DEFECT VERIFICATION REGISTER

**Purpose:** Centralized, cross-section register of suspected defects, business-rule gaps, dangerous edge cases, and claims that require verification before launch. Findings are recorded while studying each section; they are **not automatically confirmed bugs**.

**Statuses:** SUSPECTED → REPRODUCED → CONFIRMED → FIX IN PROGRESS → FIXED → REGRESSION VERIFIED → CLOSED; or NOT REPRODUCIBLE / ACCEPTED RISK with evidence. **Severity** is provisional until reproduction. **Ownership:** engineering and product must decide fix priority.

## RISK-001 — Test Product eligibility in Confirmation Upsell

- **Source section:** Products; cross-section: Confirmation / Orders / Inventory.
- **Status:** SUSPECTED — code gap observed; runtime exploitability unverified.
- **Provisional severity:** HIGH if reproduced on Real Orders.
- **Code:** `apps/backend/src/modules/confirmation/confirmation.service.ts`, `updateUpsell` (variant selection, approximately lines 5529–5565 in `dev/wossol-integration`).
- **Observation:** The Confirmation Upsell variant query validates ACTIVE status, matching Product/Variant IDs, merchant/workspace and Store link. In the inspected method, it does **not** explicitly compare `lockedOrder.isTestRecord` with `variant.product.isTestProduct`. In contrast, `orders.service.ts` validates Test/Real classification on normal Order item selection; Shopify COD Upsells also exclude Test Products.
- **Potential impact:** If another guard does not block it, a Test Product could be added to a Real Order through Confirmation Upsell, affecting OrderItems, commercial totals and possibly inventory reservation. Conversely, Real Product inclusion on Test Orders should also be checked. No successful reproduction is claimed.
- **Reproduction plan:** In an isolated database create an ACTIVE Real Product, ACTIVE Test Product, and Real Order in the same authorized Store. Before dispatch, call the authorized Confirmation Upsell update endpoint with the Test Variant and valid positive quantity/rowTotal. Record response, OrderItem, ConfirmationUpsellItem, InventoryReservation, Order subtotal/total, action log and timeline. Repeat with a Test Order and Real Variant. Test UI filtering and direct API invocation separately.
- **Expected:** Reject incompatible Test/Real classification with a structured error; leave Order, reservations and audit unchanged.
- **If confirmed:** Add backend invariant at the transactional selection/validation boundary; include `isTestProduct` in selected Product fields, enforce equality with `lockedOrder.isTestRecord`, add regression tests for both directions and multi-line sets. Do not rely only on UI filtering.
- **Release gate:** Not closed until reproduced/ruled out, fixed if necessary, and regression tested.
- **Evidence:** source inspection, not executed end-to-end test.

## RISK-002 — Confirmation Upsell rollback under reservation failure

- **Source section:** Products / Confirmation / Inventory.
- **Status:** VERIFICATION REQUIRED.
- **Provisional severity:** MEDIUM–HIGH.
- **Code:** `confirmation.service.ts` `updateUpsell`; `inventory-reservation.service.ts`.
- **Observation:** Upsell row changes, reservations, Order totals, and audit are structured within a Prisma transaction; inventory service can throw `INVENTORY_INSUFFICIENT_STOCK`. Existing inspected reservation tests assert rejection and no new reservation for selected unit cases. An executed database integration test of **entire Confirmation Upsell transaction rollback** was not established.
- **Risk:** Unexpected partial commercial/reservation/audit state under a failed update, if a boundary or side effect escapes transaction guarantees.
- **Acceptance:** Inject insufficient stock and other failures after partial work; verify all OrderItems, ConfirmationUpsellItems, InventoryReservations, Order totals, timeline and audit remain unchanged. Repeat under contention. If behavior is correct, close as VERIFIED SAFE, not as a defect.
- **Release gate:** Regression evidence recorded.

## Operating rules for subsequent section studies

1. Add each new suspected issue here immediately with stable ID, section, code path, observed behavior, impact, reproduction, expected result, status and release gate.
2. Never label a source-code suspicion a confirmed production defect without reproduction or decisive proof.
3. Keep product-strength analysis in section study documents; keep defects and verification tasks here.
4. Do not delete closed findings: preserve outcome, fix commit and regression evidence.
5. Before launch, review all unresolved HIGH/CRITICAL findings, and explicitly disposition every other entry.

## RISK-003 — Test Orders included in commercial Analytics populations

- **Source:** Historical Products V1.2 Director review (`04-review-history/PRODUCTS_V1_2_MIGRATION_REVIEW_2026-09-28.md`).
- **Status:** HISTORICALLY VERIFIED; CURRENT BRANCH REVALIDATION REQUIRED.
- **Provisional severity:** HIGH if still present: Test Orders can contaminate commercial metrics and Decision Center inputs.
- **Observation at historical audit:** Merchant Analytics `orderWhere` lacked `isTestRecord: false`; Inventory and Market Center had narrower explicit exclusions. The older audit explicitly warns that Test classification is not universal Analytics exclusion.
- **Test:** Audit every Analytics/economic/decision projection's population predicates on current HEAD; create mixed Test/Real fixtures; verify metrics, recovery measures, revenue and recommendations against intended business-truth semantics.
- **Expected:** Each projection has an explicit, documented Test population policy; commercial truth excludes Test transactions unless a deliberately labeled test metric is requested.

## RISK-004 — Final V1 Store-mapping contract vs merchant implementation

- **Source:** Historical Products intelligence and V1.2 review.
- **Status:** PRODUCT-CONTRACT GAP — CURRENT REVALIDATION REQUIRED.
- **Provisional severity:** MEDIUM; release scope decision required.
- **Observation:** Older Final V1 specification describes broader All Stores / mapping management; historical executable audit verified ProductStore association and scoped reads but did not establish general merchant ProductStore mutation management.
- **Test:** Compare approved current UI contract with ProductStore management endpoints/UI on current HEAD. Decide whether contract is still binding, superseded, or deferred. Do not mark “by design” without an approved decision.
- **Expected:** Implement contracted behavior or explicitly update the product contract and launch scope.

## RISK-005 — Product read-projection test/source mismatch

- **Source:** Historical Products V1.2 review.
- **Status:** HISTORICAL TEST ISSUE — CURRENT REVALIDATION REQUIRED.
- **Provisional severity:** LOW–MEDIUM.
- **Observation:** Historical focused suite reported 152/153 passing; one assertion expected non-archived Product visibility while actual projection required `ProductStatus.ACTIVE`.
- **Test:** Run focused current suite; confirm intended read-projection eligibility, repair stale assertion or implementation as appropriate.
- **Expected:** Agreed product visibility contract and passing tests. Historical failure does not alone establish a runtime defect.

## RISK-006 — External provider lifecycle and live readiness proof

- **Source:** Historical Products intelligence and current code review.
- **Status:** LIVE ACCEPTANCE REQUIRED.
- **Provisional severity:** MEDIUM–HIGH.
- **Observation:** Guarded inactive creation, provider mapping/compensation, recovery and provider-first deletion are source-backed, but no live-provider acceptance evidence was established in the historical review or this study.
- **Test:** Sandbox create, multi-variant partial failure, compensation failure, retry/manual review, variant edit and deletion with order-history guard.
- **Expected:** Merchant-visible states accurately reflect actual external outcomes and do not falsely indicate readiness.


## RISK-007 — Product image upload before creation: orphaned binaries

- **Source:** `merchant/products/create/page.tsx`, `ProductImageUploadSection.tsx`, `ProductsService.uploadMerchantProductImage`.
- **Status:** SUSPECTED / CLEANUP CONTRACT UNVERIFIED (not confirmed defect).
- **Provisional severity:** MEDIUM.
- **Observation:** Image upload writes filesystem files under `uploads/product-images/<merchantId>/new-product` before Product creation. Product save separately persists ordered image references. User cancellation, failed save and image removal from the form could leave unreferenced files; cleanup service/retention has not yet been exhaustively searched.
- **Test:** Upload then cancel, upload then fail Product POST, remove uploaded image, retry creation; inspect disk and any cleanup job. Test container/redeploy storage persistence.
- **Expected:** Explicit lifecycle/retention and cleanup of unreferenced media, with no accidental deletion of referenced images.

## RISK-008 — Provider-first deletion and local archive divergence

- **Source:** `ProductsService.deleteMerchantProduct`.
- **Status:** SUSPECTED DISTRIBUTED CONSISTENCY WINDOW (not confirmed defect).
- **Provisional severity:** HIGH.
- **Observation:** Accurate/Mayar Variant deletes execute sequentially before local Prisma archive transaction. If a later provider delete or local archive fails, earlier provider-side deletes may already have succeeded. Recovery/compensation logic has not yet been fully traced.
- **Test:** Simulate provider delete success on first Variant and failure on second, plus DB archive failure after all provider deletes; inspect merchant status, provider mappings, retry idempotency and audit/reconciliation.
- **Expected:** Durable, operator-visible reconciliation and safe retry without misleading active Product state.

## RISK-009 — Waiting-stock priority change is not explained during Order editing

- Section: Products / Inventory / Orders merchant experience.
- Status: CODE-SUPPORTED UX GAP; live reproduction pending.
- Severity: MEDIUM provisional.
- Evidence: OrdersService resets waitingForStockAt when stockDemandSignature changes. Merchant Edit Order submit flow saves and redirects without an identified priority-change notice.
- Impact: Merchant may expect the earlier waiting position to remain after changing requested items or quantities.
- Validation: Edit stock demand on an older waiting Order, compare waitingForStockAt and FIFO processing; repeat with an unrelated edit; inspect UI messaging and actual promotion.
- Resolution: Approve priority policy first; then disclose its effect before saving and verify the resulting status is clear. Do not invent a numeric queue position.
- Release gate: Policy decision, UI acceptance evidence and changed-versus-unchanged-demand regression coverage.

## RISK-010 — Bounded waiting-stock batch may repeatedly revisit blocked oldest Orders

- Status: PERFORMANCE / FAIRNESS HYPOTHESIS; load reproduction required.
- Severity: MEDIUM provisional.
- Evidence: WaitingStockPromotionService.processScoped selects first 100 waiting Orders by waitingForStockAt then id; blocked Orders remain waiting and eligible for the next batch.
- Risk: If the oldest 100 remain blocked, later Orders might not be reached by this scoped sweep even when their own stock is available. Other event paths and direct edit-triggered promotion may mitigate; do not claim starvation as confirmed.
- Acceptance: Create over 100 waiting Orders with old blocked Orders and newer stock-ready Orders; test scoped wake-up, recovery, fairness and eventual processing. Document batch progression policy.

## RISK-011 — Stock promotion succeeds but automatic Confirmation assignment fails

- Status: HANDOFF RECOVERY UNVERIFIED; not a confirmed lost Order.
- Severity: MEDIUM–HIGH provisional.
- Evidence: WaitingStockPromotionService.promoteOrder commits PENDING_CONFIRMATION and stock reservation before best-effort assignOrderAutomatically; assignment errors are logged without reverting promotion.
- Risk: Order may remain unassigned until another Confirmation recovery path processes it.
- Acceptance: Inject assignment failure after successful stock promotion; inspect Confirmation workload, retry schedulers, merchant-visible status and eventual assignment. Verify no duplicate assignment and no stranded Orders.


### RISK-011 — Confirmation recovery investigation update (2026-10-10)

- Confirmed from current code: assignOrderAutomatically records a pending Confirmation Overflow entry for TEAM_PROVISIONING_FAILED or NO_ELIGIBLE_CAPACITY. drainPendingOverflowForWorkspace retries bounded pending Overflow entries; authorized retryAutomaticAssignment also exists.
- Remaining gap: WaitingStockPromotionService catches an exception thrown by assignOrderAutomatically after successful promotion and logs it. No proof yet that an exception occurring before Overflow recording creates a durable recovery item or is found by a periodic scan of all unassigned PENDING_CONFIRMATION Orders.
- Revised acceptance: separately inject (a) no capacity with successful Overflow recording and (b) exception before Overflow recording. Verify durable recovery and eventual assignment for each. Keep RISK-011 OPEN until both are proven.


### RISK-011 — Oversight and test-coverage refinement (2026-10-10)

- ConfirmationOversightService counts unassigned Orders with no active assignment and separately counts pending Overflow; ConfirmationOversightProcessor periodically runs workspace oversight reconciliation (60-second interval in code, subject to write mode).
- This is detection/monitoring evidence, not proof of an automatic assignment retry for an unassigned Order without a persisted Overflow entry.
- Confirmation Overflow mocked tests cover idempotent pending-entry creation, recovery, bounded drain and worker-capacity triggers. They do not demonstrate an injected exception before Overflow persistence followed by automatic recovery of the resulting unassigned Order.
- Required test: promote waiting Order successfully, force assignOrderAutomatically to throw before recordOverflow, restart background workers, verify whether an independent process creates durable recovery work and eventually assigns; inspect merchant/admin visibility. Keep OPEN.

### RISK-011 — Narrowed missing-Overflow recovery case (2026-10-10)

- Source verification: drainPendingOverflowForWorkspace queries only ConfirmationOverflowEntry rows with status PENDING. ConfirmationOversightService.reconcileWorkspace reconciles ConfirmationAlert conditions; idleOrders specifically requires an active Confirmation assignment. Neither proves re-assignment of a PENDING_CONFIRMATION Order that lacks both an active assignment and Overflow entry.
- The mocked Confirmation Overflow drain test verifies one thrown assignment attempt is isolated *after* an Overflow entry already exists; it does not cover a missing Overflow entry.
- Test priority: Inject an exception before recordOverflow while promoting a waiting Order, then execute all scheduled recovery paths and verify eventual assignment, alerting and operator discoverability. If no recovery exists, propose bounded idempotent scan for eligible unassigned Orders with safe scope and retry controls; do not modify application code before approval.

### RISK-011 — Additional entry-path review

- OrdersService also calls automatic Confirmation assignment after normal Order creation and selected blocked-customer corrections. Some exceptions are logged after Order commit.
- Confirmation lifecycle manages team configuration, while Oversight reconciles alerts and Overflow drain retries only persisted Overflow entries.
- Expand verification to every eligible Order entry path. Inject assignment failure before Overflow persistence and check durable recovery. This is still a suspected gap, not a verified production defect.

## RISK-012 — Waiting-stock edit can fail after its changes are saved

- Status: suspected post-commit response mismatch; requires failure injection.
- Severity: MEDIUM provisional.
- Evidence: Merchant Order edit commits its database transaction, then awaits waitingStockPromotion.promoteOrder without a local catch. A promotion exception can make the request fail after edits were persisted.
- Impact: Merchant may see an error and retry a change already saved.
- Acceptance: Inject promotion failure after commit; verify database state, HTTP response, frontend message and safe retry. Ensure merchant sees an accurate save result.

### RISK-011 test coverage

- Inspected waiting-stock-promotion.service.spec.ts and confirmation-overflow.spec.ts. No inspected test proves automatic recovery after an assignment exception before Overflow persistence. Tests were not executed.

### RISK-012 — Backend-to-frontend trace

- OrdersService.updateMerchantOrder commits the edit transaction, then awaits waitingStockPromotion.promoteOrder without a local catch.
- promoteOrder performs a separate Serializable transaction and can throw before its internal post-promotion Confirmation assignment catch. That internal catch only handles Confirmation assignment errors after successful promotion.
- Merchant orders/create/page.tsx edit handleSubmit awaits updateMerchantOrder, navigates to Order Detail only on success, and otherwise calls presentSubmitError.
- Therefore an injected promotion transaction failure after successful edit commit can produce a merchant-visible save error despite persisted edits. Verify via actual failure-injection test before marking reproduced.
