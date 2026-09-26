# Advertising — Section Intelligence

## 1. Audit Metadata

- **Audit date:** 2026-09-26.
- **Methodology:** `00-methodology/MASTER_INSTRUCTIONS.md` v1.1; `00-methodology/CODEX_OPERATING_PROTOCOL.md` v1.0.
- **Competitive reference:** `01-competitive/WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md` v1.
- **Intelligence source:** `jetshop7/wossol-brand-intelligence`, `main`, `0d78ce6cceabd6495382a0a7501ca584fd74f914` at audit start, clean and synchronized with `origin/main` before inspection.
- **Product source:** `jetshop7/wossol-platform`, `dev/wossol-integration`, `8600a4cbd1a894579a057b3476db35465289c670`, clean and tracking `origin/dev/wossol-integration`; inspected read-only.
- **Evidence standard:** P1 executable source/schema, P2 focused tests/typechecks, P3 current merchant UI spec/current master architecture, and P4 historical design notes only when qualified. Source inspection does not prove deployment, real provider acceptance, data completeness at scale, merchant adoption, legal compliance, or business outcomes.

## 2. Audit Coverage Map

| Surface | Coverage | Evidence |
|---|---|---|
| Merchant Advertising hub and provider pages | routes, states, permissions, connection selection, reporting presentation | EV-ADV-001–002, 012 |
| API trust boundary and connection lifecycle | Workspace/Merchant scope, permissions, OAuth state, credentials, relink/disconnect | EV-ADV-002–003 |
| Meta hierarchy and reporting sync | fixed provider client, durable sync, pagination, reconciliation, persisted daily slices | EV-ADV-004–006 |
| Attribution and acquisition evidence | URL parameter health, ingress parsing, exact canonical identity resolution, Orders persistence | EV-ADV-007–008 |
| Product and Variant mapping | explicit merchant mapping, scope, correction/history semantics | EV-ADV-009 |
| Meta Conversions API Purchase | destination discovery/selection, eligibility, durable event dispatch and payload boundary | EV-ADV-010 |
| Schema, connected domains, product documents | Advertising schema/migrations, Analytics, Orders, Finance, current vs historical specs | EV-ADV-011–013 |
| Verification | Advertising backend specs, typechecks, source state | EV-ADV-014 |
| Competitive comparison | stable competitive master only; competitor claims not re-verified live in this audit | EV-ADV-015 |

Not inspected/verified: production deployment and database state, connected Meta/TikTok accounts, real sync freshness/volume, live Meta event acceptance or Ads Manager results, privacy/legal basis and consent implementation, merchant usage/conversion outcomes, current competitor private capabilities.

## 3. Executive Section Truth

Advertising is a real but asymmetric operational subsystem. Meta has OAuth and account authorization, a persisted Campaign→Ad Set→Ad graph, daily provider reporting, read-only merchant performance projections, explicit product mappings, narrow acquisition-evidence resolution, URL-tag health, and a carefully bounded server-side Purchase event path. TikTok is currently a connection/authorized-advertiser surface, not a reporting or optimization integration. Google and Snapchat are not implemented as provider adapters in the inspected source; enum/schema vocabulary alone is not evidence of integration.

The strongest current value is not automated ad buying. It is bringing selected Meta evidence into a Workspace-scoped operating system while keeping provider-native results distinct from orders Wossol can exactly attribute. CAPI Purchase is an additional narrow bridge from eligible Shopify Wossol COD order-creation events to a selected Meta Dataset; it is not proof of ad acquisition, campaign effectiveness, or a complete ads-to-profit loop. No inspected path mutates budgets, bids, campaigns, or creative.

The material gap is decision closure: there is no cross-platform, end-to-end system that reliably observes click→order→delivery/collection→profit, learns causality, and executes optimization. Provider runtime acceptance, legal basis, and deployed correctness remain unverified.

## 4. Scope & Architecture Map

