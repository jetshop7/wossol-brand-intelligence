# Products — Section Intelligence

## 1. Audit Metadata

- **Audit date:** 2026-09-25
- **Methodology:** `MASTER_INSTRUCTIONS.md` v1.1; operating protocol current on audit date.
- **Competitive reference:** `WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md` v1.
- **Intelligence repository source:** `jetshop7/wossol-brand-intelligence`, `main`, `75954e84d31d2537f8cc4bfeb0083bd1e5fe82ae`, clean and synchronized with upstream before inspection.
- **Product repository source at correction:** `jetshop7/wossol-platform`, `dev/wossol-integration`, `e3912a967827bde06450d3510228e5a5ca9e78a7`, clean. The reviewed audit evidence was collected at `e842e8e4e45ba4748412232c914fdeb20063b8ab`; the intervening diff was outside Products and this correction did not re-audit Products.
- **Correction provenance:** targeted application of `04-review-history/PRODUCTS_REVIEW_2026-09-25.md` to reviewed artifact commit `dffc7d4`.
- **Evidence basis:** current executable code/schema (P1), automated tests and documentation aligned with code (P2), the current approved/final UI specification as Product intent (P3), and a dated architecture-reading summary (P4). Static inspection and automated checks establish implemented behavior, not deployment, provider connectivity, data quality, or live merchant adoption.

## 2. Audit Coverage Map

| Surface | Coverage | Evidence |
|---|---|---|
| Merchant UI | list, create, edit, variant edit, read-only detail, media, connections, advertising mapping controls | EV-PROD-001–004 |
| Product API/services | list/detail/edit/create/delete, variant lifecycle, image lifecycle, payment policy, provider recovery | EV-PROD-005–010 |
| Data model | Product, Variant, Store association, taxonomy, versioned payment policy, mappings and recovery state | EV-PROD-011 |
| Permissions and scope | authenticated active merchant, workspace/store ownership, Product and Commerce/Advertising visibility | EV-PROD-005, EV-PROD-012 |
| Connected domains | Inventory availability, Orders history guard and Test Order eligibility, Shopify, Advertising, Support, payment-policy snapshots | EV-PROD-006–010, EV-PROD-013, EV-PROD-017 |
| Tests and build checks | focused Products lifecycle/recovery tests; frontend Products UI checks; both app typechecks | EV-PROD-014 |
| Documentation comparison | current/final UI contract plus dated architecture summary; conflicts qualified below | EV-PROD-015, EV-PROD-018 |

Not audited as current Products capability: live Accurate/Mayar or Shopify accounts, provider APIs, production database contents, product performance/profitability, market/network sourcing, bulk catalog import, or product opportunity recommendations.

## 3. Executive Section Truth

Products is an implemented, scoped merchant catalog control surface rather than a passive SKU table. A merchant manages Product library records, executable Variants, taxonomy, images, landing references, payment-policy settings, store usage context, and selected channel relationships. The most consequential current behavior is a strict operational gate: newly created Products and Variants are initially inactive and become active only after each required Accurate/Mayar mapping succeeds; failure attempts recovery or cleanup instead of presenting an ordinary active catalog item.

The section makes operational truth inspectable without claiming ownership of it: availability is read-only Inventory data, historical order use prevents deletion, Payment policy has versioned evidence, Shopify mappings are explicit and exact, and visible history names the actor and time. This is meaningful merchant agency and trust architecture. It is not yet product intelligence: there is no current Product performance/profit dashboard, recommendation engine, sourcing/network catalog, bulk import, or downstream outcome analysis here.

## 4. Scope & Architecture Map

`Product` is the merchant/workspace library identity; `Variant` is the execution identity consumed by Inventory, orders, external shipping/local pickup, payment snapshots, and channel mappings. `ProductStore` makes store usage explicit without giving a store ownership of the Product. Products owns catalog lifecycle and mapping meaning; Inventory owns stock truth, Orders owns order lifecycle, Finance owns ledger truth, Commerce owns channel connectivity, and Advertising owns advertising-link lifecycle.

