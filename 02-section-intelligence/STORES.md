# Stores — Section Intelligence

## 1. Audit Metadata

- **Audit date:** 2026-09-27 (incremental V1.2 migration).
- **Methodology:** `00-methodology/MASTER_INSTRUCTIONS.md` v1.2; `00-methodology/CODEX_OPERATING_PROTOCOL.md` v1.1; queue item `RR-V12-014`.
- **Competitive reference:** `01-competitive/WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md` v1.0 (2026-09-09).
- **Intelligence source:** `jetshop7/wossol-brand-intelligence`, `main`, starting commit `b97d678518713bb0b8bc71342114962fbbf5623f`, clean and synchronized with `origin/main` before task interpretation.
- **Product source:** `jetshop7/wossol-platform`, `dev/wossol-integration`, `933fb7d3431fc32de2473b4e45c2289e00111463`; local `HEAD` equals the local `origin/dev/wossol-integration` tracking ref. Product remote was not freshly fetched during this pass. Inspected read-only.
- **Source-state delta:** Compared with the prior Stores evidence commit `de2bb9bad7c39ce8f6ccf2d237a36cea1a82fb98`, only Prisma schema and MerchantShell changed among the inspected Store-related paths. Store model/service/controller, Store Settings UI/specifications, Store access code/specs and Store-focused frontend spec are unchanged. The schema adds Shopify COD checkout-session relations to Store; MerchantShell adds a workspace-scoped open-capture badge, not Store behavior. No Product files were modified.
- **Product local state:** Initially clean at the source-state check. At final verification, 14 uncommitted paths appeared in Orders, Confirmation, Analytics, and related admin/frontend surfaces; none are Store-owned paths. Preserve them untouched and exclude them from this audit's committed capability claims. Local HEAD still equals the tracking ref, but the Product remote was not freshly fetched, so equality with GitHub at audit time is **NOT VERIFIED**.
- **Verification performed:** Focused backend Store/access plus Shopify checkout-session unit tests: 19/19 passed (13 Store/access and 6 checkout-session tests). Store Settings workspace UI tests: 3/3 passed; adjacent MerchantShell Order Capture navigation tests: 3/3 passed (Node module-type warning only). These are source/unit tests; no DB migration application, typecheck, production, or runtime verification.
- **Audit status:** Incremental V1.2 source review complete; current Director Quality Gate pending. Preserve the Stores Director acceptance of 2026-09-26 and its open product issues; this migration is not itself accepted.

### V1.2 migration delta review

- **Prior product truth retained:** Store remains a provider-neutral Merchant operating identity inside one Workspace, with Owner/Admin-managed lifecycle, Staff Store grants as an additional restriction, and domain-owned interpretation of Store scope. The prior Director review's accepted conclusions and open issues are retained; no direct Store-owned code or Store UI contract changed since evidence commit `de2bb9b`.
- **Product truth changed:** The committed schema now relates `CommerceCodCheckoutSession` to Store, CommerceConnection, Product/ProductStore and a finalized Order by composite scope. Shopify COD code carries the same Merchant/Workspace/Store tuple into session lookup and canonical Order ingestion. The Home/Store Shell also now renders an open Order Capture count, queried by active Workspace; this is Orders navigation, not a Store-level capability.
- **V1.2 value previously missed:** Store is more than a selector/domain join for the Shopify COD path: it participates in identity continuity from connected storefront/product through temporary checkout evidence into an Order, alongside an acquisition snapshot. This supports traceable downstream joins, but does not establish complete ad attribution, Store-level profitability, or improved decisions.
- **Connected-domain evidence added:** inspected current Shopify checkout-session persistence/finalization and Order ingestion, Product/Commerce mapping relationships, and Store/Workspace context. Source inspection found a material Prisma-schema/SQL-migration mismatch in the new checkout-session migration; the consequence for an applied database is high risk but deployed migration state is unknown.
- **Prior strategic conclusion:** Stores remains foundational/scoped operating infrastructure, not a demonstrated differentiator or moat. V1.2 adds a qualified cross-domain provenance contribution, not a stronger superiority claim.
- **Queue:** `RR-V12-014` updated for this incremental pass; submit to current Director Quality Gate. Do not mark the V1.2 migration accepted here.

**Evidence standard:** P1 executable source/schema, P2 targeted tests, P3 current UI/system specifications, P4 historical design material qualified as such. Production deployment/data and merchant outcomes are not inferred from repository inspection.

## 2. Audit Coverage Map