The Merchant Advertising API and UI are Workspace-wide and Store-independent for provider connections and reporting. `MerchantAdvertisingScopeGuard`, `MerchantAdvertisingService`, controller permissions, and `AdvertisingService` enforce active Workspace/Merchant scoping. Meta and TikTok OAuth adapters create/reconnect provider-bound connections and securely stage credentials. Meta sync workers persist canonical provider entities and daily reporting slices. Reporting endpoints project persisted evidence. Commerce order ingress supplies optional acquisition identifiers; the Advertising resolver checks those against canonical scoped entities and appends exact or unresolved evidence to Order attribution. Product/Variant mapping is separate, merchant-confirmed linkage. The CAPI Purchase destination is Store-bound even though its Advertising connection is Workspace-wide; its independent durable dispatch consumes eligible order-created events.

Analytics consumes persisted Advertising reporting and attribution evidence but owns its own reporting/decision projections. Finance owns collections, fees, and financial evidence; Advertising does not calculate profit. Orders owns order lifecycle and attribution history. These domain boundaries are substantive and should not be collapsed into an “ad performance” claim. (EV-ADV-002, 006–013.)

## 5. Current Capability Inventory

| Capability | Status | Current behavior |
|---|---|---|
| Meta connect/reconnect/disconnect | LIVE in source | OAuth state bound to scope/provider; credential staging/finalization; exact connection lifecycle |
| TikTok connect/reconnect | LIVE, limited | OAuth and authorized advertiser connection; no performance sync or reporting surface established |
| Meta structure sync | LIVE in source | Bounded persisted account/campaign/ad-set/ad hierarchy with parent identity and reconciliation |
| Meta daily performance | LIVE in source | Persisted daily account/campaign/ad-set/ad provider slices; one explicit connection per reporting query |
| Meta Results | LIVE, provider-native | Provider conversion evidence remains distinct and compatibility-qualified |
| Wossol Outcomes | LIVE, exact-evidence-limited | Orders with persisted exact acquisition identity; not a heuristic or provider conversion count |
| URL-tag attribution health | LIVE, bounded | Aggregate readiness/affected status; raw tags, URLs, and creative IDs are not exposed |
| Product/Variant mapping | LIVE, explicit | Merchant-confirmed links to Meta entities; not attribution and not a provider-side edit |
| Meta CAPI Purchase | LIVE in code; runtime acceptance unverified | Narrow Shopify/Wossol COD Order-created events sent to an activated Store Dataset destination |
| TikTok performance; Google/Snap adapters | NOT IMPLEMENTED / NOT FOUND AFTER SEARCH | No corresponding sync/reporting adapter established in reviewed source |
| Automated optimization / causal learning / ad mutation | NOT IMPLEMENTED in reviewed surface | No budget/bid/campaign write or proven outcome-learning loop found |

## 6. Workflow & Lifecycle

1. An authorized merchant enters Advertising in an active Workspace; read access and connection-management authority are distinct.
2. Meta/TikTok connection initiation persists hashed one-time state with bounded expiry and scope; callback uses state-bound scope rather than trusting browser scope. Credentials are staged through the secure-credential service and are not returned to the browser.
3. Successful Meta authorization discovers provider accounts and queues bounded durable sync work. Workers persist structure then reporting; periodic requests are idempotent by connection/hour. Failures, partial results, leases, retries, and abandoned runs have explicit states.
4. Merchant performance reads persisted data for one chosen connection and bounded dates; there is no live provider request from the browser. A missing slice is not rendered as zero. Provider Results and exact Wossol-attributed outcomes are separate measures.
5. Optional URL tags are configured at Meta. Health inspects a bounded set of ads and saves safe aggregate status. Order ingress parses the versioned parameter contract; the resolver only links exact persisted, scoped provider identities and parent relationships. Otherwise the evidence remains unresolved.
6. Product/Variant links require explicit merchant action and remain separate from Orders attribution.
7. A Store CAPI destination is discovered against an authorized Meta account and provider Dataset candidates; after activation, a durable cursor consumes eligible order-created events and sends immutable snapshots with stable event identity, bounded retries, and safe status.

