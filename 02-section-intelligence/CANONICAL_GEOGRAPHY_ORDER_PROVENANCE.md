# Canonical Geography & Order Provenance — Targeted Foundation Supplement

**Status:** targeted source-foundation supplement; not a Geography UI, Analytics, or Market Center re-audit. **Source:** `jetshop7/wossol-platform`, `dev/wossol-integration`, HEAD `007d317522d6e1eaa8e2e01d4a6d0608da812ce6` (upstream-aligned); Product working tree clean at inspection. **Trigger/authority:** `04-review-history/CURRENT_PRODUCT_CAPABILITY_COVERAGE_REVALIDATION_2026-09-28.md` and `04-review-history/FINAL_PRODUCT_CAPABILITY_COVERAGE_GATE_2026-09-28.md`, GAP-04. Product source only; no Product files changed.

## Current truth

`CanonicalGeographyService` is invoked at canonical Order create/edit boundaries. When the additive reference schema is available, it records immutable/versioned `OrderGeographyInterpretation` evidence tied to the raw source fingerprint and matcher version, including raw provider and region/city/zone identifiers, canonical IDs, match source and `MATCHED` / `PARTIAL` / `UNMATCHED` completeness. A changed current interpretation is superseded transactionally. Confidence remains null: deterministic configured matches are not probabilities. If the optional geography table is unavailable, a warning is logged and otherwise-valid Order creation/edit is not rejected.

Matching is constrained to effective-dated exact provider location-ID mappings or unique normalized exact configured aliases. Ambiguous mappings fail closed; there is no fuzzy matching, geocoding or AI inference. The current reference catalogue/mapping configuration is not seeded, and no current merchant geography UI or downstream Analytics consumer was found in the targeted search.

## Value and limits

**Classification:** SCAFFOLD / provenance foundation, with persistence conditional on additive schema availability and configured mappings. Its present value is to retain source identity and explicit unknown/partial outcomes rather than conflate unstable provider/display names. Its future potential is safer geographic joins for Analytics/Market Center, but storage is not a geographic insight, recommendation or validated market benchmark. No current geo-intelligence claim is supported.

**Evidence:** Product `apps/backend/src/modules/canonical-geography/canonical-geography.service.ts`, `.spec.ts`, Orders create/edit call sites and Prisma schema/migrations. Targeted `rg` consumer search found no current non-test reader outside the producer module. Focused suite: **7/8 passed**; the one failing assertion expects a single update call containing both `supersededAt` and `supersededById`, whereas current source closes the prior row first and links it to the created interpretation in a second update inside the caller transaction. This test/source contract mismatch remains unresolved; it does not by itself prove a persisted-data defect, and this closure does not mark the test stale or pass. Owner links: `ORDERS.md`, `ANALYTICS_DECISION_CENTER.md`, `MARKET_CENTER.md`.

## Provenance

This closes GAP-04 in the 2026-09-28 Director coverage records cited above. Deployment migration state and real configured mappings remain unverified. Pending Director Coverage Quality Gate.