Merchant routes are `/merchant/products`, `/merchant/products/create`, `/merchant/products/edit`, `/merchant/products/variant-edit`, and `/merchant/product-detail`. Product APIs are guarded under `/products`; product connection health is composed at `/merchant/products/:productStoreLinkId/connection-health`.

## 5. Current Capability Inventory

| Capability | Status | What current code supports |
|---|---|---|
| Scoped catalog list | LIVE | workspace/store scope, pagination, search by Product/Variant/SKU/code, sort, taxonomy filter, exact-store Shopify health filter |
| Product and Variant creation | LIVE, provider-dependent | local inactive foundation, required provider mapping, activation only on complete success |
| Test Product eligibility | LIVE | merchant create/edit flag, default off; downstream Manual Test Order eligibility only |
| Catalog editing | LIVE | safe Product/default-Variant fields; Variant edits sync provider before local save |
| Product/Variant images | LIVE | bounded local Product images and Wossol-owned staged Variant image storage; auditable association changes |
| Taxonomy | LIVE | active global categories and historical/superseded assignment evidence |
| Payment policy | LIVE | mandatory COD; paired optional electronic methods; versioned effective periods and audit/events |
| Read-only availability | LIVE when authoritative data exists | effective availability from Inventory reservation projection; user-triggered Inventory refresh |
| Shopify relationship | LIVE when authorized/connected | create unpublished draft, explicit Product/Variant linking, bounded selection, exact mappings, safe reconciliation |
| Advertising relationship | LIVE when authorized/eligible | Product and Variant mappings with active/historical lifecycle and explicit correction/relink controls |
| History | LIVE | product/variant and channel actions rendered as read-only audit history |
| Deletion | LIVE but guarded | archive rather than hard-delete; order history and provider-deletability gate the action |
| Product intelligence/marketplace | NOT IMPLEMENTED | no performance, profit, demand, sourcing, opportunity, or recommendation surface found |

## 6. Workflow & Lifecycle

1. An authorized merchant chooses an active Store in an active Workspace and creates a Product with at least one Variant. The merchant may mark it as a Test Product; that flag is for future Manual Test Order eligibility only.
2. Product, Variant(s), taxonomy assignment, Store association, and an audit foundation are written inactive in a serializable transaction.
3. Each Variant is linked to Accurate/Mayar. Only complete mapping activates the Product/Variants and creates initial versioned payment-policy evidence.
4. A failed create compensates newly created provider records where possible; otherwise the inactive foundation is retained only for audited reconciliation. Failed Variant creation is likewise archived/compensated.
5. Merchants can safely edit catalog fields, images, variants, the Test Product flag, optional payment settings, and explicitly managed channel mappings. Provider update is attempted before local Variant changes are saved.
6. Availability can be read or refreshed but not edited in Products. Other domains consume active exact Variants.
7. Product/Variant deletion first rejects historical order use, then requires successful provider deletion/deletability; local records are archived with audit evidence.

## 7. Value Recipient Map

| Recipient | Current value |
|---|---|
| Merchant operator | one scoped catalog view, clear variant-level identity, media and channel-reference context |
| Merchant owner/manager | controlled delegated permissions, history, product payment settings, safe destructive boundaries |
| Downstream operations | stable Product/Variant identifiers, active-state gating, Store/Workspace ownership, mapping evidence |
| Customer-facing channels | explicit Shopify Product/Variant identity and selected Wossol-owned media transfer |
| Support | Product context can be referenced; Product Detail links to a pre-scoped Product/Inventory support request |
| Merchant demand-validation operator | Test Products can be selected for eligible Manual Test Orders under Orders rules; this does not rewrite commercial history or stock truth |

## 8. Control & Merchant Agency

