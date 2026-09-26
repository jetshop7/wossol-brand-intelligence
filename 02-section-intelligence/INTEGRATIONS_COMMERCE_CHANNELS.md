# Integrations / Commerce Channels — Section Intelligence

## 1. Audit Metadata

- **Audit date:** 2026-09-26.
- **Methodology:** `00-methodology/MASTER_INSTRUCTIONS.md` v1.1; `00-methodology/CODEX_OPERATING_PROTOCOL.md` v1.0.
- **Competitive reference:** `01-competitive/WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md` v1.
- **Intelligence source:** `jetshop7/wossol-brand-intelligence`, `main`, `0153cbea25c8e9122d4cadd3558b104b5c2bb84d` at audit start, clean and synchronized with `origin/main`.
- **Product source:** `jetshop7/wossol-platform`, `dev/wossol-integration`, `8600a4cbd1a894579a057b3476db35465289c670`, clean and tracking `origin/dev/wossol-integration`; inspected read-only.
- **Evidence standard:** P1 executable source/schema; P2 focused tests/typechecks; P3 current product/UI architecture specifications; P4 historical designs qualified as such. Inspection does not verify deployment, live provider acceptance, merchant adoption, data quality at scale, legal basis, or commercial outcomes.

## 2. Audit Coverage Map

| Surface | Coverage | Evidence |
|---|---|---|
| Shared Commerce domain | providers, connection scope, identity, mappings, delivery state | EV-IC-001–004 |
| Shopify connection and catalog | install/auth, products/variants, mappings, outbound projection | EV-IC-005–007 |
| Shopify Wossol COD channel | app extension, signed proxy, canonical order, projection boundary | EV-IC-008–009 |
| Native Shopify order intake | receiver/event path and current-vs-historical docs | EV-IC-010–011 |
| YouCan connection and webhooks | OAuth/hooks, signature, normalization, destination resolution, error path | EV-IC-012–015 |
| YouCan catalog and admin capabilities | API/UI reachability and capability metadata | EV-IC-016–017 |
| Connected domains and source state | Products, Orders, Inventory, Delivery, Finance, Advertising, Analytics | EV-IC-018–020 |
| Verification and competition | focused tests, typechecks, competitive master | EV-IC-021–022 |

Not verified: deployed versions or production DB state, actual connected provider accounts, successful real-world imports/exports, provider delivery/retry policy, throughput, merchant usage, privacy/compliance acceptance, or current competitor implementations first-hand.

## 3. Executive Section Truth

Commerce channels are **unevenly operational**. Shopify has a real merchant-authorized connection, explicit exact product/variant mapping, product creation/linking, and a distinctive Wossol COD storefront path that creates canonical Wossol Orders and can mirror those orders back to Shopify. That outbound mirror must not be confused with inbound native Shopify Checkout order intake: no current inbound Shopify order receiver was found. YouCan has a real connection and signed order webhook plumbing, but the implemented order normalizer emits only external destination evidence while the shared importer requires a Wossol Destination ID. Consequently, the inspected YouCan order path deterministically fails before canonical Order creation. The connection is not evidence of a functioning end-to-end YouCan order channel.

The common substrate contributes scoped connections, exact identity mappings, and durable webhook-delivery records. It does not make all advertised provider capabilities available as complete merchant workflows. WooCommerce remains a “Coming soon” UI entry, not an implemented provider.

The strongest demonstrated value is exact catalog relationship plus a bounded Shopify COD order-conversion path for Wossol’s delivery/pricing model. Current evidence does not support broad “multichannel commerce management,” “all orders centralized,” inventory synchronization, or complete commerce-to-profit claims.

## 4. Scope & Architecture Map

Commerce connections are scoped through Workspace/Merchant/Store and provider identity. Product and Variant mappings bind exact external IDs to Wossol entities in that scope; Orders own canonical order persistence and idempotency. Shopify app and COD services call the shared Commerce/Orders seams. YouCan webhooks also call the shared order resolver. The architectural seam is soundly separated, but YouCan’s current normalized destination shape cannot pass that seam. Delivery providers are a separate operating domain; Advertising connections and attribution are separate from store integrations; Finance remains authoritative for collections, fees, and ledger evidence. (EV-IC-001–004, 018–020.)

