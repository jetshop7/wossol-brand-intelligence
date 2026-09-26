# Stores — Section Intelligence

## 1. Audit Metadata

- **Audit date:** 2026-09-26.
- **Methodology:** `00-methodology/MASTER_INSTRUCTIONS.md` v1.1; `00-methodology/CODEX_OPERATING_PROTOCOL.md` v1.0.
- **Competitive reference:** `01-competitive/WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md` v1.0 (2026-09-09).
- **Intelligence source:** `jetshop7/wossol-brand-intelligence`, `main`, `5937d9b4915e0a6fe46dea78cca958f85f00293f` at audit start, clean and synchronized with `origin/main`.
- **Product source at final verification:** `jetshop7/wossol-platform`, `dev/wossol-integration`, `de2bb9bad7c39ce8f6ccf2d237a36cea1a82fb98`, clean and equal to the local `origin/dev/wossol-integration` ref. It was inspected read-only.
- **Source-state note:** The product checkout was initially at `db2acf042c539f7784585b241c26fd3b34a3cf19` with three local Shopify offer-preview files modified. During this audit it advanced to `de2bb9b` and became clean. The only diff between those commits is four Shopify offer-preview/runtime/test files; Store schema, Store services, Settings, Shell, and Store specs/UI specifications are unchanged. No Product files were modified by this audit.
- **Evidence standard:** P1 executable source/schema, P2 targeted tests/typechecks, P3 current UI/system specifications, P4 historical design material qualified as such. Production deployment/data and merchant outcomes are not inferred from repository inspection.

## 2. Audit Coverage Map

| Surface | Coverage | Evidence |
|---|---|---|
| Store identity and schema | Store fields, lifecycle, Workspace/Merchant relationships, domain links | EV-STORE-001–002 |
| Merchant Store management API | list/create/edit/disable/reactivate and scope checks | EV-STORE-003–005 |
| Store access and roles | Owner/Admin management, Staff Store grants, workspace/section boundaries | EV-STORE-006–007 |
| Merchant Shell context | Workspace selection, active Store selection, All Stores, persistence | EV-STORE-008 |
| Store Settings UI | Workspace-scoped list, create/edit, errors, status and logo flow | EV-STORE-009–011 |
| Logo storage | size/type validation, private path, replacement/removal | EV-STORE-012 |
| Connected domains | Products/Orders/Inventory/Delivery/Finance/Commerce/Ads Store relationships | EV-STORE-013–015 |
| Specifications and history | Current Store Management and Settings specs; historical Store-source architecture | EV-STORE-016–017 |
| Verification | focused backend/frontend suites and typechecks | EV-STORE-018 |
| Competition | stable competitive master, not a new competitor audit | EV-STORE-019 |

Not verified: production deployment/database state, real tenant/user records, live cloud object-storage configuration (the inspected implementation writes private local files), business usage or store-level outcome differences, and current competitor Store-entity designs first-hand.

## 3. Executive Section Truth

In the inspected product, a **Store is a Wossol Merchant-owned, Workspace-bound operating identity and scope**, not necessarily an external e-commerce storefront. It carries a generated immutable Store Code, name, optional URL, status, and optional private logo. Workspace supplies market/currency context; Store identity scopes relevant products, orders, pricing, provider connections, and other domain records. External commerce identity and credentials live separately in Commerce connections.

Merchant Owner and Admin can create and manage Stores from Settings within one authorized active Workspace. They can edit name/URL, manage a private logo, disable, and reactivate; there is no delete or cross-Workspace transfer. Staff has no Store mutation authority; their operating Store visibility is controlled by Store access in addition to Merchant, Workspace, and section access. The Shell lists active Stores only, and All Stores means the active/authorized Store cohort within the current Workspace—not every Store across the Merchant’s markets.

This is meaningful **tenant and operating-scope infrastructure**, with an understandable self-service lifecycle and strong access-boundary patterns. It is not itself a differentiated commerce feature. Current implementation has two notable workflow risks: create-time URL input lacks the backend validation applied during edit, and Store creation plus optional logo upload is a two-request flow that can leave a persisted Store behind when logo upload fails, making an unguarded retry create another Store.

## 4. Scope & Architecture Map