Agency is real where it matters: merchants select the scoped Store, manage catalog facts, decide when to create a Shopify draft or link an existing Shopify Product, select both ends of Variant links explicitly, and can correct/relink advertising relationships with confirmation. Permissions are enforced server-side; a Product-only user does not receive Commerce existence hints.

Agency is bounded deliberately: Products cannot alter stock, orders, finance, provider credentials, or internal provider mapping/recovery mechanics. Those boundaries prevent a catalog screen from becoming an unsafe operations console.

## 9. Transparency & Trust

- Product Detail shows canonical category, Product/Variant codes, Store context, availability only when known, payment-policy status, landing references, connection state, and actor/time history.
- Product creation is not falsely represented as complete while required external mapping is unresolved.
- Shopify links are exact, manually confirmable, and do not infer mapping from names/SKUs; change/end actions state their effect and preserve/reconcile evidence.
- Product and Variant deletion refuses to erase records with Order history and archives rather than hard-deletes successful removals.
- Payment-policy changes close the prior effective record, create a versioned replacement, record audit evidence, and publish a domain event.

The UI intentionally hides raw provider names, identifiers, credentials, and recovery details. That protects merchant-safe explanation, but makes the internal Accurate/Mayar dependency less visible; the generic attention indicator is a trade-off, not evidence of full self-service recovery.

## 10. Merchant Value Extraction

The immediate merchant value is operational confidence: a Product only becomes ordinary catalog inventory after the execution identity is mapped, a Variant cannot be silently detached from its channel, and old order evidence is protected. The list remains fast and readable rather than turning into an ERP dashboard. Product/Variant codes, taxonomy, images, prices/weights, landing pages, and Store scope reduce ambiguity across later workflows.

## 11. Feature Clusters

1. **Operationally safe catalog creation:** inactive foundation + provider mapping + compensation + activation + audit. This is more valuable than a basic create form because it avoids a locally visible but operationally unusable SKU.
2. **Exact channel identity:** Store-scoped Shopify Product/Variant mappings + explicit selector + opaque correlation recovery + media evidence. The capability is not merely an integration badge.
3. **Merchant-safe product truth:** taxonomy + read-only availability + payment-policy versioning + history + immutable order guard. Together these make Product data more dependable downstream.
4. **Bounded demand-validation flag:** Test Product is a small but meaningful Product/Orders control. It exposes eligibility for future Manual Test Orders while preserving separation from historical Order purpose, stock, and commercial Analytics.

## 12. Merchant Journey / Old Way vs Wossol Way

| Stage | Old way / common risk | Current Wossol way |
|---|---|---|
| Set up | catalog and provider records can diverge | activation waits for required mapping success |
| Operate | product facts, stock and channel links blur together | Product edits catalog facts; Inventory/channel systems retain their own authority |
| Monitor | operators infer whether a SKU is connected | scoped connection status and only merchant-safe attention are visible |
| Intervene | map by memory or copied IDs | exact Product/Variant selection with explicit confirmation |
| Recover | retry might create duplicate channel records | provider/channel operation state and reconciliation fence uncertain outcomes |
| Understand | historical edits and payment treatment are opaque | read-only audit history and versioned payment-policy evidence |

## 13. Hidden / Non-Obvious Advantages

- Provider creation is not a fire-and-forget side effect: the local operational state is gated, failures compensate where possible, and recovery state distinguishes safe retry from review-required uncertainty.
- Exact channel correlation and media reconciliation avoid guessing or duplicate provider media after an uncertain external outcome.
- The Product view never fabricates a zero quantity; effective availability is composed from persisted Inventory truth and reservations.
- The Product Detail conditionally adds Commerce landing references only after Commerce authorization, so Product visibility does not become a channel-discovery leak.

## 14. Data & Intelligence Assets

Current durable data includes Product/Variant identity, Store/Workspace scope, category-assignment history, images, prices/weights/SKUs, provider mappings and current recovery state, Inventory provider snapshots/reservations through related models, payment-policy versions, order-item payment snapshots, Shopify mappings/media operations, advertising mappings, and audit events.

