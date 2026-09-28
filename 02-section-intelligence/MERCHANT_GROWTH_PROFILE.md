# Merchant Growth Profile — Targeted Capability Supplement

**Status:** targeted backend-foundation coverage; not a full section re-audit. **Source:** `jetshop7/wossol-platform`, `dev/wossol-integration`, HEAD `007d317522d6e1eaa8e2e01d4a6d0608da812ce6` (upstream-aligned); four local uncommitted paths were present and excluded. **Trigger/authority:** `04-review-history/CURRENT_PRODUCT_CAPABILITY_COVERAGE_REVALIDATION_2026-09-28.md` and `04-review-history/FINAL_PRODUCT_CAPABILITY_COVERAGE_GATE_2026-09-28.md`, GAP-02. Product source only; no Product files changed.

## Current truth

Authenticated Merchant Portal endpoints expose `GET/PATCH /merchant/growth-profile`. A Merchant Owner/Admin can record declared acquisition source/campaign/referral partner, experience level, business models, primary sales channels, current/target market country codes, approximate monthly Order band, canonical Product categories and team-size band. Active Workspace access is required. Updates create versioned records, supersede the prior current record transactionally and emit a high-sensitivity audit event with actor, reason and before/after snapshot; unchanged content is a no-op. Categories are validated against current canonical categories. No dedicated frontend page/surface was found in the current frontend tree; an API is not proof of an end-user workflow adoption.

The service explicitly contains no cohort calculation, inferred attribution, marketing automation or Analytics projection. The fields are Merchant declarations, not observed behavior or verified acquisition facts. Market countries use country codes; there is no seeded geography catalogue to imply region-level market intelligence.

## Value and limits

**Classification:** PARTIAL / backend evidence foundation; merchant-declared and versioned, with no verified dedicated UI or downstream decision consumer. Current value is preservation of structured context and accountability for its changes. Potential future segmentation, onboarding personalization, market guidance or cohort analysis is an opportunity only, not current intelligence. Do not claim that Wossol knows merchant fit, acquisition performance, growth trajectory or recommended next markets from this record.

**Evidence:** Product `apps/backend/src/modules/merchant-portal/merchant-portal.controller.ts`, `merchant-growth-profiles.service.ts`, `merchant-growth-profiles.service.spec.ts`, schema and audit event. Cross-links: Analytics / Decision Center and Market Center are potential future consumers, not verified current projections.

## Provenance

This supplement closes GAP-02 from the 2026-09-28 Director coverage records cited above. It preserves the current-vs-future distinction and is pending Director Coverage Quality Gate.
