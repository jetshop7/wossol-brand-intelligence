# Products cross-system capability ledger

Status: IN PROGRESS; static source inspection, no executed tests or deployment verification. 2026-10-11, wossol-platform dev/wossol-integration.

## P-CS-01 Product/Variant identity to Order and Inventory
OrdersService.listMerchantOrderableProducts selects scoped Product/Variant links for one Store, REAL versus TEST, and effective Inventory availability. Order creation persists immutable name/code/price snapshots and calls InventoryReservationService.reserveOrderItems for reservation-required lines. Inventory locks Variant rows, checks ownership, aggregates requested quantities, and returns WAITING_FOR_STOCK or rejects insufficient stock according to allocation policy. WaitingStockPromotionService retries full allocation transactionally. Merchant value hypothesis: continuity from product setup to controlled order handling. Caveat: not every shipment type requires reservation, Test Orders skip it, and promotion timing is unverified.

## P-CS-02 Product/Variant to Accurate/Mayar
ProductsService requires provider mapping before activating newly created Products/Variants, compensates partial remote create, preserves reconciliation evidence when uncertain. LINK recovery distinguishes FAILED_RETRYABLE from ACTION_REQUIRED. Product-level update commits locally first; provider delete is sequential and precedes local archive. Caveat: no distributed atomicity guarantee.

## P-CS-03 Product to Shopify
ProductConnections.tsx exposes explicit Product and Variant linking, draft create, missing Variant sync, relink, unlink. Backend and provider outcomes require full trace. No automatic name/SKU mapping claim.

## P-CS-04 Product/Variant to Meta Advertising
ProductAdvertisingMappings.tsx exposes Product/Variant links to persisted Meta advertising hierarchy and correction/end lifecycle. No evidence that Products itself launches or optimizes ads.

## P-CS-05 Product to Orders historical integrity
ProductsService.deleteMerchantProduct checks OrderItem historical use before provider calls and blocks deletion, preserving downstream references. Local archive occurs after successful provider delete sequence when permitted.

Sources:
https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/orders/orders.service.ts
https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/inventory/inventory-reservation.service.ts
https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/orders/waiting-stock-promotion.service.ts
https://github.com/jetshop7/wossol-platform/blob/dev/wossol-integration/apps/backend/src/modules/products/products.service.ts