This is a credible foundation for later Product-level operational or commercial intelligence because it preserves identity and selected provenance. It is not itself an intelligence engine: no current Product-level delivery, confirmation, return, acquisition, profit, demand, quality, or recommendation projection was found in this section.

## 15. Cross-Section Compound Advantages

- **Products × Inventory:** exact Variants and reserved-availability projection support operationally honest catalog availability without mutable duplicate stock.
- **Products × Orders/Finance:** Product payment-policy versions can be captured against order lines, preserving historical economic context instead of rewriting it. The Test Product flag is checked by Orders for future Manual Test Order eligibility; it does not rewrite historical Order purpose, stock, or commercial Analytics.
- **Products × Shopify:** Wossol Product identity can be carried into a draft channel Product with exact Variant and media evidence.
- **Products × Advertising:** Product/Variant links create a prerequisite identity layer for later attribution, but do not prove attribution or performance today.
- **Products × Support:** a merchant can enter support with the relevant Product context without granting Support ownership of catalog mutation.

## 16. Competitive Analysis

Direct COD competitors commonly expose catalog/store integrations and basic operational visibility. The competitive master does not verify equivalent depth for merchant-controlled Product/Variant/channel identity, safe reconciliation, or historical payment-policy evidence. That is an absence of evidence about competitors, not a superiority claim.

Wossol is currently at least table stakes on catalog and channel linkage. Its stronger candidate is **trustworthy product execution identity**: exact Variants, guarded activation, scoped relationships, explicit mapping, and auditability. That is harder to copy than a catalog table, but its commercial differentiation remains unproven until merchants experience it and the broader order/delivery/finance chain is validated.

## 17. Marketing Intelligence

**Asset ID:** PROD-01  
**Capability:** Operationally ready catalog creation  
**Evidence IDs:** EV-PROD-005, EV-PROD-007, EV-PROD-014  
**Evidence Status:** GREEN for the guarded lifecycle; YELLOW for reliability outcomes in production.  
**Target Recipient:** merchant operator entering a local delivery/COD workflow.  
**Merchant Problem:** a catalog item that looks created but cannot safely execute downstream.  
**Functional Value:** active Product/Variants are gated on required mapping; failures are not presented as routine success.  
**Marketing Angle:** “Build a catalog that is ready for operations, not just saved.”  
**Proof Point:** inactive foundation, mapping gate, compensation/reconciliation logic, audit.  
**Claim Eligibility:** qualified until live reliability evidence exists.

**Asset ID:** PROD-02  
**Capability:** Exact channel mapping with visible completeness  
**Evidence IDs:** EV-PROD-003, EV-PROD-009, EV-PROD-012  
**Evidence Status:** GREEN for implemented Shopify controls where permissions/connection exist.  
**Marketing Angle:** “Know which exact variants are connected—without guessing from names.”  
**Caveat:** current implementation is Shopify-specific; no general multi-channel claim.

## 18. Surprise Findings

The strongest finding is not the UI: it is the refusal to treat local catalog persistence as operational completion. A second unexpected strength is the fidelity of channel/media recovery: uncertain external writes are fenced for exact reconciliation rather than blindly retried or guessed.

## 19. Potential Category Reframes

Current defensible framing: **operational product control** or **a catalog built for execution**. Avoid “product intelligence,” “winning-product discovery,” “profit optimization,” “unified commerce graph,” or “automatic omnichannel sync”; the necessary downstream products or outcomes are not established here.

## 20. Brand Evidence

- **Control:** merchants explicitly control catalog facts and channel-link decisions within scoped permissions.
- **Transparency:** the system exposes Store context, connection completeness, availability only when known, and historical actions.
- **Accountability:** meaningful catalog, payment, provider, and channel events carry actor/role/time evidence.
- **Reliability:** activation gates, compensations, idempotency/correlation evidence, and archive-over-erasure show a reliability orientation.

These are current product qualities, not a final brand promise.