## 5. Current Capability Inventory

| Capability | Status | Current behavior |
|---|---|---|
| Provider connections | Implemented, asymmetric | Shopify and YouCan connect; connection state and credentials are scoped and retained safely. |
| Product/Variant identity | Implemented | Exact provider IDs and explicit mappings; no SKU/title-based identity inference. |
| Shopify catalog operation | Implemented, bounded | Explicit create/link/sync actions with mapping scope and guarded media operation. No general continuous sync. |
| Shopify native order intake | Not found | No inbound Shopify order webhook/controller path identified. |
| Shopify Wossol COD | Implemented, narrow | Signed app proxy uses Wossol catalog, destination, price/fee authority and Orders import; optional Shopify mirror is outbound. |
| YouCan order webhooks | Partially implemented / blocked | Signature verification and delivery processing exist; normalized external destination evidence is rejected by canonical resolver. |
| YouCan catalog | Backend partial | Catalog and mapping endpoints/API methods exist; no corresponding merchant mapping workflow found in the YouCan page. |
| Inventory synchronization | Not established | Provider adapter metadata may describe capabilities, but no verified complete Shopify or YouCan merchant inventory workflow. |
| WooCommerce | Not implemented | Applications UI labels it “Coming soon.” |

## 6. Workflow & Lifecycle

Shopify install/auth → scoped connection and secure credentials → explicit product/variant create or link → Wossol COD extension reads exact mappings and canonical delivery/pricing state → signed proxy submits to Commerce resolver → Orders owns canonical create/idempotency and downstream owner-domain behavior → optional durable outbound Shopify Order projection. Native Shopify Checkout is not shown entering this pipeline.

YouCan connect/auth → required hooks installed and status surfaced → signed webhook is deduplicated/claimed → current provider order is fetched and normalized → exact product mappings and destination validated → intended Orders import. The last path currently fails at destination resolution because the normalizer emits external location text and the resolver accepts only an exact Wossol Destination ID. `order.updated` and `order.paid` reuse initial-import behavior; they do not synchronize changes to an already imported Order. (EV-IC-005–015.)

## 7. Value Recipient Map

- **Merchant/operator:** less manual catalog duplication for exact Shopify mappings; Wossol COD checkout can collect a delivery-address request in a locally grounded canonical flow.
- **Customer:** familiar storefront product journey with Wossol-managed COD fields and delivery quote.
- **Operations:** canonical Wossol Orders can proceed through Orders-owned confirmation/inventory paths when validly created.
- **Current YouCan user:** connection and hook visibility, but no demonstrated successful canonical order ingestion under inspected code.
- **Wossol:** scoped provider identity and exact mappings provide a foundation for future integration workflows; foundation is not itself realized channel breadth.

## 8. Control & Merchant Agency

Shopify product linking/creation is explicit and scoped. The Wossol COD form cannot choose arbitrary Wossol IDs or authoritative prices from the browser; server mappings and canonical destinations constrain the request. Provider connection/disconnection does not imply catalog or order synchronization. YouCan mapping/admin functionality is incomplete as a visible merchant workflow; inspected source does not establish merchant control over failed order correction/replay.

## 9. Transparency & Trust

Exact provider IDs and Wossol mappings avoid fuzzy identity. Shopify COD separates Wossol Order creation from Shopify outbound projection. The integration surfaces should disclose that native Shopify Checkout orders are not currently imported, YouCan connection does not currently establish successful order ingestion, and provider capability metadata is not proof that a UI/workflow exists. Delivery payload storage is bounded to event evidence/hash rather than raw webhook body in the common delivery record. (EV-IC-003–004, 008–017.)

## 10. Merchant Value Extraction

