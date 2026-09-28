# Merchant Settings & Store Delivery Pricing — Targeted Capability Supplement

**Status:** targeted V1.2 capability supplement; reuses accepted Stores, Notifications, Team/Auth, Products, Orders and Tracking / Delivery findings without re-auditing them. **Source:** `jetshop7/wossol-platform`, `dev/wossol-integration`, HEAD `007d317522d6e1eaa8e2e01d4a6d0608da812ce6` (upstream-aligned); Product working tree clean at inspection. **Trigger/authority:** `04-review-history/CURRENT_PRODUCT_CAPABILITY_COVERAGE_REVALIDATION_2026-09-28.md` and `04-review-history/FINAL_PRODUCT_CAPABILITY_COVERAGE_GATE_2026-09-28.md`, GAP-03. Product source only; no Product files changed.

## Surface and authority

The authenticated `/merchant/settings` API and merchant Settings UI combine profile, business display name, account/contact, security, email notification preference, Store management, and Store-scoped Delivery Pricing. Profile/contact/email/password/avatar and preference changes are audited; email and password changes increment session version and invalidate existing sessions. Business-name and pricing mutations require Owner/Admin. Avatar storage is private and bounded. Email notification preference does not disable in-app notifications. Store lifecycle details remain owned by `STORES.md`; notification delivery remains owned by `NOTIFICATIONS.md`.

Delivery Pricing is a commercial control, not merely Store metadata. The selected active Store and Workspace, Merchant, assigned Fee Profile, active destination/provider zone and permissions are checked. The Fee Profile remains authority for Wossol’s delivery cost (destination override before governorate default). Merchant Owner/Admin may set/reset a Store customer-facing delivery-price override by supported provider zone; unsupported/incompletely priced destinations cannot be freely invented. Product-destination override and free-delivery policy have higher precedence for the applicable Product flow. Order/Confirmation consume persisted price snapshots, so later Settings edits do not silently rewrite historical Orders. The subsidy deduction is bounded below at zero: a customer markup changes the amount charged to the customer, not into a negative merchant deduction. This does not make the Store override authoritative for provider costs.

## Value, work and boundaries

Settings consolidates personal account/security, business identity and a Store-level customer pricing control within one merchant surface. The pricing override can reduce repeated per-destination pricing coordination and enables Store-level commercial choice while retaining platform Fee Profile ownership and explicit reset-to-inherited behavior. This is plausible workflow reduction, not measured labor, conversion or margin improvement. Pricing changes require eligible setup and do not alter already-created Order snapshots.

**Classification:** LIVE, source-backed for Settings and delivery-price control. **Value:** operational control, agency and risk-reduced pricing provenance. **Brand/market claim:** supporting proof only; no claim of autonomous pricing, fee optimization, provider-rate control, guaranteed margin or universal coverage. Key remaining verification is deployment/runtime and actual merchant use; tests/source do not establish either.

**Evidence:** Product `apps/frontend/src/app/merchant/settings/page.tsx`, `settings-data.ts`; backend `merchant-settings/merchant-settings.controller.ts`, `merchant-settings.service.ts`, delivery-pricing service/specs, Orders/Confirmation snapshot consumers and audits. Owner references: `STORES.md`, `NOTIFICATIONS.md`, `PRODUCTS.md`, `ORDERS.md`, `TRACKING_DELIVERY.md`.

## Provenance

This targeted supplement closes GAP-03 from the two 2026-09-28 Director coverage records named above. It is not Director acceptance.