The source defines scheduler windows in UTC while provider reporting is daily and carries provider timezone metadata; actual provider-side freshness, account-local boundary behavior, and late historical revisions have not been validated against a live account. (EV-ADV-003–010.)

## 7. Value Recipient Map

| Recipient | Current value |
|---|---|
| Merchant/operator | Connect Meta, inspect persisted account/ad structure and daily performance, see safe tracking readiness, deliberately map products, distinguish provider results from exact order outcomes |
| Marketing operator | Obtain a bounded, persisted Meta reporting view and exact acquisition evidence where URL tags survive the commerce journey |
| Customer | No direct Advertising UI/control value established; order data can be transmitted to Meta via the separately activated Purchase destination |
| Wossol | Canonical provider entities, reporting evidence, mapping history, and explicit attribution evidence can support later cross-domain analysis |
| Meta | Eligible, configured Purchase events from the narrow server-side pathway; provider acceptance and resulting optimization value not verified |
| TikTok / other ad providers | TikTok authorization exists; no reporting data flow established; no adapter evidence for Google or Snapchat |

## 8. Control & Merchant Agency

The merchant controls authorization, exact connection selection, local Wossol-use pause/resume, disconnect, Product/Variant mapping, and CAPI destination activation. Local use pause is not provider revocation. Performance pages are read-only. No campaign, budget, bid, audience, or creative controls were found. Destination activation can enable subsequent eligible Purchase dispatch but is not an Ads optimization control. (EV-ADV-001–003, 009–010.)

## 9. Transparency & Trust

Strong safeguards include active scope checks repeated in service logic, separated permissions, state-hash/one-time OAuth claims, provider-bound credentials, fixed provider hosts and endpoints, bounded pages/responses, safe error codes, no token/raw provider payload exposure, explicit missing-vs-zero semantics, exact connection selection, immutable dispatch snapshots, and distinct provider versus Wossol outcome labels. URL-tag inspection stores safe aggregates only. Trust limits remain: runtime authorization and provider contracts are not live-tested; CAPI sends hashed identity fields (pseudonymized data, not anonymous data); no consent/legal basis was verified; the UI’s approximate hourly refresh is not a latency SLA. (EV-ADV-002–006, 008, 010, 014.)

## 10. Merchant Value Extraction

Meta reporting can reduce tool/context switching by bringing persisted spend, impressions, clicks, reach, and derived metrics into the merchant workspace. It is only as current and complete as successful provider sync. Exact Wossol outcomes can answer a narrower question—what orders carry exact recognized acquisition evidence—without presenting Meta-reported conversions as Wossol orders. Explicit mappings can connect a merchant’s catalog to advertising entities, but do not establish sales attribution or profitability. CAPI can provide an eligible server-side Purchase signal to Meta; it cannot demonstrate that the event was accepted, matched, or improved delivery. (EV-ADV-004–010.)

## 11. Feature Clusters

1. **Connect and authorize:** Meta/TikTok OAuth, reconnect, credential safety, lifecycle and local-use control.
2. **Observe Meta:** canonical hierarchy, durable sync, daily report slices, connection-specific read projection.
3. **Relate evidence:** URL tag contract/health, exact Order acquisition evidence, separately explicit Product/Variant mapping.
4. **Transmit selected outcome:** Store Dataset destination and durable, bounded Meta Purchase delivery.

The clusters are connected, but not a closed optimization loop: mappings do not attribute orders; acquisition evidence does not prove delivery/profit; CAPI dispatch does not prove campaign causality. (EV-ADV-004–013.)

## 12. Merchant Journey / Old Way vs Wossol Way