Merchant Settings → authenticated Merchant Portal Store endpoints → actor’s current Merchant and Workspace access → Store model/status → Shell context projection and domain-specific Store scope. `Store` relates to Workspace and Merchant, while dependent records use Store identity. `CommerceConnection` attaches provider-specific account identity and credentials to a Store; it does not redefine Store. Store disable/reactivate is an audited state transition rather than a delete.

| Layer | Current role |
|---|---|
| Workspace | Market, currency and operating context; the Store Settings API requires an active Workspace. |
| Store | Provider-neutral Merchant sales/operating identity; stable internal ID and Store Code, mutable name/URL, ACTIVE/DISABLED lifecycle. |
| Merchant Portal | Owner/Admin management; each request derives actor/Merchant context from the authenticated user and validates workspace/store boundaries. |
| Merchant Shell | Projects authorized active Stores for the selected Workspace; persists workspace choice in session storage and Store choice in local storage, then revalidates choices against context. |
| Store access | Staff Store restriction layered under active Merchant + Workspace membership; it does not grant section or Workspace authority. |
| Domain owners | Products, Orders, delivery pricing, Commerce and other domains retain their own data/behavior and enforce Store scope where applicable. |
| Store URL/logo | Descriptive optional fields, not provider identity or public authorization. Logo bytes are served through authenticated private endpoints. |

## 5. Current Capability Inventory

| Capability | Status | Control depth | Merchant consequence |
|---|---|---:|---|
| Merchant/Workspace-bound Store identity | LIVE | 1 — Identity/scope | Gives owner domains a stable Merchant Store key; Workspace provides market context. |
| Owner/Admin create and list | LIVE | 2 — Configuration | Self-service setup in the selected authorized Workspace; Store Code is generated by backend. |
| Basic name and URL edit | LIVE | 2 — Configuration | Allows bounded identity maintenance without changing Store identity/scope. |
| Disable/reactivate | LIVE | 2 — Lifecycle control | Stops an active Store from normal active Store context; retains Store and linked history rather than deleting it. |
| Staff Store access | LIVE | 3 — Scoped access | Staff with selected access sees only the explicitly granted active Store cohort; Store grants do not override other gates. |
| Shell Store context and All Stores | LIVE | 1 — Visibility/context | Switches Store-owned views inside a selected Workspace; All Stores is Workspace-limited. |
| Private Store logo | LIVE | 2 — Identity presentation | Provides optional private identity media with file type/size bounds and authenticated retrieval. |
| Store URL validation | PARTIAL | n/a | Edit path enforces HTTP(S)/length; create path relies on client-side `type=url` and lacks equivalent server validation. |
| Atomic create plus logo | NOT IMPLEMENTED | n/a | Store persists before separate optional logo request; upload failure can leave a partial success and invite duplicate retry. |
| Store deletion/Workspace transfer | NOT AVAILABLE | n/a | V1 preserves ownership/scope history; correction requires disable/reactivate or a future governed transfer path. |

## 6. Workflow & Lifecycle

1. Merchant selects an authorized active Workspace in the Shell.
2. Owner/Admin opens Settings → Stores; list query requires that Workspace and returns the authorized cohort. No all-Workspace management view exists.
3. Create submits Store name, optional URL and Workspace code. Backend revalidates the actor’s active Merchant/Workspace membership, generates a Store Code, creates an ACTIVE Store and writes an audit event. If a logo was selected, the frontend uploads it in a separate request after creation.
4. Edit changes name and URL only; workspace, Merchant and Store Code are immutable. Blank URL clears it.
5. Logo upload/change/removal uses a Store-scoped authenticated endpoint and Audit record; image file bytes are separate from Store metadata.
6. Disable/reactivate changes ACTIVE ↔ DISABLED with a guarded transaction and Audit record. Disabled Stores remain visible to managers in Settings but do not enter Shell’s active Store context.
7. Domain screens use the selected Store, or a bounded All Stores projection, according to their own ownership and permission rules. Re-opening a session does not make a stale arbitrary Store ID authoritative; Shell chooses from the current authorized Store list.

## 7. Value Recipient Map

