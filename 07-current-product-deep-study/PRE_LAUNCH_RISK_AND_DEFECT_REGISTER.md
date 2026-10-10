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