Potential journey: connect Meta → authorize account → wait for durable sync → review one account’s persisted hierarchy/performance → configure exact URL parameters → inspect tracking health → observe some exact-attributed Orders → optionally map Products/Variants and activate a Store Purchase destination. The journey has meaningful gaps: TikTok has no analogous reporting journey; acquisition depends on URL evidence surviving checkout and exact canonical matches; collection/delivery/profit are not closed in Advertising; no automatic optimization follows.

The “old way” substitute is provider-native Meta Ads Manager plus commerce order and spreadsheet reconciliation. Wossol may reduce fragmentation for its narrow integrated view, but the audit does not establish time savings or superior reporting completeness. (EV-ADV-001, 006–010.)

## 13. Hidden / Non-Obvious Advantages

- Provider-reported conversions and Wossol-attributed Orders are deliberately different measures, preventing a common category error.
- Explicit provider hierarchy and scope-bound exact matching avoid treating arbitrary incoming IDs as attribution.
- CAPI dispatch is a separate, auditable pipeline rather than silently treating every attributed order as an eligible Purchase event.
- Product/Variant mapping history can be corrected without rewriting historical Order evidence.

These are implementation-quality advantages, not verified market differentiation. (EV-ADV-006–010.)

## 14. Data & Intelligence Assets

Persisted assets include provider connection/account identity and lifecycle; canonical ad hierarchy and relationship identity; sync runs/checkpoints; daily reporting slices with date, currency, provider timezone, attribution policy and provider query identity; provider metric evidence; safe URL-tag-health aggregates; exact/unresolved Order acquisition evidence; merchant-confirmed mapping history; and immutable CAPI dispatch evidence. These assets can support reporting and later analysis. They do not by themselves amount to cross-platform attribution, clean-room data, causal measurement, identity graph, or autonomous intelligence. Missing provider coverage, consent/legal basis, click survival, late revisions, and financial lifecycle linkage constrain interpretation. (EV-ADV-004–013.)

## 15. Cross-Section Compound Advantages

Advertising + Orders joins exact acquisition evidence to canonical Orders. Advertising + Products provides deliberate product-to-ad-entity associations. Advertising + Analytics exposes persisted provider performance and separates provider conversions from Wossol outcomes. Advertising + Finance could support qualified outcomes only when compatible collection, cost, fee, expense, period, and attribution evidence exist; Advertising itself does not provide the profit computation. Advertising + Confirmation/Tracking/Delivery might mature order outcomes but no Advertising-owned complete lifecycle attribution/optimization was established. These are evidence seams, not a proven unified return-on-ad-spend capability. (EV-ADV-007, 009–013.)

## 16. Competitive Analysis

The competitive master supports category-level expectations around ad-platform reporting and commerce analytics but does not establish current competitor implementation depth. Advertising connections and dashboards are readily copyable. Wossol’s potentially less-common angle is a scoped combination of local commerce operations, explicit exact acquisition evidence, and separately governed server-side Purchase dispatch. The reviewed evidence is insufficient to call that unique, superior, or a durable moat. Competitors were not re-verified live for this audit. (EV-ADV-015.)

## 17. Marketing Intelligence

Defensible present-tense angle: “Connect Meta, review synced ad performance alongside your Wossol operations, and distinguish platform-reported results from orders Wossol can match to exact ad evidence.” This requires qualifying availability, connection choice, sync state, and attribution coverage. A second safe description is “send eligible Wossol COD Purchase events to a configured Meta Dataset,” with runtime acceptance explicitly unclaimed.

Avoid “all-channel ad intelligence,” “true ROAS,” “automatically optimize,” “every order attributed,” “real-time,” or “privacy-safe” absent evidence beyond this audit. TikTok connection must not be marketed as TikTok analytics. (EV-ADV-006–010, 012.)

## 18. Surprise Findings