- **Merchant owner/admin:** can configure operating Store identities without asking platform operations to create every Store; can retain a clean Store Code and audit trail while updating display metadata.
- **Merchant staff:** receive only the authorized active Store context consistent with their Store grant; cannot create, rename, disable, or change logo.
- **Operations/support:** Store identity provides a stable scoping key for order and other domain records, reducing accidental cross-Store handling.
- **Customer:** may indirectly see Store-specific product/COD presentation and delivery-price policy; Store Settings itself is not a customer storefront.
- **Wossol:** Store-level identity provides an organizing layer for multi-market/multi-store operation without putting provider-specific fields in the core Store model.

## 8. Control & Merchant Agency

Owner/Admin control basic Store metadata, logo and active/disabled state; they do not control Store Code, Merchant ownership, Workspace assignment or deletion. Staff Store selection is separately managed through Team access. A `SELECTED` Staff grant is narrower than `ALL_ACTIVE`; Store authorization does not by itself grant a Workspace or section permission. Provider connection, Product/Variant mapping, delivery price, Order and financial actions remain governed by their owner services, not by hidden Store Settings authority.

Disabling is reversible but operationally consequential: the Store is omitted from the active Shell context, and active-only access checks/queries prevent ordinary new operations against it. The audit did not find a merchant-facing historical-archive mode that guarantees all linked past records remain conveniently browseable once disabled; record retention must not be equated with historical visibility.

## 9. Transparency & Trust

The product exposes stable Store Code and Workspace, distinguishes ACTIVE/DISABLED, and records create/edit/status/logo actions as audit events. The Shell labels All Stores with the active Workspace name; this helps avoid implying a cross-market aggregate. Private logo retrieval does not expose a storage key or public URL. Backend checks are authoritative even when UI controls are hidden.

Trust is weakened by the create/edit URL validation mismatch and by the multi-request create/logo behavior. The optional URL is descriptive; it is not verified ownership, a trusted external commerce account, or a security boundary. Provider account verification and credentials belong to CommerceConnection. No operational SLA, version history for Store metadata, or recovery dashboard was established.

## 10. Merchant Value Extraction

Store management reduces setup friction and creates a durable scope boundary as a Merchant operates multiple sales identities or markets. Explicit Workspace confinement limits accidental cross-market setup; immutable Store Code and history-preserving disable reduce identity churn. The main value is **safer organization and scoping**, not direct demand, conversion, profit, or market intelligence. No measured setup-time, error-rate, or operational outcome improvement was available.

## 11. Feature Clusters

1. **Stable identity:** internal ID + generated immutable Store Code + Merchant/Workspace ownership.
2. **Agency with guardrails:** Owner/Admin CRUD-lite controls while Store Code, Merchant and Workspace stay fixed.
3. **Scoped operations:** Shell selection + Store access + domain-specific authorization/query predicates.
4. **Store-specific experience:** logo/URL and Store-specific product/COD/delivery-price settings, each still governed by its domain.
5. **History-preserving lifecycle:** disable/reactivate and audit rather than destructive deletion.

The strongest cluster is the combination of tenant identity, authorized context and domain-owned scope. A Store selector alone is not a security model; the backend services provide the meaningful boundary.

## 12. Merchant Journey / Old Way vs Wossol Way

**Setup:** an owner/admin creates a Store under the selected Workspace and can immediately use it as a stable context. No separate cross-Workspace setup wizard or provider connection is implied.

**Operate:** user switches among active Stores in that Workspace, or uses All Stores when available. Individual screens may require one concrete Store for mutation/configuration. Staff sees only authorized context and remains subject to section access.

**Intervene/recover:** owner/admin can rename, correct URL, disable, reactivate or change logo; action history is written. Store is not deleted, and linked history is not rewritten. However, if Store creation succeeds and logo upload fails, the UI reports an error without refreshing/closing; retrying the Create flow can create a duplicate. A failed Store URL link may also reach downstream users because create-time backend validation is absent.

Compared with ad hoc names and manual market filters, Wossol’s stable, authorization-aware Store scope is clearer and auditable. No comparative task-time or error reduction has been measured.

## 13. Hidden / Non-Obvious Advantages