The present merchant outcome is narrower than “connect your store and run commerce in Wossol”: connect supported provider identity, explicitly map Shopify catalog entities, and use the Shopify Wossol COD block to create a Wossol-owned order. The operational advantage depends on exact mappings and configured canonical delivery data. No measured time savings, conversion lift, order completeness, or profit impact was available.

## 11. Feature Clusters

1. **Scoped provider identity:** authorization, account/store identity, credential lifecycle.
2. **Exact catalog bridge:** explicit product/variant links and selected Shopify create operations.
3. **Canonical order boundary:** normalized facts, mapping checks, destination validation, Orders-owned import/idempotency.
4. **Shopify COD storefront channel:** Wossol-controlled checkout request, canonical Order, optional provider mirror.
5. **YouCan webhook foundation:** signed event and durable delivery processing, currently blocked by destination mismatch.

## 12. Merchant Journey / Old Way vs Wossol Way

For the supported Shopify COD case, the merchant can retain Shopify product presentation while using Wossol’s canonical COD/order/delivery context rather than manually recreating each requested order. The evidence does **not** show an all-channel order inbox or automated reconciliation for native Shopify checkout, YouCan order changes, inventory, and finance. Traditional/manual alternatives are plausible, but effort or outcome comparisons were not measured.

## 13. Hidden / Non-Obvious Advantages

- Order ownership and outbound mirroring are deliberately distinct; this preserves Wossol’s canonical workflow rather than treating Shopify as canonical.
- Exact IDs, scoped mappings, and idempotency are stronger trust foundations than superficial “integration” checkboxes.
- Shopify COD uses Wossol delivery destinations and authoritative quote logic in a storefront path.
- Webhook delivery durability exists as reusable infrastructure, but the YouCan destination contract shows that durable delivery alone does not guarantee successful business processing.

## 14. Data & Intelligence Assets

Connections and mappings preserve provider identity relationships; webhook records preserve event identity/hash and processing status; canonical Orders preserve accepted commerce facts. This is operational context, not automatically clean cross-channel customer intelligence, inventory truth, demand intelligence, attribution, or profit. YouCan failed deliveries do not become valid canonical Orders merely because event evidence is retained.

## 15. Cross-Section Compound Advantages

- **Products:** Wossol Product/Variant identity is mapped explicitly to external catalog IDs.
- **Orders:** canonical creation/idempotency and lifecycle are Orders-owned.
- **Inventory / Confirmation:** validly created Orders use owner-domain behavior; integrations do not replace those domains.
- **Tracking/Delivery:** canonical delivery configuration is separate from store-provider connection.
- **Advertising:** provider reporting and acquisition attribution remain distinct; a store connection or order does not imply ad attribution.
- **Finance:** connection/order projection is not settlement reconciliation or profit; Finance owns financial records.
- **Analytics / Decision Center and Market Center:** integration facts alone do not prove demand, market performance, or decision intelligence.

## 16. Competitive Analysis

The competitive master describes integrations as common/table-stakes across relevant products, including broader named channel coverage in some competitors. Treat those as the master’s documented landscape, not a fresh competitor capability audit. Presence of a connector is not a defensible differentiator by itself. A potentially differentiated Shopify COD experience is visible in architecture/code, but comparative superiority, reliability, adoption, or outcomes are unverified. (EV-IC-022.)

## 17. Marketing Intelligence

Potentially supportable: “Connect a Shopify store, map products and variants, and offer Wossol COD checkout on the storefront, with resulting orders created in Wossol and optionally mirrored to Shopify.” Must qualify availability/configuration and not imply native Shopify Checkout order import. Do not market YouCan as successful order ingestion until destination resolution is addressed and verified. Do not imply multichannel parity or inventory synchronization.

## 18. Surprise Findings

1. Shopify has meaningful operational code but no current native Shopify inbound order receiver was found; current inbound creation path is Wossol COD form.
2. YouCan’s signed/order webhook flow has an explicit deterministic failure at a shared resolver contract boundary, not merely an unverified provider runtime.
3. YouCan capability metadata lists a wider surface than the current merchant workflow; this metadata is descriptive, not authorization or proof of user-accessible functionality.