The breadth of Meta implementation is materially beyond a basic OAuth connection, yet the strongest business conclusion remains narrower than “ad attribution”: reporting is provider-native, Wossol outcomes require exact acquisition evidence, and CAPI eligibility is an independent source/event rule. Also, current executable behavior materially exceeds an older attribution-foundation document that describes reporting/UI as deferred; that P4 text should not be mistaken for current source truth. (EV-ADV-004–010, 012.)

## 19. Potential Category Reframes

Provisional only: “commerce operations with evidence-aware advertising measurement” is a possible frame to investigate. This audit does not establish enough integrated outcome coverage, market superiority, customer demand, or causal proof to recommend a category change.

## 20. Brand Evidence

Current evidence suggests a product posture of operational connection, explicit scope, cautious evidence treatment, and merchant-controlled linkage. Those are behaviors, not a final brand identity or promise. A plausible future territory is “commerce outcomes with evidence attached,” but current coverage is Meta-specific and incomplete. Do not infer final positioning, naming, or brand promise from this section.

## 21. Weaknesses / Risks / Gaps

- Meta only for sync/performance; TikTok is connection-only; no Google/Snap provider adapters established.
- No campaign mutation, budget/bid optimization, causal evaluation, automated learning, or closed decision-action loop.
- Exact acquisition coverage depends on correctly configured tags, journey preservation, canonical entity matching, and order-ingress evidence; unresolved identities remain unresolved.
- Provider-reported conversions are not Wossol orders, and Wossol exact order counts are not delivered/collected/profitable outcomes.
- Daily/hourly sync design does not establish live freshness or historical correction completeness; runtime provider state not verified.
- Meta CAPI provider acceptance, event matching, legal basis/consent, data retention, and measured effect are unverified. Hashed phone/name remain potentially personal data.
- An older P4 architecture note is stale/contradictory with current implementation; maintain clear superseded status in future product docs.
- Focused PostgreSQL concurrency test was skipped; no live DB migration/concurrency verification performed.

## 22. Future Strategic Potential

If provider coverage, acquisition capture, delivery/collection outcomes, financial completeness, consent governance, data-quality feedback, and operational action loops mature, Advertising could contribute to a commerce outcome measurement system. Each is a future dependency, not current capability. Expansion should preserve separation between provider fact, Wossol-observed event, merchant mapping, and derived business outcome.

## 23. Claim Safety

| Claim | Status | Safe formulation |
|---|---|---|
| Meta connection and persisted performance | SUPPORTED, source-level | Meta accounts can sync and display persisted daily performance in the reviewed implementation; runtime availability not verified |
| Exact Wossol ad-attributed Orders | QUALIFIED | Shows Orders with exact recognized persisted acquisition evidence; coverage may be partial |
| TikTok advertising analytics | UNSUPPORTED | TikTok account connection/authorized advertisers only; no performance reporting found |
| Automated optimization / full ROAS | UNSUPPORTED | No automated Ads mutation or complete, causal ad-to-profit loop established |
| Meta Purchase event delivery | QUALIFIED | Eligible configured events are queued/sent through a durable implementation; provider acceptance and impact unverified |
| Privacy-safe CAPI | UNSUPPORTED as broad claim | Payload is bounded and hashes selected identity fields; legal compliance or anonymity is not established |

## 24. Commercial Magnitude

Potentially meaningful for merchants running Meta campaigns who otherwise reconcile Ads Manager and order operations manually. Magnitude is unmeasured: no adoption, time saved, attribution coverage, incremental sales, event match rate, profit impact, or retention data were inspected. The subsystem may improve visibility and data access without changing business outcomes.

## 25. Strategic Classification

- **Current category:** Meta advertising connection, persisted reporting, and evidence-qualified commerce linkage; plus limited TikTok authorization.
- **Control level:** read/observe and merchant-configured links; narrow server-side event transmission; no advertising decision execution.
- **Intelligence level:** structured provider facts + exact-match operational evidence; not causal intelligence or autonomous learning.
- **Differentiation confidence:** low-to-moderate hypothesis; competitive uniqueness not verified.
- **Evidence confidence:** high for reviewed source implementation and focused unit behavior; low/unverified for deployment and commercial outcomes.

