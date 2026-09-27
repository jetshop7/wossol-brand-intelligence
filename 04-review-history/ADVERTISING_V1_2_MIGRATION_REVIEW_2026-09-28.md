# Advertising V1.2 Migration Review — 2026-09-28

## Review metadata
- Section: Advertising
- Reviewed intelligence commit: `2db309f2a6f328a8ad1a67b89210396c8e837480`
- Product evidence commit: `ac51f387bc65ab33d5827074a787ac6384609161`
- Current Product HEAD additionally checked: `80b6393cafd652ea6e731cb3d074f8989e339a74`
- Prior authoritative review: `04-review-history/ADVERTISING_REVIEW_2026-09-26.md`
- Methodology: V1.2
- Reviewer: ChatGPT — Strategic Reviewer / Quality Gate
- Decision: **ACCEPT WITH OPEN PRODUCT ISSUES**
- Full re-audit required: No
- Retroactive queue: RR-V12-007 completes V1.2 Quality Gate

## Prior accepted truth preserved

The migration preserves the accepted Advertising boundaries:
- Meta reporting is a real persisted provider-reporting subsystem;
- exact Wossol acquisition evidence is separate from provider-native conversion reporting;
- Meta CAPI Purchase is a bounded server-side dispatch path, not proof of provider acceptance or campaign lift;
- TikTok remains connection/authorization only for the reviewed capability surface;
- no campaign/budget/bid/creative mutation or autonomous optimization loop is established;
- privacy/legal basis, live provider acceptance, production freshness and complete attribution remain unresolved.

The prior V1.1 acceptance remains valid historical evidence and is not silently upgraded into stronger V1.2 claims.

## Product-state check

Director comparison confirms that Product changes after `ac51f387...` through current committed HEAD `80b6393...` are confined to Shopify Upsell files/tests and do not alter the Advertising/Messaging authority paths reviewed here.

Therefore `ac51f387...` remains a valid current-source snapshot for the Advertising V1.2 findings at the checked Product HEAD.

## V1.2 merchant job / friction reduction

The migration identifies two defensible bounded reductions in merchant/operator work.

### 1. Meta One Connect
One Meta authorization entry can discover both:
- Advertising Ad Accounts;
- eligible Facebook Pages for a separately permissioned Messaging setup.

This can reduce repeated OAuth/setup steps when both domains are configured.

The value is real but bounded:
- Page selection is explicit;
- Messaging provisioning remains separate;
- Advertising and Messaging permissions remain distinct;
- a healthy Advertising connection can survive Page-side failure;
- no measured setup-time reduction or live Meta acceptance is established.

This is setup consolidation, not authority consolidation.

### 2. Messenger referral provenance into Orders
When an eligible Messenger capture carries exactly one Meta ADS referral, current source can preserve that Ad identity through canonical Order creation and resolve it against the scoped Advertising graph.

This can reduce later manual rematching/reconstruction of the captured Ad identity.

It does not reduce the harder interpretive work of determining causality, incrementality, or campaign effectiveness.

## Tool / process consolidation

Advertising consolidates selected Meta connection, persisted reporting, URL-tag readiness, explicit Product/Variant mappings and bounded acquisition evidence in Wossol.

Meta One Connect further reduces duplicated authorization setup across Advertising and Messaging.

It does not replace:
- Ads Manager;
- full attribution tooling;
- experimentation/measurement systems;
- privacy/consent governance;
- cross-platform advertising operations;
- campaign execution tooling.

## Meta One Connect authority boundary

Director source verification confirms that the V1.2 interpretation is correct.

The Advertising OAuth entry requires `advertising.connections.manage`.

Meta asset discovery independently inspects Ad Accounts and Messenger Pages. Discovery failure on one asset family does not automatically invalidate the other.

Selected Facebook Page provisioning is delegated into the Messaging onboarding subsystem rather than being absorbed into Advertising ownership.