## 19. Potential Category Reframes

The evidence supports a **commerce-to-operations bridge** framing for the narrow Shopify COD path: storefront product context can cross into Wossol’s canonical operational order flow. It does not yet support “unified commerce OS,” “omnichannel order intelligence,” or a generalized integration platform promise. These are analytical possibilities, not chosen positioning.

## 20. Brand Evidence

Evidence of careful identity boundaries, merchant-authorized connection, backend-authoritative checkout, exact catalog mapping, and a canonical operational order seam could support trust and operational-coordination themes. Broken YouCan ingestion and absent native Shopify Order intake constrain any broader claim of seamless connected commerce. No final brand promise or positioning is inferred.

## 21. Weaknesses / Risks / Gaps

- YouCan orders fail canonical ingestion when destination is external provider text; no mapping/alias resolution route was found.
- YouCan tests mock successful order resolution and miss the normalizer→resolver incompatibility; a separate deterministic-failure test confirms the failure path.
- YouCan returns an accepted HTTP response even for a deterministic processing failure; no internal retry worker, replay UI, or manual correction workflow was found. Provider retry behavior for this response is unknown.
- `order.updated` and `order.paid` do not update an existing Wossol Order.
- Shopify native Checkout orders are not shown entering Wossol; older architecture material conflicts with current executable behavior and newer COD source document.
- YouCan catalog mapping routes lack a matching visible merchant mapping page in inspected frontend.
- Capability registry is wider than verified provider/user workflows.
- No continuous catalog/inventory synchronization or cross-channel reconciliation established.
- Full backend targeted test run: 327 tests; 324 passed, 3 failed. One stale source-text assertion in Shopify COD acceptance, one stale Shopify manual-linking fake missing `commerceProductMapping.updateMany`, and one PostgreSQL test blocked by configured DB-name mismatch before DB access. Treat as verification caveats, not product acceptance.
- Frontend typecheck passed. Backend typecheck was started; completion status could not be recovered from the command session, so it is not claimed as passing.
- Migration filenames in product tree extend later than audit date; this audit did not verify deployed migration/database state.

## 22. Future Strategic Potential

Closing YouCan destination mapping plus replay/correction would convert its existing connector/event scaffolding into a verifiable order path. Native Shopify order import and update reconciliation could expand breadth but require clear ownership/idempotency decisions. Completing merchant-facing YouCan catalog workflows and carefully scoped sync policies could increase usefulness. These are future opportunities, not current capability claims.

## 23. Claim Safety

| Claim | Safety |
|---|---|
| “Shopify connection and explicit product mapping” | Supported by inspected source/UI. |
| “Wossol COD checkout on Shopify can create a Wossol Order” | Supported narrowly by source/tests; production acceptance and outcomes unverified. |
| “Wossol COD orders may be mirrored to Shopify” | Supported as best-effort durable outbound projection; not Shopify-originated canonical order intake. |
| “Import Shopify orders” | Unsupported by inspected current source. |
| “YouCan orders sync into Wossol” | Contradicted by current destination resolver path. |
| “Integrates with WooCommerce” | Unsupported; UI says coming soon. |
| “Unified inventory / multichannel / commerce-to-profit” | Unsupported by inspected evidence. |
| “All integrations are real-time/reliable” | Unsupported; deployment/runtime and full operational acceptance unverified. |

## 24. Commercial Magnitude

Potential magnitude is moderate for merchants using Shopify storefronts and Wossol COD operations, because the path joins customer order intent to operational execution. Realized magnitude is unquantified. Broader commerce-channel magnitude is presently low/uncertain given absent native Shopify intake, blocked YouCan imports, and lack of continuous inventory/order reconciliation. No revenue or ROI figures inferred.

## 25. Strategic Classification