## 26. Action Register

| Priority | Action | Owner / boundary | Audit disposition |
|---|---|---|---|
| P1 | Validate live Meta OAuth, sync freshness, reporting revisions, and real CAPI acceptance in approved test environment | Product/runtime owner | Not performed; no credentials or runtime evidence in scope |
| P1 | Establish reviewed consent/legal basis and retention posture for Meta hashed identity/event data | Product/privacy governance | Not assessed as legal advice; unresolved evidence gap |
| P2 | Clarify older attribution foundation document as historical/superseded | Product documentation owner | No Product repo edits authorized in audit |
| P2 | Evaluate TikTok reporting only as separately scoped implementation; do not imply it currently exists | Product owner | No implementation requested |
| P2 | Test late Meta report revisions and account-timezone day boundaries against provider sandbox/live account | Product/runtime owner | Not verified |
| P3 | Preserve provider conversion, exact acquisition, lifecycle, and financial outcome distinctions in future UX/claims | Product and marketing | Guardrail recorded here |

## 27. Evidence Register

| ID | Evidence | Relevance |
|---|---|---|
| EV-ADV-001 | `apps/frontend/src/app/merchant/advertising/` and `docs/ui/merchant/MERCHANT_ADVERTISING_UI_SPEC.md` | Merchant pages, states, connection/performance UI contract |
| EV-ADV-002 | `apps/backend/src/modules/advertising/merchant-advertising-scope.guard.ts`, `merchant-advertising.controller.ts`, `merchant-advertising.service.ts` | Active Workspace/Merchant scope, read/manage permissions, safe projections |
| EV-ADV-003 | `apps/backend/src/modules/advertising/meta-oauth.service.ts`, `tiktok-oauth.service.ts`, provider OAuth controllers and credential service integration | One-time scoped OAuth, provider binding, secure credential lifecycle |
| EV-ADV-004 | `apps/backend/src/modules/advertising/meta-graph.client.ts`, `meta-ad-structure-sync.service.ts` | Fixed bounded Meta client and canonical structure sync |
| EV-ADV-005 | `advertising-periodic-sync.scheduler.ts`, `advertising-sync-request.worker.ts`, `advertising-sync-scope.service.ts`, `advertising.service.ts` | Durable requests, idempotency, leases, retries, lifecycle and sync scope |
| EV-ADV-006 | `meta-insights-reporting-sync.service.ts`, merchant reporting projection files under Advertising, Analytics advertising projection | Persisted daily slices, connection-specific reporting, provider Results vs Wossol Outcomes |
| EV-ADV-007 | `advertising-acquisition-evidence-resolver.service.ts`, `tracking-parameter-ingress-parser.ts`, Orders attribution persistence and Commerce order ingress | Exact scoped identity resolution; unresolved evidence; no inference from mappings |
| EV-ADV-008 | `meta-attribution-health.service.ts`, `meta-tracking-parameter-contract.spec.ts` | Bounded safe tag inspection and versioned Meta URL parameter contract |
| EV-ADV-009 | Advertising Product/Variant mapping controller/service and mapping lifecycle tests | Merchant-confirmed mappings, scope, append/preserve corrections |
| EV-ADV-010 | `meta-conversion-destination.service.ts`, `meta-conversion-dispatch.service.ts`, Meta Purchase client and tests | Store destination verification, event eligibility, immutable durable dispatch and payload boundary |
| EV-ADV-011 | Advertising Prisma schema and `apps/backend/prisma/migrations/*advertising*` / related migrations | Scoped identities, reporting evidence, mapping and dispatch persistence constraints |
| EV-ADV-012 | `docs/ui/merchant/MERCHANT_ADVERTISING_UI_SPEC.md`; `docs/wossol-system-design/.../ADVERTISING_FOUNDATION_MASTER_ARCHITECTURE.md`; historical `.../ADVERTISING_ATTRIBUTION_FOUNDATION_V1.md` | Current UI/architecture direction versus stale historical/deferred claims |
| EV-ADV-013 | `02-section-intelligence/ANALYTICS_DECISION_CENTER.md`, `ORDERS.md`, `FINANCE.md`, `TRACKING_DELIVERY.md`, `PRODUCTS.md`, `MARKET_CENTER.md` and current Product owner-domain services | Cross-section authority and outcome limits |
| EV-ADV-014 | Advertising backend `*.spec.ts`; `pnpm --filter @wossol/backend typecheck`; `pnpm --filter @wossol/frontend typecheck` | 191 Advertising tests passed, 0 failed, 1 PostgreSQL concurrency test skipped; both typechecks passed |
| EV-ADV-015 | `01-competitive/WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md` | Stable competitive baseline; not live competitor verification |