## 21. Weaknesses / Risks / Gaps

1. **Provider dependency:** Accurate/Mayar mapping is required for activation, while the merchant receives only a generic safe status rather than self-service technical explanation. Production reliability is unverified. 
2. **Store-mapping contract gap:** P1 verifies one Store association at creation and scoped Store reads. The P3 UI contract describes All Stores creation requiring at least one selected Store and optional broader mapping management, but a general merchant Store-mapping mutation flow was not verified. Preserve this as unresolved intent-versus-executable scope.
3. **No Product commercial intelligence:** no Product performance, profitability, demand, delivery/return, or product-opportunity insight is present.
4. **No bulk import/network catalog/sourcing:** current Product creation is merchant-entered and provider-dependent.
5. **Channel concentration:** deep current channel control is Shopify-specific; Advertising mapping is a relationship layer, not outcome attribution.
6. **Availability caveat:** displayed quantity is a read-only projection; its freshness and provider truth depend on Inventory sync, not Product itself.
7. **Documentation drift risk:** the dated architecture-reading summary states Product create/edit/delete and real provider sync were closed, while P1 code implements them. It is P4 historical context and cannot support a current-capability conclusion.

## 22. Future Strategic Potential

| Category | Assessment |
|---|---|
| Current foundation | exact Variant identity, provenance, payment snapshots, channel links, availability and audit evidence |
| Approved/implemented extension | Shopify draft/mapping/media workflow and advertising mapping lifecycle |
| Inferred potential | connect Product × acquisition × confirmation × delivery × return × cost/profit into qualified product intelligence |
| Strategic relevance | could support product opportunity, stock-aware promotion, safe Product-level profitability and merchant guidance |
| Brand relevance | evidence could eventually support “understand what to sell and why”; it does not support that claim now |

## 23. Claim Safety

| Claim | Safety | Reason |
|---|---|---|
| Manage products and variants in a scoped merchant workspace/store | GREEN | implemented UI/API/schema |
| Mark a Product for future Manual Test Order eligibility | GREEN, qualified | Product create/edit and Orders eligibility checks are implemented; the flag does not change historical Order purpose, stock, or commercial Analytics |
| Keep stock read-only while showing known availability | GREEN | current Product/Inventory boundary |
| Create an unpublished Shopify draft and explicitly map variants | GREEN, qualified to eligible connected stores | implemented but connectivity/permissions are prerequisites |
| Product creation is operationally safe | YELLOW | strong code evidence, but no live reliability/SLA evidence |
| Automatically synchronize every sales channel | RED | only Shopify mapping flow is established |
| Product performance/profit intelligence | RED | not implemented in Products |
| Find winning products / source products from a network | RED | not implemented in Products |

## 24. Commercial Magnitude

**High operational magnitude.** Catalog identity is a prerequisite for every sellable order, stock check, channel linkage, payment-policy snapshot, and support conversation. The section is primarily access/operating-system infrastructure today. Its direct selling appeal is strongest for merchants who need a dependable local-commerce catalog; its differentiated commercial upside depends on future validated product outcomes, not its present data model alone.

## 25. Strategic Classification

| Area | Classification |
|---|---|
| Product/Variant catalog, images, categories | TABLE STAKES |
| Store-scoped channel linkage | PARITY / MUST MATCH |
| Explicit Variant links and safe mapping recovery | WOSSOL STRONGER / POTENTIAL DIFFERENTIATOR |
| Gated activation, compensation, archive and audit | POTENTIAL DIFFERENTIATOR |
| Product performance/profit/opportunity intelligence | WHITESPACE / FUTURE |
| Bulk import, sourcing/network catalog | WOSSOL WEAKER / not established |

## 26. Action Register

