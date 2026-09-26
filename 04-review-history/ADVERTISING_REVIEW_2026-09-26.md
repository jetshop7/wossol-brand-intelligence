# Advertising Review — 2026-09-26

## Review metadata
- Section: Advertising
- Reviewed intelligence commit: `41dfc5ada0a19ca52b71be159eb4b90cc40c4dfc`
- Product evidence commit: `8600a4cbd1a894579a057b3476db35465289c670`
- Reviewer: ChatGPT — Strategic Reviewer / Quality Gate
- Decision: **ACCEPT WITH OPEN PRODUCT ISSUES**
- Full re-audit required: No

## What passes

The audit correctly separates three materially different evidence layers: provider-native Meta reporting, exact Wossol acquisition evidence, and server-side Meta Purchase dispatch. It does not collapse them into one attribution or optimization claim.

Targeted Product verification confirms substantial Meta reporting implementation: authorized account scope, persisted daily reporting slices, provider timezone/currency context, bounded pagination, explicit completeness/error states, and provider-native conversion evidence. This establishes a reporting subsystem, not live freshness, causal attribution or complete commerce outcome measurement.

The acquisition boundary is appropriately conservative. Wossol attribution depends on exact persisted acquisition evidence and scoped canonical provider identities; Product/Variant mappings do not substitute for acquisition evidence. Provider-reported conversions remain distinct from Wossol Orders.

The CAPI boundary is also correct. Current P1 materializes eligible order-created events into a durable dispatch pipeline with stable identity, leases/retries and bounded payload handling. That proves an implemented send path, not Meta acceptance, event matching, attribution credit, campaign lift or optimization effectiveness.

TikTok is correctly classified as limited connection/authorization capability. Current P1 establishes OAuth and authorized advertiser discovery, but no equivalent TikTok performance reporting or optimization subsystem was established.

Merchant control is accurately bounded. The merchant can authorize connections, choose/use scoped connections, manage mappings and activate the Meta conversion destination, but no campaign, budget, bid, audience or creative mutation path was established.

The audit correctly retains privacy/legal uncertainty. Hashing selected identity fields makes them pseudonymized, not anonymous, and source implementation alone does not establish consent, lawful basis, retention compliance or provider-policy compliance.

Cross-section discipline passes. Analytics may combine Advertising evidence with Wossol outcomes under its own rules, and Finance owns collection/cost facts. Advertising itself therefore does not establish true ROAS, profit attribution or an end-to-end ads-to-profit loop.

Verification discipline passes. The audit records 191 focused Advertising tests passed, one PostgreSQL-specific test skipped, and both typechecks passed without converting those results into production/provider proof.

## Open product issues retained

1. **Live Meta authorization/runtime:** approved scopes, supported account conditions, deployed sync success and real provider behavior remain unverified.
2. **Reporting freshness/revisions:** hourly scheduling does not establish completion SLA, account-local day correctness or complete late-revision reconciliation.
3. **CAPI provider acceptance:** code-path dispatch is established, but Meta receipt/acceptance, diagnostics, event match quality and downstream effect are not.
4. **Privacy/legal governance:** consent/lawful basis, retention/deletion and governance for transmitted hashed identity/event data remain unverified.
5. **Attribution coverage:** exact evidence is intentionally conservative, but real-world tag survival and the proportion of eligible Orders that resolve exactly are unknown.
6. **Provider breadth:** TikTok remains authorization-only for the reviewed surface; Google/Snap reporting adapters were not established.
7. **Optimization closure:** no campaign mutation, causal evaluation, automated learning or closed decision-action loop is established.
8. **Database concurrency/runtime:** the PostgreSQL-specific coordination test was skipped and no deployed database concurrency behavior was verified.

These issues materially bound Advertising claims but do not invalidate the intelligence audit.

## Claim / strategic safety

Safe present territory is Meta connection, persisted Meta reporting, exact-evidence-limited Wossol acquisition linkage, explicit Product/Variant mappings, URL-tag readiness evidence, and a bounded server-side Meta Purchase dispatch pathway.

Do not claim all-channel advertising intelligence, complete attribution, true/causal ROAS, every-order attribution, real-time reporting, automated optimization, AI learning, Meta-accepted/matched conversions, guaranteed campaign improvement, TikTok analytics, or broad privacy/compliance certification.

## Methodology impact

No methodology change is required.

## Retroactive impact

No prior accepted section requires correction. Analytics / Decision Center must continue distinguishing provider-reported conversions, exact Wossol acquisition evidence and Finance-backed outcomes. Later Integrations/Commerce Channels review should preserve the difference between an authorized provider connection and a complete operational/data integration.

## Acceptance

**Advertising passes the Director Quality Gate and is accepted with the open Product issues above.**

No Codex correction or re-audit is required.

The next section in the canonical sequence is **Integrations / Commerce Channels**.