Messaging retains its own scoped connection/credential state and its own management authority.

Therefore the safe statement is:

**one authorization/setup entry can reduce repeated Meta setup, while Advertising and Messaging retain separate authority and runtime ownership.**

Do not describe this as one merged permission model or one merged integration authority.

## Messenger referral → Advertising graph boundary

Director source verification confirms that `resolveMessengerReferralAd`:
- accepts one provider Ad external ID;
- searches only within the current Workspace/Merchant scoped Meta Advertising graph;
- requires a unique matching Ad entity;
- follows only canonical `PRIMARY_PARENT` relationships for Ad Set/Campaign;
- performs no provider lookup or fuzzy/best-effort hierarchy inference;
- returns unresolved evidence when exact identity is unavailable.

This is strong provenance/integrity behavior.

It establishes that Wossol can say, in the qualified case:
**the captured Messenger referral identity matched this known scoped Meta Ad graph.**

It does not establish:
- conversation-level provenance;
- freshness-qualified attribution;
- multi-touch winner selection;
- ad causality;
- incrementality;
- campaign credit.

The upstream Messaging gaps remain controlling: no conversation/thread identity, no referral freshness window, discarded provider `ref`, and bounded capture semantics.

## Orders / delivery / economic continuity

Advertising provenance can survive into a canonical Order.

After that point, owner domains remain distinct:
- Orders owns lifecycle and attribution history;
- Tracking/Delivery owns delivery evidence;
- Finance owns human collection and ledger evidence;
- Inventory owns FIFO cost evidence;
- Analytics combines selected evidence under its own population/calculation rules.

This is a useful connected chain, but it is not a closed Ads-to-profit system.

The strongest current continuity is:

**provider Ad identity / reporting evidence → exact-or-unresolved Wossol attribution evidence → canonical Order → downstream owner-domain outcomes → bounded Analytics synthesis.**

The chain preserves source identity, but outcome closure remains incomplete.

## Provider metrics vs Wossol outcomes

The migration correctly preserves a key distinction:
- provider-reported Meta Results are provider-native evidence;
- exact Wossol outcomes are Wossol-side canonical Order evidence;
- delivered/collected/economic outcomes belong to later owner domains.

These populations must not be merged into one “true ROAS” number without compatible denominator/scope/outcome evidence.

This remains especially important because:
- Analytics has a separate Test Order population issue;
- Inventory COGS and Finance collections come from different authorities;
- Messenger referral evidence lacks a freshness contract.

## Decision effort reduction

Advertising reduces some decision-preparation effort by placing:
- provider reporting;
- exact acquisition evidence;
- mapping/readiness context;
- bounded downstream Wossol evidence

closer together.

It can help the merchant distinguish “what Meta reported” from “what Wossol exactly linked.”

It does not currently establish:
- why a campaign worked;
- what budget should change;
- which creative/audience should change;
- whether an Ad caused incremental profit;
- what automated action should execute.

Decision effort reduction is therefore bounded to evidence reconciliation/inspection, not Decision Intelligence.

## Intelligence depth

Current Advertising reaches:
**provider data → persisted reporting → scoped identity resolution → bounded connected Order provenance → downstream analytical consumption.**

It does not reach:
- causal interpretation;
- recommendation validation;
- campaign action;
- outcome learning;
- autonomous optimization.

Advertising therefore contributes Merchant/Marketing evidence and connected provenance, not a complete advertising intelligence or learning system.

## Verification assessment

The migrated audit correctly records that the new selected Product test run did not complete because the runner failed with stack overflow / closed pipe.

No V1.2 test pass is claimed.

This is acceptable for the intelligence migration because:
- the prior V1.1 Advertising verification remains historical evidence;
- the V1.2 delta is directly source-verifiable;
- Product `ac51f387...` is canonical and the later committed Product delta does not touch the reviewed Advertising/Messaging paths;
- the artifact transparently preserves the missing fresh test verification.