| Priority | Action | Rationale |
|---|---|---|
| MUST FIX | Add merchant-safe recovery guidance when a Product has a generic operational attention state, without exposing credentials or raw provider mechanics. | Current UI reveals issue existence but limited resolution path. |
| MUST MATCH | Establish a safe bulk/import or assisted catalog-onboarding path if target merchants arrive with existing catalogs. | Manual single-Product creation creates adoption friction. |
| MUST BEAT | Join existing Product identity to verified downstream outcome projections before presenting Product intelligence. | This turns good data foundations into merchant decisions. |
| MUST MATCH | Resolve the Final V1 Store-mapping contract against the executable ProductStore lifecycle; implement or explicitly narrow the approved behavior before marketing multi-Store management. | P3 intent is broader than targeted P1 evidence. |
| WORTH ADOPTING | Make Test Product eligibility and its Manual Test Order-only boundary clear in merchant education. | The capability is useful for demand validation but unsafe to imply as commercial analytics or stock bypass. |
| DO NOT COPY | Do not present raw historical confirmation/delivery rates as universal product promises. | Competitive master identifies context and trust risk. |
| POTENTIAL MOAT | Preserve exact cross-domain provenance and historical policy/mapping evidence as the outcome graph expands. | History is slower to recreate than a catalog UI. |

## 27. Evidence Register

**EV-PROD-001 — Merchant catalog UI**  
**Type:** P1. **Path:** `apps/frontend/src/app/merchant/products/page.tsx`.  
**Observed:** bounded Workspace/Store list, pagination, search, category/Shopify filters, read-only quantity, safe attention badge, guarded delete actions and Inventory refresh. **Confidence:** High.

**EV-PROD-002 — Product creation/edit/variant UI**  
**Type:** P1. **Paths:** `apps/frontend/src/app/merchant/products/create/page.tsx`, `edit/page.tsx`, `variant-edit/page.tsx`, `ProductImageUploadSection.tsx`.  
**Observed:** Product form, category/payment/image fields, staged Variant image flow, and separate edit controls. **Confidence:** High.

**EV-PROD-003 — Product Detail and connections UI**  
**Type:** P1. **Paths:** `apps/frontend/src/app/merchant/product-detail/page.tsx`, `products/ProductConnections.tsx`, `products/ProductAdvertisingMappings.tsx`.  
**Observed:** read-only overview/variants/payment/connections/history; explicit Shopify and Advertising relationship controls with confirmation and visible historical mappings. **Confidence:** High.

**EV-PROD-004 — Client data boundary**  
**Type:** P1. **Path:** `apps/frontend/src/app/merchant/product-data.ts`.  
**Observed:** separate bounded product-management reader and legacy reader; merchant-safe connection health and scoped Shopify endpoints. **Confidence:** High.

**EV-PROD-005 — Authorization and read projections**  
**Type:** P1. **Paths:** `merchant-products-list.controller.ts`, `merchant-products-detail.controller.ts`, `merchant-products-edit.controller.ts`, `merchant-products-variants.controller.ts`, `products.controller.ts`.  
**Observed:** access token guards, active merchant/workspace/store checks, scoped permission checks, inventory read-only boundaries, audit history. **Confidence:** High.

**EV-PROD-006 — Safe activation and payment policy**  
**Type:** P1. **Path:** `products.service.ts` (`createMerchantProduct`, `finalizeMerchantProductCreate`, payment-policy methods).  
**Observed:** inactive foundation; activate only after mappings; serializable policy versioning, mandatory COD, paired electronic settings and domain events. **Confidence:** High.

**EV-PROD-007 — Provider recovery and compensation**  
**Type:** P1. **Path:** `products.service.ts` (provider retry, create/variant finalize and compensation methods).  
**Observed:** safe retry/action-required distinction, compensation and archival logic, audit trails. **Confidence:** High; live external behavior unverified.

**EV-PROD-008 — Immutable history/deletion guard**  
**Type:** P1. **Path:** `products.service.ts` (`deleteMerchantVariant`, `deleteMerchantProduct`).  
**Observed:** historical Order items block deletion before provider mutation; success archives local entities with audit, rather than hard-deletes. **Confidence:** High.

