# Merchant Global Search — Targeted Capability Supplement

**Status:** targeted Product-capability coverage supplement; not a replacement for Home or the 20 accepted section audits. **Source:** `jetshop7/wossol-platform`, `dev/wossol-integration`, HEAD `007d317522d6e1eaa8e2e01d4a6d0608da812ce6` (upstream-aligned); four local uncommitted paths were present and excluded. **Trigger/authority:** `04-review-history/CURRENT_PRODUCT_CAPABILITY_COVERAGE_REVALIDATION_2026-09-28.md` and `04-review-history/FINAL_PRODUCT_CAPABILITY_COVERAGE_GATE_2026-09-28.md`, GAP-01. Product source only; no Product files changed.

## Current truth

`MerchantShell.tsx` mounts `MerchantGlobalSearch` with the active Workspace and selected Store. The component debounces requests, cancels prior requests, supports keyboard selection and routes results to their owning screens. `MerchantPortalController` exposes authenticated `GET /merchant/search`; `MerchantGlobalSearchService` builds a bounded, read-only projection over Products/Variants, Orders, Customers, External Shipments, exact Withdrawal references, Support tickets, Team members and Stores. Queries shorter than two characters return no results; longer than 80 are rejected; result limits are capped and ranked deterministically. It does not persist search history, call providers, or calculate business state.

Backend checks active Merchant and Workspace membership, section access for Products, Orders, Customers, External Shipping, Finance and Support, and Store access. Team and Store results are Owner/Admin-only. Store scope is applied to Store-owned domains; Customers are Merchant/Workspace-scoped, consistent with their owner model. Results may include authorized Order/Customer contact identifiers (including phone); this is not public search and the authorization boundary remains material.

## Value and limits

This is a live cross-domain navigation/context-continuity capability. It can reduce repeated page switching, manual identifier reconstruction and the work of locating an object before acting in its owning workflow. No reduction was measured. Search itself grants no new control, does not combine domain records into a new operational truth, and does not replace each destination’s authorization or workflow. It is supporting Merchant Shell capability, not a standalone intelligence engine or evidence of unified data semantics.

**Classification:** LIVE, source-backed; merchant-facing. **Strategic reading:** supporting operational-effort reduction and context continuity; no differentiation claim established. **Evidence:** Product `apps/frontend/src/app/merchant/MerchantShell.tsx`, `MerchantGlobalSearch.tsx`; backend `apps/backend/src/modules/merchant-portal/merchant-portal.controller.ts`, `merchant-global-search.service.ts`, `.spec.ts`; Store/section access services. The accepted section audits remain owners of the underlying data and destination actions.

## Provenance

This targeted supplement closes the specific omission recorded in the two 2026-09-28 Director records named above. It does not amend those review records and remains pending Director Coverage Quality Gate.
