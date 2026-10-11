# Cross-system capability discovery — active working register

Status: **provisional source-backed findings, pending complete code/test/runtime review**. Product source: `jetshop7/wossol-platform`, `dev/wossol-integration`; inspected 2026-10-11. Do not treat as approved marketing claims.

## CS-001 — Stock shortage → potentially unblockable Orders → Home priority
- **Systems:** Orders, Inventory, Home.
- **Code path:** `MerchantPortalService.getWaitingForStockImpact` → active reservation-required Order items → `InventoryReservationService.getAvailabilityBatch` → per-variant insufficiency, affected Orders, directly-unblockable Order count → top-ranked Home attention → filtered Orders link.
- **Merchant value hypothesis:** helps prioritize a shortage by operational Order impact rather than stock count alone.
- **Limit:** top candidate only; only known insufficiencies ranked; availability may be cached; directly-unblockable is conditional, not an actual order release.
- **Evidence:** [Merchant Portal Service](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/merchant-portal/merchant-portal.service.ts), [Inventory Reservation Service](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/inventory/inventory-reservation.service.ts), `merchant-home-summary.service.spec.ts` (inspected, not executed).

## CS-002 — Canonical Analytics recommendations → Home
- **Systems:** Analytics/Decision Center, Home.
- **Code path:** `MerchantPortalService.getHomeSummary` (Analytics permission gate) → `MerchantAnalyticsService.getMerchantHomeRecommendations` → `getMerchantAnalytics(LAST_7_DAYS)` → up to 3 nextBestActions → Home cards.
- **Merchant value hypothesis:** makes selected analytics-derived priorities visible on entry.
- **Limit:** all cards currently route to general Analytics; Analytics may materialize recommendations; runtime behavior not verified.
- **Evidence:** [Analytics service](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/analytics/merchant-analytics.service.ts), [Merchant Portal Service](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/merchant-portal/merchant-portal.service.ts).

## CS-003 — Confirmation/Tracking event history → Home performance
- **Systems:** Orders status history, Confirmation, Tracking, Home.
- **Code path:** scoped `order_status_history` query, latest qualifying Confirmation outcome per Order within period; Tracking outcome aggregation; percentage presentation on Home.
- **Merchant value hypothesis:** operational outcome view based on event transitions rather than only current status.
- **Limit:** specific semantics and corner cases require deeper validation; not evidence of full causal performance attribution.
- **Evidence:** [Merchant Portal Service](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/merchant-portal/merchant-portal.service.ts), `merchant-home-summary.postgres.spec.ts` (not executed).

## Pending investigation
Follow Inventory availability provenance; Analytics materialization/side effects; Notifications authorization; merchant action destinations and complete journeys; test execution; final cross-system sweep. Archived studies are reference material only.