**EV-PROD-009 — Shopify exact mapping and media reconciliation**  
**Type:** P1. **Path:** `apps/backend/src/modules/shopify/shopify-product.service.ts`.  
**Observed:** draft creation, opaque correlation, exact mapping, missing-Variant synchronization, explicit reconciliation fence for uncertain media calls. **Confidence:** High; provider runtime unverified.

**EV-PROD-010 — Connection health isolation**  
**Type:** P1. **Paths:** `product-connection-health.service.ts`, `advertising-product-connection-health-projection.service.ts`, `product-provider-connection-health-projection.service.ts`.  
**Observed:** Commerce/Advertising visibility is permission-gated; unavailable data is not fabricated; Product attention remains merchant-safe. **Confidence:** High.

**EV-PROD-011 — Durable data model**  
**Type:** P1. **Path:** `apps/backend/prisma/schema.prisma` Product/Variant/ProductPaymentPolicy/ProductCategoryAssignment/ProductStore/Commerce mappings/VariantProviderSyncState models.  
**Observed:** scoped identity, versioned policy, category history, exact external mappings and recovery records. **Confidence:** High.

**EV-PROD-012 — Channel permissions and product health endpoint**  
**Type:** P1. **Paths:** `permission-catalog.ts`, `product-connection-health.controller.ts`.  
**Observed:** distinct Products, Commerce and Advertising permissions; connection read requires Products view and then conditionally exposes domain state. **Confidence:** High.

**EV-PROD-013 — Product/Inventory/Orders boundaries**
**Type:** P1/P4. **Paths:** `product-read-projection.service.ts`, schema relationships; `docs/architecture-reading/PRODUCT_VARIANT_READING_SUMMARY.md`.
**Observed:** Variants are the execution identity and Product reads do not mutate stock; the dated summary supports ownership concepts only and is stale on implementation status. **Confidence:** High for P1; low for P4 current-status claims.

**EV-PROD-014 — Verification**  
**Type:** P1 test evidence. **Commands:** focused backend Products lifecycle/recovery specs; focused frontend Products UI specs; `pnpm --filter @wossol/backend typecheck`; `pnpm --filter @wossol/frontend typecheck`.  
**Observed:** commands passed. One combined frontend test command initially used the wrong working directory for `product-advertising-mappings.spec.ts`; rerun from repository root passed all 8 assertions. **Confidence:** High for static checks; no runtime/provider claim.

**EV-PROD-015 — Specification comparison**
**Type:** P3 and P4. **Paths:** `docs/ui/merchant/MERCHANT_PRODUCTS_UI_SPEC.md`; dated `PRODUCT_VARIANT_READING_SUMMARY.md`.
**Observed:** the Final V1 UI specification is approved Product intent/contract evidence, while the dated architecture summary is stale supporting context. P1 establishes the reviewed executable behavior; neither document may be silently treated as superseded where a material contract conflict remains. **Confidence:** High for document classification; contract resolution remains open where noted below.

**EV-PROD-016 — Test Product executable setting**
**Type:** P1. **Paths:** `apps/frontend/src/app/merchant/products/create/page.tsx`, `edit/page.tsx`; `apps/backend/src/modules/products/products.service.ts`, `merchant-products-edit.controller.ts`, `merchant-products-list.controller.ts`, `merchant-products-detail.controller.ts`; `apps/backend/prisma/schema.prisma`.
**Observed:** `isTestProduct` defaults false, is exposed on create/edit, is persisted and returned, and is shown as Test-enabled in the list. Existing Product permissions govern the mutation; no separate dedicated Test Product permission was verified. **Capability status:** LIVE. **Confidence:** High.

