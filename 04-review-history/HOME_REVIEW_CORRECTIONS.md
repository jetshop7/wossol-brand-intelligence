# Home Review Corrections

## Review record

| Field | Value |
|---|---|
| Section | Home |
| Corrected intelligence artifact | `02-section-intelligence/HOME.md` |
| Review source | Codex task history: “Wossol Merchant → Home UX/UI Operational Command Center — Final Approval” and “Wossol Merchant Home — Final UX Polish” |
| Review basis | Product implementation review and focused source-level regression checks |
| Reviewed product source | `jetshop7/wossol-platform`, branch `dev/wossol-integration`, commit `e842e8e4e45ba4748412232c914fdeb20063b8ab` |
| Correction date | 2026-09-25 |
| Resolution | Applied to the canonical Home intelligence record; no product-repository change required |

## Corrections applied to the intelligence record

1. Home is recorded as a blocker/attention-first operational surface.
2. Today is recorded as exactly three metrics: Orders created today, Confirmed today, and Delivered today. Needs Attention remains a separate section.
3. The trend chart, trend range controls, and ring-based performance presentation are recorded as removed from current executable Home.
4. Duplicate Waiting-for-Stock presentation in Current Work is recorded as absent; stock context remains only in the dedicated attention projection.
5. Recent Updates is recorded as bounded to three current implementation items, not the stale six-item specification.
6. Performance secondary outcomes are recorded as conditional non-zero details; primary outcomes are not redundantly repeated.
7. Recommendations remain an Analytics-owned, bounded projection. Home does not calculate, rank, explain raw evidence, persist, or execute recommendations.
8. Authorization, Workspace/Store isolation, race protection, partial dependency failure handling, manual refresh, and merchant-safe copy are recorded as preserved review constraints.

## Evidence and limitations

The corrections are supported by current executable source and the task’s reported focused validation. This record does not claim authenticated production/rendered approval. The Home UI specification remains stale in the conflicting areas and is retained as a documented contradiction until its owning team reconciles it.

## Methodology impact

None. Existing contradiction, current-vs-future, evidence hierarchy, and claim-safety rules were sufficient.