| Surface | Coverage | Evidence |
|---|---|---|
| Store identity and schema | Store fields, lifecycle, Workspace/Merchant relationships, domain links | EV-STORE-001–002 |
| Merchant Store management API | list/create/edit/disable/reactivate and scope checks | EV-STORE-003–005 |
| Store access and roles | Owner/Admin management, Staff Store grants, workspace/section boundaries | EV-STORE-006–007 |
| Merchant Shell context | Workspace selection, active Store selection, All Stores, persistence | EV-STORE-008 |
| Store Settings UI | Workspace-scoped list, create/edit, errors, status and logo flow | EV-STORE-009–011 |
| Logo storage | size/type validation, private path, replacement/removal | EV-STORE-012 |
| Connected domains | Products/Orders/Inventory/Delivery/Finance/Commerce/Ads Store relationships; Shopify COD session → Order scope/provenance trace | EV-STORE-013–015, EV-STORE-020 |
| Store model vs migrations | Current Prisma checkout-session relation and additive SQL migration compared | EV-STORE-021 |
| Specifications and history | Current Store Management and Settings specs; historical Store-source architecture | EV-STORE-016–017 |
| Verification | prior accepted focused tests retained; current Store/access/COD session backend tests and Store Settings/Shell UI tests rerun | EV-STORE-018, EV-STORE-022 |
| Competition | stable competitive master, not a new competitor audit | EV-STORE-019 |

Not verified: production deployment/database state, real tenant/user records, live cloud object-storage configuration (the inspected implementation writes private local files), business usage or store-level outcome differences, and current competitor Store-entity designs first-hand.

## 3. Executive Section Truth

In the inspected product, a **Store is a Wossol Merchant-owned, Workspace-bound operating identity and scope**, not necessarily an external e-commerce storefront. It carries a generated immutable Store Code, name, optional URL, status, and optional private logo. Workspace supplies market/currency context; Store identity scopes relevant products, orders, pricing, provider connections, and other domain records. External commerce identity and credentials live separately in Commerce connections.

Merchant Owner and Admin can create and manage Stores from Settings within one authorized active Workspace. They can edit name/URL, manage a private logo, disable, and reactivate; there is no delete or cross-Workspace transfer. Staff has no Store mutation authority; their operating Store visibility is controlled by Store access in addition to Merchant, Workspace, and section access. The Shell lists active Stores only, and All Stores means the active/authorized Store cohort within the current Workspace—not every Store across the Merchant’s markets.

This is meaningful **tenant and operating-scope infrastructure**, with an understandable self-service lifecycle and strong access-boundary patterns. It is not itself a differentiated commerce feature. Store also acts as a scope/provenance key in the current Shopify COD checkout-session → canonical Order flow. That trace is constrained by a newly identified model/migration mismatch and is not proof of deployed behavior. Prior workflow risks remain: create-time URL input lacks the backend validation applied during edit, and Store creation plus optional logo upload can persist the Store before a failed second request, making an unguarded retry create another Store.

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
| Shopify COD session | Carries Merchant/Workspace/Store, CommerceConnection and Product scope plus acquisition/commercial snapshots; finalization submits a canonical Order using the same Store tuple. Migration parity is unresolved. |
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

Store management reduces setup friction and creates a durable scope boundary as a Merchant operates multiple sales identities or markets. Explicit Workspace confinement limits accidental cross-market setup; immutable Store Code and history-preserving disable reduce identity churn. In the Shopify COD chain, Store identity links a connected storefront/product context to a temporary captured checkout and then a canonical Order, allowing downstream domains to join on the same scope when their own data contracts permit. The main value remains **safer organization, context continuity and scoping**, not direct demand, conversion, profit, or market intelligence. No measured setup-time, error-rate, or operational outcome improvement was available.

### V1.2 second-pass merchant value synthesis

| Capability / merchant job | Work removed or reduced | Remaining work and boundary | Truth, context and downstream value | Claim limit / proof consequence |
|---|---|---|---|---|
| Create a Store in an authorized Workspace rather than request every internal operating identity from platform staff | Reduces provisioning handoff/setup friction qualitatively | Owner/Admin still configures Store details and separately connects a provider; no measured setup-time delta | Backend-generated Store Code and Workspace/merchant ownership provide a stable scope key | Demo the Store Code and active Workspace; do not claim the Store itself connects an external channel |
| Switch among Stores / use All Stores within one Workspace | Reduces repeated manual market/Store filtering in screens that honor Shell context | Each domain can define filters differently; All Stores is not all markets, and concrete Store may still be required for writes | Shell validates stored preference against authorized active context; domain joins preserve Store identity | No complete cross-market operational or performance view is implied |
| Limit a Staff member to selected Stores | Reduces ad hoc reliance on verbal store assignments and broad visibility | Merchant, Workspace and section gates still apply; some endpoints may vary and product-wide universal enforcement is not proven by this section | Store grant is an additive access restriction, maintained by Team/auth services | Demonstrate only audited paths; not a universal guarantee without endpoint-wide verification |
| Preserve Store identity while disabling/reactivating | Avoids destructive deletion/identity churn and retains linked references | Disabled Store operational visibility is restricted; historical browsing differs by domain and remains unresolved | Stable Store key can connect historical records and lifecycle Audit evidence | Retention is not equivalent to convenient historical access |
| Carry Shopify COD context into Orders | Could reduce manual Store/source re-entry in the implemented code path; this reduction is not established in a running database workflow | Product/Commerce mapping and valid App Proxy path are prerequisites; Order processing/confirmation/delivery/economics remain owner-domain work; DB migration parity unresolved | Checkout session code carries Store + Workspace + Merchant tuple and acquisition snapshot; finalization submits an Order with the same tuple and evidence | Proof is code-level and partially unit-tested; current migration omits model fields/constraints, and live operation is not verified |