The test-run failure remains a verification limitation, not evidence of a Product defect.

## Claims strengthened / weakened / unchanged

**Strengthened:** Meta One Connect is valid evidence of reduced repeated setup across separate Advertising/Messaging domains.

**Strengthened:** Messenger referral → exact scoped Advertising graph → Order is real provenance continuity.

**Strengthened:** Advertising participates in a broader cross-domain evidence chain without owning downstream outcome truth.

**Unchanged:** no complete attribution, causal ROAS, live provider acceptance, automated optimization, TikTok analytics or learning loop.

**Bounded:** an exact Ad match is identity evidence, not conversion credit or causality.

## Open Product issues

1. Verify live Meta OAuth scopes, asset discovery, Page provisioning and partial-failure behavior in production.
2. Define/verify referral freshness/window semantics upstream in Messaging.
3. Preserve conversation/referral provenance if stronger attribution claims are desired.
4. Establish deployed reporting freshness/revision behavior and account-local date correctness.
5. Verify Meta CAPI receipt/acceptance, diagnostics, event match quality and production behavior.
6. Establish privacy/legal basis, consent, retention/deletion and provider-policy governance.
7. Quantify attribution coverage/tag survival and unresolved rates.
8. Maintain explicit provider-vs-Wossol population semantics in Analytics.
9. Resolve Analytics Test Order policy before broad advertising-performance claims.
10. Preserve Finance collection vs Inventory COGS ownership in economic projections.
11. Repair/diagnose the selected Product test runner stack-overflow/pipe failure so V1.2 delta tests can become repeatable.
12. Competitive distinctiveness remains unverified.

## Claim / marketing safety

Safe current framing:

**Wossol can authorize Meta for Advertising, optionally use the same authorization journey to discover/select Facebook Pages for a separately permissioned Messaging setup, persist Meta reporting, and preserve an exact Messenger Ad referral on a canonical Order when it resolves uniquely to the scoped Meta Ad graph.**

A safe value framing is:

**Wossol reduces some repeated Meta setup and keeps selected advertising evidence connected to later Wossol operational records without pretending provider metrics and Wossol outcomes are the same truth.**

Do not claim:
- one merged Advertising/Messaging authority;
- full or causal attribution;
- every-order attribution;
- true/complete ROAS;
- ad-to-profit closure;
- incremental conversion;
- real-time reporting;
- Meta-accepted Purchase events;
- campaign mutation/optimization;
- AI learning;
- TikTok analytics;
- privacy certification;
- measured setup/productivity gains;
- competitive superiority.

## Strategic / brand implication

Advertising strengthens the emerging working hypothesis around:
- Market/Acquisition Access;
- Reduced Merchant Work;
- Context Continuity;
- Connected Commercial Truth.

Its strongest current strategic role is not “ad optimization.” It is:

**selected provider advertising evidence can remain connected as the commercial workflow moves into Wossol, while downstream operational/economic authorities remain distinct.**

That is useful Brand Evidence for a connected-system story, but not sufficient to position Wossol as an advertising intelligence platform.

## Methodology impact

No methodology change required.

V1.2 correctly forces separation of:
- shared setup from shared authority;
- identity resolution from attribution;
- attribution from causality;
- provider Results from Wossol outcomes;
- operational outcomes from economic truth;
- connected evidence from Decision/Learning Intelligence.

## Retroactive impact

RR-V12-007 has completed its V1.2 Quality Gate.

Messaging remains the owner of referral/capture semantics. Orders remains canonical Order authority. Tracking/Delivery and Finance retain downstream outcome authority. Inventory retains COGS provenance. Analytics owns any combined decision-support projection.

No previously accepted section requires correction because the relevant boundaries are already preserved as open Product issues.

## Final decision

**ACCEPT WITH OPEN PRODUCT ISSUES.**

Advertising is V1.2-complete for intelligence purposes. No Codex correction or re-audit is required before proceeding to Integrations / Commerce Channels.