## 28. Contradictions & Uncertainty

- **Historical product-doc mismatch:** the older `ADVERTISING_ATTRIBUTION_FOUNDATION_V1.md` labels reporting/UI deferred; current P1 source and later master architecture describe implemented Meta reporting and UI. Current behavior is resolved from executable P1; the older text is historical intent, not evidence of absence. No product-doc edit was made.
- **CAPI approval vs runtime:** current architecture describes bounded V1 with runtime acceptance still pending; implementation and tests establish code-path behavior, not external provider acceptance.
- **“Automatic refresh about hourly” UI contract:** source has hourly idempotent periodic requests; this does not prove hourly completion, freshness SLA, or refresh of all historical revisions.
- **Attribution semantics:** canonical Wossol outcomes require exact evidence. Neither provider conversions nor Product mappings substitute for it; true capture completeness is unknown.
- **Provider coverage:** TikTok account authorization exists, while the UI and inspected source have no TikTok performance sync; Google/Snap adapters not found after scoped search.
- **Source/environment:** production accounts, data, deployment, runtime logs, migrations, and concurrency behavior were unavailable. One PostgreSQL-specific coordination test was skipped.
- **Potential daily range issue:** requests use UTC-derived bounded windows while reporting data is provider-daily and stores provider timezone; actual local-day edge correctness and late-revision reconciliation remain unverified rather than asserted as defects.

## 29. Open Questions

1. What live Meta OAuth scopes and account conditions are approved, and are all supported accounts syncing successfully?
2. What freshness and historical revision guarantees are observed in deployed reporting across provider timezones?
3. Has Meta accepted the configured CAPI Purchase events, and what event match/diagnostic evidence is available?
4. What documented legal basis, consent controls, retention and deletion policies govern transmitted identifiers?
5. What share of real eligible Orders have valid exact URL-tag evidence, and how does that mature through delivery/collection and Finance outcomes?

## 30. Methodology Learnings

No reusable methodology change is proposed. The section-specific critical distinction—provider-reported results vs exact Wossol acquisition evidence vs CAPI event delivery—is captured in this audit and aligns with existing evidence/claim-safety rules.

## 31. Retroactive Review Impact

No methodology change; no retroactive queue update required. The fetched `04-review-history/MARKET_CENTER_REVIEW_2026-09-26.md` was consulted as current review context: do not infer demand/profitability from Market Center signals in downstream advertising/sourcing interpretation. This audit makes no such inference.

## 32. Canonical Section Takeaway

Wossol has a substantial Meta-specific advertising evidence subsystem: safe connection lifecycle, durable structure and daily reporting, exact-match acquisition evidence, merchant-controlled product mapping, and a separate narrow Purchase event pathway. It should currently be described as evidence-aware Meta visibility and integration—not cross-channel attribution, complete ROAS, causal intelligence, privacy certification, or automated advertising optimization. TikTok reporting, live provider acceptance, complete commerce outcome linkage, and realized merchant value remain unverified or absent.