**Operational → economic → decision chain:** Store identity is present and joinable as a scope key in relevant records (DATA CAPTURED / CONNECTED). Domain owners calculate operational and economic truth; Stores itself does not reconcile or interpret it. This audit found no Store-level recommendation/action/outcome loop (NOT FOUND AFTER SEARCH in the inspected Store surface and relevant cross-domain evidence). No merchant decision improvement or quantified economic benefit may be claimed.

No evidence establishes that Store management itself replaces a merchant's commerce-provider portal, spreadsheet, or other operating system. It consolidates Wossol's internal Store identity/scope and connects to separately owned domain workflows; external provider management remains separate.

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
- **Shopify COD capture → Orders:** The current checkout-session schema/service carries Store/Workspace/Merchant identity with connection and product scope, snapshots acquisition evidence, and submits a canonical Order on finalization. This is a current source-code chain, but the session migration does not match the Prisma model; deployed availability and end-to-end provenance are not established (EV-STORE-020–021).
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
- **P1 — New schema/migration contract mismatch:** `CommerceCodCheckoutSession` Prisma model declares `revision`, `upsellDecisionIndex`, composite unique keys and relations to Store/CommerceConnection/Product/ProductStore/Order. `20260927_shopify_cod_checkout_session_v1/migration.sql` creates neither the two declared scalar fields nor the composite unique indexes/foreign-key constraints. If this migration is applied as written against this model, the database will not provide the model's declared contract and runtime operations depending on missing fields/relational integrity may fail or permit inconsistent references. The deployed migration state is **NOT VERIFIED**; confirm/fix migration and exercise generated-client integration before claiming the new Store→checkout→Order chain is operational.
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
- **Store continuity through Shopify COD capture:** POTENTIAL DIFFERENTIATOR / qualified foundation; current P1 code retains scope and acquisition evidence into canonical Order, but migration mismatch and no measured merchant outcome materially limit the claim.
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
| P1 | Align the Shopify COD checkout-session SQL migration with the Prisma model (fields, composite keys and foreign keys); verify it with schema/migration validation and DB-backed integration tests. | MUST FIX | Current schema-to-migration mismatch undermines the newly introduced Store-scoped session/order provenance path if the SQL migration is applied as written. |

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
| EV-STORE-020 | `CommerceCodCheckoutSession` model; `shopify-cod.service.ts` (`syncCheckoutSession`, `scopedCheckoutSession`, `contextForCheckoutSession`, `finalizeCheckoutSession`); focused session tests | P1/P2 | Current committed source carries Merchant/Workspace/Store plus provider/Product scope and acquisition snapshot into session state, then canonical Order ingestion with the same scope. Unit tests cover retry/cursor/finalization concurrency behaviors; they do not establish live DB operation or full provenance quality. |
| EV-STORE-021 | Prisma `CommerceCodCheckoutSession` / Store relations vs `20260927_shopify_cod_checkout_session_v1/migration.sql` | P1 source contract comparison | Model declares `revision`, `upsellDecisionIndex`, composite unique keys and relational references; SQL creates none of those declared fields/constraints. Deployed migration application/current DB parity is NOT VERIFIED. |
| EV-STORE-022 | Current Product focused run: `merchant-store-management.service.spec.ts`, `merchant-store-access.service.spec.ts`, `shopify-cod-checkout-session.spec.ts` (19/19); `settings-workspace-scope.spec.ts` and `merchant-shell-order-captures-nav.spec.ts` (6/6) | P2 | Current scoped Store/access, checkout-session unit behaviors, Workspace-bound Store Settings and Shell capture-count navigation passed; mocks/source assertions do not verify database migration or production behavior. |

## 28. Contradictions & Uncertainty

### C-STORE-001 — Shopify COD checkout-session Prisma model and SQL migration disagree