- Store is deliberately provider-neutral: integrations attach through CommerceConnection rather than adding external shop identity/credentials to Store.
- Shell revalidates persisted Store selection against the current Workspace’s authorized context, so local browser state is preference, not authority.
- Store scope composes with (but does not replace) Workspace and section permissions.
- Store disable preserves the identity needed to interpret existing linked records; status transition and audit preserve accountability.
- Owner/admin can see disabled Stores in management while operational Shell context is active-only, separating lifecycle administration from normal operation.

## 14. Data & Intelligence Assets

Store ID/Code and Merchant/Workspace links support scoped Products, Orders, delivery-price overrides, commerce connections and other linked records. Audit events preserve key Store-management transitions. Store URL/name/logo are descriptive identity inputs, not verified channel truth. The Store record does not itself capture order outcomes, customers, inventory ownership, provider data, or profit; those are owned by domain records and must keep their provenance.

Cross-Store analytics can become useful only when the consuming domain defines eligible Workspace, Store, authorization and aggregation rules. The All Stores selector does not make unlike markets, stock pools, currencies, providers or fees interchangeable.

## 15. Cross-Section Compound Advantages

- **Products:** ProductStore links scope product availability to Stores; a Product can participate in Store contexts without the Store becoming the Product owner.
- **Orders:** canonical Orders have Store identity; Store scope is applied at creation/list/detail paths by Orders.
- **Inventory:** Workspace/provider-backed stock and Store product visibility are distinct; Store selection must not imply independent physical stock pools.
- **Delivery / Finance:** Store-specific customer delivery-price overrides are separate from platform Fee Profile charges and immutable Order snapshots; Finance remains authoritative for ledger/settlement.
- **Commerce:** Shopify/YouCan connections attach provider-specific accounts to Store scope. Internal Store is not the same thing as external commerce channel.
- **Advertising:** Store-level conversion destination selection can exist without turning Store identity into ad attribution.
- **Team:** Staff Store selection is an additional restriction; Team owns assignment changes, Store management does not grant Staff access.
- **Home / Analytics / Market Center:** aggregate projections must preserve active Workspace and authorized Store scope and must not infer market demand, decision quality, or profitability from Store count/configuration.

## 16. Competitive Analysis

The competitive master lists Store integrations, multi-store/channel support and basic team/user management among common operational infrastructure across several direct competitors. That is adjacent evidence—not proof that competitors model Wossol’s internal Store entity in the same way. Store organization and basic lifecycle are therefore **table stakes / enabling architecture**, not a differentiator by themselves. Wossol’s potential advantage would depend on the depth and reliability of scoped workflows and how Store identity compounds with market operations and intelligence; it is not established by this entity or selector alone. (EV-STORE-019.)

## 17. Marketing Intelligence

**Qualified current message:** “Organize each market operation around a Store in its Workspace, with a stable Store identity and role-aware access.” Supportable only when describing the internal operating model, not as an external-store connector claim.

**Proof points:** generated Store Code; Workspace-bounded settings; role-aware Store access; active/disabled lifecycle; audit history.

**Objection/demo:** show that “All Stores” remains within the selected Workspace and that provider connection is configured separately for one Store. Do not imply Store creation itself connects Shopify/YouCan, partitions physical stock, changes Staff permissions, or aggregates all markets.

Claim safety: GREEN for the scoped management behaviors evidenced in code; YELLOW for broad “multi-store operations” because domain-specific parity and historical access differ; RED for “connect any store,” “complete multi-market view,” or “independent Store inventory” based only on this section.

## 18. Surprise Findings

1. “Store” is an internal provider-neutral operating identity, while Shopify/YouCan shop identity and credentials live in separate CommerceConnection records.
2. The Store selector and Settings list are intentionally different projections: active Stores for operating context, manager-visible disabled Stores for lifecycle management.
3. Store URL is more trusted on edit than on create: the update endpoint validates HTTP/HTTPS and length, but create-time service normalization does not.
4. Optional logo upload is not atomic with Store creation; a second request failure can be mistaken for total create failure.

## 19. Potential Category Reframes

Store is best understood as a **merchant operating scope**—the stable unit through which multiple domain-specific records and permissions can be organized—rather than an e-commerce storefront record. That is a useful architectural interpretation, not a category claim or final positioning.