**EV-PROD-017 — Test Product downstream eligibility**
**Type:** P1/P2. **Paths:** `apps/backend/src/modules/orders/orders.service.ts`; `apps/backend/src/modules/orders/orders.orderable-product-picker.spec.ts`.
**Observed:** Orders selects Test Products only for Test purpose with known zero effective availability, rejects real orders for Test Products, rejects Test Orders for non-Test Products, and preserves unknown/positive stock boundaries. This setting does not rewrite historical Order purpose, stock, or commercial Analytics. **Capability status:** LIVE. **Confidence:** High.

**EV-PROD-018 — Store-mapping contract versus implementation**
**Type:** P3 versus P1. **Paths:** `docs/ui/merchant/MERCHANT_PRODUCTS_UI_SPEC.md` sections 8, 13, 18, 27, 31; `merchant-products-list.controller.ts`, `products.service.ts`, `ProductStore` schema.
**Observed:** the Final V1 contract describes All Stores creation requiring at least one selected Store and optional Store-mapping management where supported. P1 verifies scoped Store context, All Stores reads, and one ProductStore association during creation; a general merchant Store-mapping mutation flow was not found after targeted search. **Capability status:** PARTIAL / contract unresolved. **Confidence:** High for the identified boundary; no inference that broader mapping is absent from the entire platform.

## 28. Contradictions & Uncertainty

1. **CONTRADICTION-PROD-001 — Documentation status:** the dated architecture-reading summary describes Product create/edit/delete, real provider sync, and image storage as closed future work. P1 code and focused tests show these are implemented. The summary is P4 historical context, not current capability evidence.
2. **CONTRADICTION-PROD-002 — Contract versus executable Product UI:** the Final V1 UI specification is P3 approved intent; P1 establishes what the reviewed build executes. Where they differ materially, both remain recorded as executable truth versus unresolved contract intent. P1 does not by itself prove that the Final V1 contract was superseded.
3. **CONTRADICTION-PROD-003 — Store mapping:** P3 describes broader All Stores/Store-mapping behavior, while targeted P1 evidence verifies one creation association and scoped reads but not a general merchant mapping mutation flow. This remains PARTIAL / unresolved and is not resolved by inference.
4. **Test Product alignment:** P3 says the flag controls future Manual Test Order eligibility only. P1 confirms create/edit persistence and Orders eligibility checks; no evidence indicates it changes historical Order purpose, stock, or commercial Analytics.
5. No live provider, database, production deployment, or merchant session was inspected. “LIVE” in this audit means code implemented and covered by static checks, not proven production availability.
6. Product connection health is a projection: restricted/unavailable states must not be read as disconnected, and a generic attention label must not be read as a merchant-remediable provider diagnosis.

## 29. Open Questions

1. What production reliability, latency, and recovery outcomes does Accurate/Mayar creation actually achieve?
2. Is the Final V1 Store-mapping contract intended to include broader merchant mapping mutations, and if so which P1 route/service is authoritative?
3. Does existing Product create/edit authorization intentionally cover Test Product eligibility, or is a narrower permission required?
4. Which outcome data is accurate enough to attach to Products without misleading merchants?
5. What merchant-safe remediation should appear for provider attention without exposing internal system details?
6. Which non-Shopify commerce channels are approved for equivalent exact mapping support?

## 30. Methodology Learnings

No reusable methodology change identified. This correction applies the existing evidence hierarchy and contradiction protocol: P1 establishes executable truth; P3 preserves approved contract intent; P4 cannot be used as current capability evidence. The issue was application, not a methodology gap.

## 31. Retroactive Review Impact

No methodology change; no retroactive queue entry. The dated Product/Variant architecture summary does not itself justify re-auditing completed sections. Connected-domain findings should be considered when those domains are independently audited.

## 32. Canonical Section Takeaway

**Products is a controlled execution-identity system: it gives merchants a simple catalog surface while requiring exact Variant identity, scoped ownership, external mapping, history, and payment-policy evidence before the catalog can safely drive commerce. It also carries a bounded Test Product flag for future Manual Test Order eligibility. Its present value is trustworthy control, not product intelligence; broader Store-mapping behavior remains an explicit contract-versus-executable question.**