- **Current strength:** scoped Shopify connection plus a bounded storefront-to-canonical-order bridge.
- **Infrastructure strength:** exact mapping, canonical ownership and durable event identity patterns.
- **Current limitation:** provider asymmetry; YouCan order ingestion presently fails; Shopify native intake absent.
- **Differentiation:** not proven at category level; Shopify COD execution may be a candidate for further evidence.
- **Evidence confidence:** high for inspected code paths and incompatibility; moderate for completeness of repository search; low for production behavior and market outcomes.

## 26. Action Register

| Priority | Action | Owner domain | Why |
|---|---|---|---|
| P0 | Define exact YouCan destination-to-Wossol mapping/selection and make normalized order pass the canonical resolver; add end-to-end contract test. | Product / Commerce | Current order path deterministically fails. |
| P1 | Add replay/correction visibility for failed provider deliveries and clarify acknowledgment/retry semantics. | Commerce / Operations | Avoid silent loss after accepted webhook. |
| P1 | Decide and implement Shopify native order intake or keep its absence clearly disclosed. | Commerce / Orders | Align architecture and merchant expectation. |
| P1 | Define semantics for YouCan updates/paid events against an existing Wossol Order. | Commerce / Orders | Current behavior only initial import. |
| P2 | Align capability metadata with executable and merchant-visible workflows. | Commerce / Product | Avoid implied parity. |
| P2 | Add merchant-facing YouCan catalog mapping workflow if supported as a product promise. | Product UI | Backend endpoints are not an end-to-end merchant tool. |
| P2 | Re-run target suites with corrected stale fixtures and required DB fixture; document CI/source state. | Engineering | Resolve verification caveats. |

## 27. Evidence Register

| ID | Evidence | Tier | Finding supported |
|---|---|---|---|
| EV-IC-001 | `apps/backend/src/modules/commerce/commerce-provider.ts`; Commerce Prisma models/migrations | P1 | Current provider set, scoped connection/mapping model. |
| EV-IC-002 | `commerce.service.ts`, `commerce.service.spec.ts` | P1/P2 | Connection scope/lifecycle and exact mapping behavior. |
| EV-IC-003 | `commerce-order-normalization.ts`, `commerce-order-resolution.service.ts`, resolver specs | P1/P2 | Normalized contract, exact mappings/destination, Orders seam/idempotency. |
| EV-IC-004 | `commerce.service.ts`, `commerce-webhook-processing.spec.ts` | P1/P2 | Durable delivery identity, states/claims/failure handling. |
| EV-IC-005 | `shopify.controller.ts`, `shopify.service.ts`, OAuth/API tests | P1/P2 | Shopify install, authorization and connection state. |
| EV-IC-006 | Shopify product service and specs; Products Shopify mapping UI | P1/P2 | Explicit product/variant creation/linking and exact IDs. |
| EV-IC-007 | `shopify-order-projection.service.ts` and tests | P1/P2 | Outbound Wossol COD Order mirror, durable retries/reconciliation. |
| EV-IC-008 | `shopify-cod.service.ts`, App Proxy/controller, extension block and COD specs | P1/P2 | Signed storefront path and canonical Wossol Order submission. |
| EV-IC-009 | `shopify.app.toml`; current Shopify COD source-of-truth doc | P1/P3 | Extension/proxy scopes and distinction from native Shopify order intake. |
| EV-IC-010 | Search of Shopify/Commerce controllers, webhook receivers, `orders/create` and webhook registrations | P1 | No current inbound native Shopify Order receiver identified. |
| EV-IC-011 | Historical Commerce/Shopify master doc (2026-08-26); newer COD master (2026-09-08); acquisition architecture | P3/P4 | Documentation chronology and native-order-intake distinction; current code resolves operational truth. |
| EV-IC-012 | `youcan-connection.service.ts`, controller, OAuth and connection specs | P1/P2 | YouCan authorization, required hooks, status/lifecycle. |
| EV-IC-013 | YouCan webhook controller/verification/normalizer and specs | P1/P2 | Signature validation, event identity, bounded normalization, external destination evidence. |
| EV-IC-014 | `commerce-order-resolution.service.ts` lines around `resolveDestination`; resolver specs | P1/P2 | Only Wossol Destination ID is accepted for canonical Order import. |
| EV-IC-015 | YouCan connection tests for unresolved destination and updated/paid topics | P2 | Deterministic failed delivery; updates/paid share initial-import path, no sync. |
| EV-IC-016 | YouCan Store Admin/controller and frontend `applications/youcan/page.tsx` | P1 | API/admin operations and limited connection UI; no catalog mapping workflow surfaced. |
| EV-IC-017 | Commerce provider capability registry/spec; Merchant Applications UI spec | P1/P2/P3 | Capabilities are descriptive metadata; WooCommerce “Coming soon.” |
| EV-IC-018 | Products, Orders, Inventory, Confirmation, Tracking/Delivery intelligence artifacts | P3 | Owner-domain cross-section boundaries. |
| EV-IC-019 | Finance, Advertising, Analytics/Decision Center, Market Center artifacts | P3 | No implicit finance/attribution/decision/demand conclusions from integration state. |
| EV-IC-020 | Current product architecture/Cross-System Event Flow docs | P3 | Intended domain boundaries, qualified against executable code. |
| EV-IC-021 | Focused backend suites (327 tests: 324 pass, 3 fail); frontend typecheck passed | P2 | Verification and precise failures/blocker; backend typecheck result unavailable. |
| EV-IC-022 | `01-competitive/WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md` | P3 | Competitive context only; not a current independent competitor audit. |

