# Workspace Payment Configuration — Targeted Cross-Domain Supplement

**Status:** targeted clarification across accepted Products, Orders and Confirmation audits; not a standalone payments audit or claim. **Source:** `jetshop7/wossol-platform`, `dev/wossol-integration`, HEAD `007d317522d6e1eaa8e2e01d4a6d0608da812ce6` (upstream-aligned); four local uncommitted paths were present and excluded. **Trigger/authority:** `04-review-history/CURRENT_PRODUCT_CAPABILITY_COVERAGE_REVALIDATION_2026-09-28.md` and `04-review-history/FINAL_PRODUCT_CAPABILITY_COVERAGE_GATE_2026-09-28.md`, GAP-05. Product source only; no Product files changed.

## Current contract

Workspace Admin API `/admin/settings/payment-configuration` is permission- and Workspace-scoped. It versions configuration with expected-version concurrency, requires a reason, and records an audit event. It controls whether POS Card and E-payment methods are operationally allowed for that Workspace and stores merchant/customer processing-charge allocation percentages constrained to total 100. An absent config defaults both electronic methods off and allocation to merchant 0% / customer 100%.

Product payment policy is a separate per-Product, versioned policy. At Order creation its sold-line policy is snapshotted. Effective eligibility combines the current Workspace operational allow-list with the immutable payment-method snapshot for the exact active sold-line set. Missing/unknown snapshot versions fail closed for electronic methods; legacy Orders retain COD eligibility without invented electronic evidence. Orders and Confirmation consume this resolver for payment selection/eligibility checks, including merchant-preconfirmed paths.

## Value and limits

This is real privileged configuration and historical-policy control across Products → Orders → Confirmation. It can prevent currently disallowed or historically unsupported payment methods from being offered and keeps later Product policy edits from silently changing the sold Order’s eligibility. It does **not** connect a payment provider, collect/authorize/settle an electronic payment, create a transaction record, or prove that an enabled method executes. The processing-charge percentages are stored policy; a corresponding actual fee charge/execution path was not found in the targeted consumer search. Therefore use “method eligibility/configuration,” not “integrated payments,” “payment processing,” or realized fee allocation. Deployment and live payment provider behavior are unverified.

**Evidence:** Product `workspace-payment-configuration` controller/service/spec; Product policy in `products.service.ts`; Order resolver call in `orders.service.ts`; Confirmation selection/dispatch call sites. Owner audits: `PRODUCTS.md`, `ORDERS.md`, `CONFIRMATION.md`, `FINANCE.md`.

## Provenance

This supplement closes the verification action for GAP-05 in the two 2026-09-28 Director coverage records named above. It does not supersede section findings or create a new payments section. Pending Director Coverage Quality Gate.