- **Source A:** `apps/backend/prisma/schema.prisma`, model `CommerceCodCheckoutSession`, declares `revision`, `upsellDecisionIndex`, composite unique keys, and relations to Store, CommerceConnection, Product, ProductStore, and finalized Order.
- **Source B:** `apps/backend/prisma/migrations/20260927_shopify_cod_checkout_session_v1/migration.sql` creates the session table and basic indexes, but omits `revision`, `upsell_decision_index`, the model's composite unique indexes, and foreign-key constraints.
- **Nature of conflict:** The Prisma data model describes a stronger, richer relational contract than the committed SQL migration creates.
- **Evidence strength:** Both are P1 committed product artifacts at Product `933fb7d`; current database/deployed migration state was not inspected.
- **Working conclusion:** Schema/migration parity is not established. If the SQL is applied as written, it does not implement the checked-in model contract and may prevent operations requiring missing columns or permit invalid cross-scope references. Unit tests use a mocked model and do not resolve this.
- **Remaining uncertainty:** Whether the migration has been applied, whether another migration or deployment process supplies the omitted objects, and actual DB behavior.
- **Required verification:** Align migration and Prisma model; inspect deployment migration state; run migration/schema validation and DB-backed session→Order tests.

- Historical Store/Commerce architecture says Shopify install/controller flow was not live; this is stale relative to inspected current Shopify code. It is not used to conclude current Store-provider status.
- The current Store Management UI spec says disabled Stores are excluded from the operational selector; current Shell context query and selector align by projecting active Stores, while Settings management returns disabled Stores to managers.
- Store record has independent Merchant and Workspace foreign keys plus a composite unique key used by linked records; the service validates their association through actor context. No independent database relation from Store to `MerchantWorkspace` was found, so cross-link integrity is principally application-enforced at creation.
- The optional create URL backend validation gap and two-step logo workflow are directly observable in P1. Their production exploitability/frequency is not measured.
- Disabled records are retained, but access to historical domain records after disable varies by active-query filters and was not exhaustively proven for every domain.
- Current checkout-session schema models Store and related records with composite scope references, but its same-date SQL migration omits fields/constraints required by that model. Since no database or deployment migration state was inspected, whether this blocks a deployed workflow is unresolved; do not describe the Store→COD session chain as verified in production.
- Product's local working tree was clean when the committed source was first recorded, but 14 unrelated Orders/Confirmation/Analytics/admin paths were modified by the final check. They were not inspected as Store truth or modified by this audit; any effect they may have on downstream operations is excluded, and the local checkout is no longer clean.

## 29. Open Questions

1. Should a disabled Store expose a read-only historical view/filter for Orders, Finance and other retained records?
2. Should optional Store logo upload be separate from creation in the UI, or made idempotent/atomic from the user’s perspective?
3. What precise URL semantics are intended (display-only, commerce site, or verified external identity), and should create/edit share one backend policy?
4. Is there a supported Admin-side correction path for incorrectly assigned Merchant/Workspace ownership, and what approval/audit controls govern it?
5. What production object/file storage and backup policy guarantees logo durability across instances?
6. Has `20260927_shopify_cod_checkout_session_v1` been applied anywhere, and if so, how does its live schema compare with the Prisma model's `revision`, decision cursor, composite keys and foreign-key relations?
7. Do DB-backed tests confirm Store identity and acquisition evidence survive Shopify COD session finalization into the canonical Order and downstream outcomes?

## 30. Methodology Learnings

For a cross-domain entity audit, distinguish **identity**, **scope**, **selector projection**, **authorization**, and **domain-owned records**; similarly, distinguish record retention from merchant-visible historical access. No methodology change proposed because existing source-state, boundary and owner-domain lenses cover the reusable questions. Self-critique: the new Shopify COD chain is easy to overstate from the model/service alone; SQL omits declared contract elements, tests mock persistence, no deployed DB was inspected, and merchant outcomes are absent. Its value remains a qualified source-level foundation, not a proven working compound advantage.

## 31. Retroactive Review Impact

No methodology change requiring a retroactive review was made. This is the requested incremental V1.2 migration for `RR-V12-014`; it adds a Store-linked COD→Order chain and a schema/migration risk. The established cross-section rule remains: internal Store scope/identity is separate from external commerce-channel functionality. Current Director Quality Gate remains pending.

## 32. Canonical Section Takeaway

Wossol Store is a provider-neutral Merchant operating identity nested in a Workspace, not automatically an external shop or physical inventory pool. Owner/Admin self-service lifecycle, active Workspace context, layered Staff Store scope and domain-owned Store relationships form foundational operating infrastructure. Shopify COD source shows Store scope can continue into a temporary checkout session and canonical Order, but the associated SQL migration does not encode the full Prisma model contract, and production operation is unverified. This is not a proven differentiator. Keep All Stores Workspace-bounded and Store permissions additive to other access gates; retain prior URL validation, partial-create/logo recovery, disabled-history, terminology and deployment issues.
