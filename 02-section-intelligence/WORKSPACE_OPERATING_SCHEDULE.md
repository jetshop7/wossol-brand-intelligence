# Workspace Operating Schedule — Targeted Supporting-Control Supplement

**Status:** targeted classification of a cross-cutting Admin control; not a new merchant section. **Source:** `jetshop7/wossol-platform`, `dev/wossol-integration`, HEAD `007d317522d6e1eaa8e2e01d4a6d0608da812ce6` (upstream-aligned); four local uncommitted paths were present and excluded. **Trigger/authority:** under-audited Admin System Settings in `04-review-history/CURRENT_PRODUCT_CAPABILITY_COVERAGE_REVALIDATION_2026-09-28.md`; final closure requirements in `04-review-history/FINAL_PRODUCT_CAPABILITY_COVERAGE_GATE_2026-09-28.md`. Product source only; no Product files changed.

## Current truth

Admin `/admin/settings` exposes Workspace timezone, working weekdays, opening/closing time, expected-version mutation, required reason and audit history. The UI explicitly describes these as a Workspace standard and says they do not shut down the platform/providers outside those hours. The configuration is not merely decorative: Confirmation call scheduling/operational-date policy and warehouse schedule calculation in External Shipping read Workspace timezone and working-day fields; timezone also supplies calendar interpretation to Orders and merchant/platform Analytics. Thus it can shape selected Wossol workflows and date buckets, but is not a universal execution kill switch or provider-hours control. No evidence shows every domain consumes every field consistently.

## Classification and limit

**Classification:** LIVE supporting operational configuration, with consumer-specific effects. It provides bounded schedule/calendar control and can reduce repeated manual interpretation of working windows. It does not guarantee staff availability, provider availability, automatic stoppage of all work, or a global service-level schedule. Exact workflow effects remain owned by Confirmation, External Shipping, Orders and Analytics. Source/tests do not prove deployment or real-world adherence.

**Evidence:** Product `apps/frontend/src/app/admin/settings/page.tsx`; backend `system-settings` controller/service/spec; consumers `confirmation-call-engine-policy.ts`, Confirmation service, `external-shipping.service.ts`, Orders date-range helpers, Admin Platform Analytics. Cross-links: `CONFIRMATION.md`, `EXTERNAL_SHIPPING.md`, `ORDERS.md`, `ANALYTICS_DECISION_CENTER.md`.

## Provenance

This targeted classification closes the Admin System Settings verification request in the 2026-09-28 Director coverage records cited above. Pending Director Coverage Quality Gate.