## 28. Contradictions & Uncertainty

- Historical general Shopify commerce architecture describes native Shopify order intake as approved/expected, while its implementation status is stale/pre-implementation; current executable source and newer COD documentation show Wossol COD ingress only. Current code is operational truth for this audit.
- YouCan API/capability metadata suggests broad catalog/order/admin coverage, but visible workflows are narrower and current order creation is incompatible with destination resolution.
- Provider webhook retry after HTTP 200 on deterministic processing failure is unknown; do not assert provider loss or retry.
- Repository source and tests do not establish deployed version, production data, or successful connected-account operations.
- Later-dated migration filenames in the checkout create chronology uncertainty relative to audit date; migration/deployment state was not verified.

## 29. Open Questions

1. What exact merchant-configurable rule should resolve YouCan location evidence into an eligible Wossol Destination ID, and how should ambiguous/unmatched orders be surfaced?
2. Are YouCan updates intended to mutate only permitted Order fields, append evidence, or remain immutable after import?
3. Is native Shopify Checkout order ingestion in scope, or should the product intentionally remain Wossol COD-originated?
4. What provider retry semantics follow a 200 response that records `FAILED`, and who can safely replay it?
5. Which advertised YouCan capabilities are committed product workflows versus unused adapter primitives?
6. What are current deployed migration state, live acceptance results, volume/error rates, and merchant usage?

## 30. Methodology Learnings

For an integration audit, distinguish connection, provider API surface, event receipt, canonical domain acceptance, and visible/operable merchant workflow as separate maturity stages. Trace a normalized event through the actual receiving resolver; webhook plumbing and mocked success tests do not prove end-to-end import. No methodology change proposed.

## 31. Retroactive Review Impact

No methodology change requiring a retroactive audit was made. This audit’s scope distinction may inform future integrations audits, but does not change prior findings by itself.

## 32. Canonical Section Takeaway

Wossol currently demonstrates a meaningful but narrow Shopify storefront-to-operations bridge: exact catalog mapping and Wossol COD checkout can create a canonical Wossol Order, with optional outbound Shopify projection. It does not currently establish native Shopify Checkout ingestion or broad multichannel commerce. YouCan has real connection and signed-webhook infrastructure, but current destination normalization is rejected by the shared Order resolver, so successful YouCan order ingestion is not supported by inspected code. Claims should describe those provider-specific boundaries rather than treating a connected account or capability list as a working channel.