## 20. Brand Evidence

Potential evidence for Control and Accountability: stable identity, explicit Workspace context, role-specific access, reversible lifecycle, and audited changes. Potential evidence for Clarity: naming Store/Workspace scope in Shell and keeping external provider identity distinct. These are operational proof clusters, not a promise that every Store workflow is frictionless or that the Store entity creates intelligence.

## 21. Weaknesses / Risks / Gaps

- **P1 — create/edit validation inconsistency:** `createStore` only enforces a minimum Store-name length and trims an optional URL; it does not enforce the 160-character name maximum, URL maximum, or HTTP(S) syntax/protocol that `updateStore` enforces. Frontend `type="url"` is not a backend trust boundary, and Store URL is rendered as a link in Settings. Align server-side create/edit validation and safely constrain link rendering.
- **P1 — Partial success/retry:** Store create transaction commits before optional logo upload request. If logo upload fails, UI catches the error before refresh/reset; retry can create another active Store. Make the flow recoverable/idempotent or present the created Store and a separate logo retry state.
- **P2 — Disabled history visibility:** status filtering excludes disabled Stores from Shell/ordinary active queries; records remain but the product does not establish universal historical browsing for a disabled Store. Define/archive UX without weakening active-operation checks.
- **P2 — Store scope labels:** “Store” can be confused with an external storefront or physical location; existing URL does not establish either. Ensure UI/help text preserves the internal operating identity meaning.
- **P2 — No deletion or transfer:** intentional V1 preservation is safer for history, but correction of wrong Merchant/Workspace association lacks a self-service path and requires governed support/admin action.
- **P2 — Metadata history depth:** Audit captures key before/after management changes, but no dedicated Store metadata version/history UI was found.
- **NOT VERIFIED:** production storage durability/backup for private logo files, deployment migrations, production authorization, load/race behavior, usage, and Store-specific outcome improvement.

## 22. Future Strategic Potential

- **Current foundation:** provider-neutral Store scope, contextual selector, constrained manager lifecycle, Store-level joins.
- **Approved future:** none in the inspected Store specifications beyond current lifecycle and Store-scoped domain behavior.
- **Inferred potential:** more visible per-Store health/reconciliation, safe historical archive views, setup validation, and stronger recovery for partial Store creation.
- **Strategic relevance:** Store could support coherent market-specific operating context if domains use consistent identity/aggregation semantics.
- **Brand relevance:** Control/Clarity/Accountability hypotheses only; no durable advantage or moat demonstrated.

## 23. Claim Safety

| Claim | Safety |
|---|---|
| “Owner/Admin can create and manage Stores within an authorized Workspace.” | GREEN — current endpoint/UI/spec and tests support it. |
| “Store settings are scoped to the active Workspace; Store Code is immutable.” | GREEN — backend checks and UI contract support it. |
| “Staff Store scope is additional to Workspace and section access.” | GREEN — access service and tests support layered gates. |
| “All Stores gives a cross-market overview.” | RED — it is Workspace-bounded. |
| “A Wossol Store is an external Shopify/YouCan store.” | RED — provider connection identity is separate. |
| “Each Store has separate physical inventory.” | RED — inventory ownership/stock semantics are domain-specific and may be Workspace/provider-backed. |
| “Disabling deletes/erases the Store.” | RED — lifecycle is status-based; linked identity/history is retained. |
| “Store-level data always remains browsable after disable.” | YELLOW/NOT VERIFIED — persistence is distinct from active-query visibility. |
| “Store setup is atomic, fully validated and cannot duplicate on retry.” | RED — create/URL/logo gaps identified. |

## 24. Commercial Magnitude

**Magnitude: foundational / moderate enabling value.** Stable Workspace-bound Store identity can reduce confusion and support multi-store operations, but its value is indirect and dependent on consistent owner-domain implementation. Realized productivity, error reduction, retention and revenue impact are unmeasured. By itself it is common operational infrastructure rather than a strong paid differentiator.

## 25. Strategic Classification

