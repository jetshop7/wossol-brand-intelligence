# Home — Fresh code-grounded study (in progress)

Status: **investigation in progress; not a final approved marketing source**. Reviewed implementation on `jetshop7/wossol-platform`, branch `dev/wossol-integration` (branch snapshot inspected 2026-10-11; exact commit pin pending).

## Current code-supported role
Home is a scoped operational starting surface. It combines Orders-derived priorities, today's activity, current workload, event-history performance, Analytics-owned recommendations and recent notifications. It is **not** itself an independent finance engine or recommendation engine.

## Verified implementation paths
1. **Orders ↔ Inventory → Home priority.** `MerchantPortalService.getWaitingForStockImpact` reads visible WAITING_FOR_STOCK Orders and active reservation-required items, requests scoped batch availability from `InventoryReservationService.getAvailabilityBatch`, groups shortage candidates by Store/Product/Variant, computes affected Orders, counts Orders whose other requirements are sufficiently stocked, estimates shortages where availability is known, and ranks candidates by directly-unblockable Orders, then affected Orders, then shortage. Home shows the **top candidate only** and links to filtered waiting Orders. This is a prioritization aid, **not a promise that replenishment will automatically release orders**.
2. **Orders status history → Home performance.** Backend queries latest qualifying Confirmation event per Order within the selected period, and Tracking history outcomes, with merchant/workspace/authorized-store filtering. Supported ranges: TODAY, 7, 14, 30 days.
3. **Analytics → Home recommended actions.** Home calls `MerchantAnalyticsService.getMerchantHomeRecommendations`, which obtains canonical Analytics for LAST_7_DAYS and projects up to three `nextBestActions`. Recommendation links currently go to general `/merchant/analytics`, not unique detail pages. If unavailable, Home flags this without suppressing core Orders.
4. **Notifications → Home recent updates.** Up to three notifications from NotificationsService; failures are isolated from Home Summary.
5. **Orders → Home Today/Current Work.** Today uses Workspace timezone, created date and status-history transitions for confirmed/delivered; Current Work excludes stock-blocked Orders from active Confirmation.
6. **Permissions/scope.** `resolveHomeScope` checks active Workspace, selected/authorized Stores and section access. Orders projections are omitted without Orders permission; Analytics recommendations are conditional on Analytics access.
7. **Frontend freshness.** Manual refresh and focus/visibility refresh after 5 minutes of successful Summary staleness; freshness label is client-side fetch age, **not provider-data freshness**.

## Evidence and test status
- **Source-inspected:** `apps/frontend/src/app/merchant/page.tsx`, `home-data.ts`; `apps/backend/src/modules/merchant-portal/merchant-portal.controller.ts`, `merchant-portal.service.ts`; `apps/backend/src/modules/analytics/merchant-analytics.service.ts`; `apps/backend/src/modules/inventory/inventory-reservation.service.ts`.
- **Test code inspected, not executed:** `merchant-home-summary.service.spec.ts` covers authorized scope, stock impact, permissions, recommendation failure, attention ordering, history semantics; `merchant-home-summary.postgres.spec.ts` covers latest qualifying Confirmation outcome; frontend `merchant-home-v1.spec.ts` covers display, scope freshness and failure states.
- **Not verified:** deployed behavior, real merchant data, current provider data, passing test results, measurable business impact, full permissions/data-leak audit.

## Candidate merchant value (inference, not measured)
- **Attention compression:** merchant may identify high-priority operational issues without opening multiple sections.
- **Stock impact prioritization:** merchant may focus replenishment on shortages linked to potentially unblockable Orders.
- **Decision distribution:** bounded Analytics recommendations can reach Home while Analytics retains calculation ownership.

## Limitations / follow-up
- Top shortage candidate only; unknown inventory does not become a fabricated zero.
- Provider availability may be cached; inventory reservation semantics and tests require deeper review.
- Home recommendation path can invoke Analytics calculation/materialization; verify side effects before calling Home strictly read-only.
- No independent runtime verification or verified test execution yet.
- Reconcile historical Home UI-spec contradictions and notification permission/projection concerns without adopting old conclusions automatically.
- Examine merchant journeys and cross-system failure/authorization cases before marketing approval.

## Source links
- [Home frontend](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/frontend/src/app/merchant/page.tsx)
- [Merchant Portal backend](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/merchant-portal/merchant-portal.service.ts)
- [Analytics backend](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/analytics/merchant-analytics.service.ts)
- [Inventory backend](https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/inventory/inventory-reservation.service.ts)