- **Core entity and basic management:** TABLE STAKES / enabling architecture.
- **Role-aware Workspace/Store scoping:** WOSSOL STRONGER implementation candidate, but comparative strength is not established from current competitor evidence.
- **Cross-domain Store identity:** POTENTIAL DIFFERENTIATOR if consistently used in a coherent merchant workflow; current data model alone is insufficient.
- **Potential moat:** none established; no Store-specific accumulation or network effect demonstrated.
- **Confidence:** high for schema/API/UI behavior; moderate for product-wide downstream completeness; low for live production and competitive-depth comparisons.

## 26. Action Register

| Priority | Action | Recommendation class | Why |
|---|---|---|---|
| P1 | Apply the same server-side Store URL syntax/protocol/length policy during create as during edit; consider safe-link rendering. | MUST FIX | Current create path accepts values the edit path rejects. |
| P1 | Make Store creation and optional logo upload recoverable: surface created Store on logo error, allow retry without another Store, or implement a safe idempotent workflow. | MUST FIX | Avoid duplicate Stores after partial success. |
| P2 | Define how merchants browse retained records for disabled Stores without restoring operational eligibility. | WORTH ADOPTING | Preserves audit/history value while active-only controls remain safe. |
| P2 | Clarify internal Store vs external commerce storefront vs warehouse/location in UI terminology. | WORTH ADOPTING | Avoids identity and scope confusion. |
| P2 | Define a governed correction path for wrong Merchant/Workspace Store ownership if such corrections are operationally needed. | POST-LAUNCH | V1 intentionally forbids transfer/delete. |
| P2 | Verify durable backup/recovery for private logo storage in deployed environments. | MUST MATCH | Repository inspection shows local private-file implementation, not deployment durability. |

## 27. Evidence Register

| ID | Evidence | Tier | Finding supported |
|---|---|---|---|
| EV-STORE-001 | `apps/backend/prisma/schema.prisma`, `Store`, `StoreStatus`, Workspace/Merchant relations | P1 | Store identity fields, status, separate Merchant and Workspace relation; provider identity is not a Store field. |
| EV-STORE-002 | Store relations in Prisma schema: ProductStore, Order, CommerceConnection, StoreDeliveryPriceOverride, StoreLogo and other scoped records | P1 | Store is a cross-domain scope key with domain-owned linked records. |
| EV-STORE-003 | `merchant-portal.controller.ts`, `/merchant/stores` routes | P1 | Authenticated Store list/create/edit/lifecycle/logo endpoints. |
| EV-STORE-004 | `merchant-portal.service.ts`, `createStore`, `getStores`, `updateStore`, `transitionStore`, `resolveActiveStoreWorkspace` | P1 | Merchant/Workspace checks, generated Store Code, update boundaries and active/disabled transitions. |
| EV-STORE-005 | `merchant-store-management.service.spec.ts` | P2 | Owner/Admin capabilities, Staff mutation denial, cross-Merchant/Workspace failure, history/status boundaries. |
| EV-STORE-006 | `merchant-store-access.service.ts` and `.spec.ts` | P1/P2 | Staff Store grants are layered under active Merchant/Workspace; SELECTED and ALL_ACTIVE semantics. |
| EV-STORE-007 | `merchant-portal.service.ts`, `getCurrentMerchantContext`, `replaceStoreAccess` and Team tests | P1/P2 | Shell only receives active authorized Stores; Team changes Store grants separately. |
| EV-STORE-008 | `apps/frontend/src/app/merchant/MerchantShell.tsx`, `chooseStoreId`, workspace/store handlers and selectors | P1 | Workspace-bounded selection, All Stores sentinel, local/session preference revalidated against authorized context. |
| EV-STORE-009 | `settings/page.tsx`, Store list/modal and `storeSubmit` | P1 | Owner/Admin Settings flow, Store display and separate create-then-logo request behavior. |
| EV-STORE-010 | `settings-data.ts`, Store management request types/routes | P1 | Store mutation and logo requests carry Workspace context. |
| EV-STORE-011 | `settings-workspace-scope.spec.ts` | P2 | Settings reloads/clears list on Workspace switch and binds mutations to active Workspace. |
| EV-STORE-012 | `store-logo-storage.service.ts` and specs; private logo controller endpoints | P1/P2 | JPEG/PNG/WebP content checks, 5 MB maximum, private path and file lifecycle. |
| EV-STORE-013 | `docs/ui/merchant/MERCHANT_STORE_MANAGEMENT_UI_SPEC.md` and `MERCHANT_SETTINGS_UI_SPEC.md` | P3 | Current UI contract, Staff boundary, no delete/transfer, Workspace-specific list and delivery-pricing exception. |
| EV-STORE-014 | Products, Orders, Inventory, Finance, Tracking/Delivery and Commerce section documents | P3 | Cross-domain ownership and distinction between Store scope and each domain’s data/policies. |
| EV-STORE-015 | `commerce.service.ts`, Commerce schema; `meta-conversion-destination.service.ts` | P1 | External provider identity and selected Meta conversion destination attach to Store scope separately. |
| EV-STORE-016 | `MERCHANT_STORE_MANAGEMENT_UI_SPEC.md` current Store lifecycle clauses | P3 | Disabled Store list vs active-only Shell selector contract. |
| EV-STORE-017 | `STORE_SOURCE_INTEGRATION_IDENTITY_P0_15.md` (historical design) | P4 | Conceptual Store/provider separation only; its “Shopify not live” state is stale against current code and is not used as current truth. |
| EV-STORE-018 | Backend targeted tests 23/23; frontend targeted tests 8/8; backend/frontend typechecks passed | P2 | Focused verification of Store management, access, UI workspace scope, and type integrity. |
| EV-STORE-019 | Competitive Intelligence Master v1.0, category baseline and capability matrix | P3 | Store integrations/basic user management are common infrastructure; not a fresh Store model comparison. |

## 28. Contradictions & Uncertainty

- Historical Store/Commerce architecture says Shopify install/controller flow was not live; this is stale relative to inspected current Shopify code. It is not used to conclude current Store-provider status.
- The current Store Management UI spec says disabled Stores are excluded from the operational selector; current Shell context query and selector align by projecting active Stores, while Settings management returns disabled Stores to managers.
- Store record has independent Merchant and Workspace foreign keys plus a composite unique key used by linked records; the service validates their association through actor context. No independent database relation from Store to `MerchantWorkspace` was found, so cross-link integrity is principally application-enforced at creation.
- The optional create URL backend validation gap and two-step logo workflow are directly observable in P1. Their production exploitability/frequency is not measured.
- Disabled records are retained, but access to historical domain records after disable varies by active-query filters and was not exhaustively proven for every domain.
- Local product checkout advanced during inspection, but the diff was limited to Shopify offer preview files; Store-relevant paths remained unchanged. Production code/deployment remains unverified.

## 29. Open Questions

1. Should a disabled Store expose a read-only historical view/filter for Orders, Finance and other retained records?
2. Should optional Store logo upload be separate from creation in the UI, or made idempotent/atomic from the user’s perspective?
3. What precise URL semantics are intended (display-only, commerce site, or verified external identity), and should create/edit share one backend policy?
4. Is there a supported Admin-side correction path for incorrectly assigned Merchant/Workspace ownership, and what approval/audit controls govern it?
5. What production object/file storage and backup policy guarantees logo durability across instances?

## 30. Methodology Learnings

For a cross-domain entity audit, distinguish **identity**, **scope**, **selector projection**, **authorization**, and **domain-owned records**; similarly, distinguish record retention from merchant-visible historical access. No methodology change proposed because existing source-state, boundary and owner-domain lenses cover the reusable questions.

## 31. Retroactive Review Impact

No methodology change requiring a retroactive review was made. No prior accepted section requires correction. The Stores audit confirms the Integrations review’s direction to keep internal Store scope/identity separate from external commerce-channel functionality.

## 32. Canonical Section Takeaway

Wossol Store is a provider-neutral Merchant operating identity nested in a Workspace, not automatically an external shop or physical inventory pool. Owner/Admin self-service lifecycle, active Workspace context, layered Staff Store scope and domain-owned Store relationships form useful operating infrastructure. This is table-stakes/foundational value rather than a proven differentiator. Keep All Stores Workspace-bounded and Store permissions additive to other access gates. Close the create-time URL validation gap and the partial Store-create/logo retry path; retain disabled history without confusing retention with active operation or guaranteed historical visibility.
