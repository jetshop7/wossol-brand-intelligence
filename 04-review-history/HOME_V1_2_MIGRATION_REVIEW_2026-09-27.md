# Home V1.2 Migration Review — 2026-09-27

## Review metadata
- Section: Home
- Reviewed intelligence commit: `1dd6972`
- Product evidence: current committed source checked at `fd7157fe1a6ee03499e026373f263c3a15c831ef`; HOME.md also records local inspected Product state `855ee94...`
- Prior authoritative review preserved: `04-review-history/HOME_REVIEW_2026-09-25.md`
- Methodology: V1.2
- Reviewer: ChatGPT — Strategic Reviewer / Quality Gate
- Decision: **ACCEPT WITH OPEN PRODUCT ISSUES**
- Full re-audit required: No
- Retroactive queue item: RR-V12-013 → may be marked REVIEWED - NO MATERIAL CHANGE / V1.2 ACCEPTED (with the material V1.2 additions preserved)

## Prior accepted truth preserved

The migration preserves the prior accepted executable truth and, critically, does not erase the unresolved final-functional-spec contradiction. Current executable Home remains narrower than `MERCHANT_HOME_SHELL_UI_SPEC.md`: the current-looking final specification still requires broader Today/trend/current-work/update behavior that P1/P2 do not fully implement. The audit correctly preserves both possible resolutions rather than treating code as proof of supersession.

The prior strategic boundary also survives: Home is a scoped operational composition/orientation surface, not a standalone intelligence engine, Finance dashboard, owner of downstream domains, or complete command center.

## Product truth changed / newly discovered

No evidence reviewed requires overturning the prior accepted core Home truth. The V1.2 pass does, however, expose two material connected-system truths that were not sufficiently represented before:

1. **Conditional Analytics side effect behind Home Summary GET.** Home calls Analytics-owned recommendation retrieval; the current Analytics path can materialize canonical recommendation records and publish `recommendation.generated` when new evidence exists. Home has no direct write, but the end-to-end GET path is not proven side-effect-free.
2. **Inherited Notifications authorization/projection concern.** Home consumes the Notifications list API. That API rechecks Workspace membership at read time but does not recheck source-category/domain permission for each persisted notice after permission revocation, and Home receives a broader row than it displays.

Both are correctly recorded as owner-domain/contract issues rather than inflated into Home-owned capabilities.

## V1.2 value newly extracted

The migration materially improves the merchant-value interpretation.

Home's defensible current value is **orientation and action routing with preserved operational semantics**, not decision intelligence. It reduces first-look scanning across Orders/Confirmation/Delivery/Inventory/Notifications and can route the merchant into the owner workflow using exact filters/links. It preserves Workspace time, Store scope and event-history semantics rather than rebuilding shallow dashboard numbers.

The work reduction is therefore primarily:
- reduced operational scanning and navigation;
- reduced need to reconstruct today's/historical operational state from multiple owner pages;
- reduced risk of interpreting later current status as historical outcome truth;
- bounded coordination/attention reduction through prioritized operational rows and recent notices.

This is qualitative evidence. No measured time saving, productivity gain or loss reduction is established.

Tool/process consolidation remains partial: Home composes owner-domain truth but does not replace those systems, provider portals, spreadsheets or a full analytical workflow.

## Connected-domain / section-island review

The V1.2 audit correctly inspects the material joins:
- Orders supplies created/current-state/history truth and exact deep-link predicates;
- Confirmation and Tracking history support historical outcome semantics;
- Inventory contributes cautious waiting-stock context without promising unblock;
- Analytics owns recommendation calculation/materialization and any higher decision layer;
- Notifications supplies bounded attention evidence but carries its own authorization/projection weaknesses;
- Finance remains deliberately excluded.

The operational → economic → decision chain is incomplete at Home. Home has operational truth and bounded guidance distribution, but no Home-owned economic reconciliation, causal interpretation, predicted impact, execution loop, outcome measurement or learning loop.

## Claims strengthened / weakened / unchanged

**Strengthened:** Home is useful evidence for Wossol's ability to preserve domain truth while reducing operational scanning and routing merchants to the owning workflow.

**Unchanged:** Home alone is not a differentiated intelligence proposition; recommendation quality belongs to Analytics; Finance is excluded; competitive superiority is unproven.

**Weakened / newly bounded:** “Home is wholly read-only/no persistent effects” is unsafe end-to-end because recommendation retrieval can conditionally materialize Analytics state/events. “Fully merchant-safe recent updates” is also too strong until Notifications read-time category authorization/projection policy is resolved.

## Open product issues

1. Reconcile the final Home functional specification with executable Home.
2. Decide whether Home Summary must be end-to-end side-effect-free or whether Analytics recommendation materialization on read is intended; document/test the chosen contract.
3. Review Notifications category-permission revocation semantics and minimize the Home notification projection where appropriate.
4. PostgreSQL-specific Home verification remains unexecuted in this migration because its database-name guard blocked the test before DB access.
5. Production/runtime performance, representative merchant usefulness and measured effort reduction remain unverified.

## Claim / strategic safety

Safe current framing: Home gives a scoped operational starting point that preserves owner-domain truth and routes merchants toward current work and bounded guidance.

Do not claim a complete command center, full decision intelligence, financial health, causal explanation, automated action, measured productivity improvement, guaranteed permission-current notification safety, or a side-effect-free read path.

## Marketing / demo consequence

Home is stronger as **supporting proof** of a broader connected-control proposition than as a hero feature. A legitimate demo can show: scoped Today/history facts → attention item → exact owner-workflow route → bounded Analytics recommendation when authorized. The proof is continuity and truthful routing, not an “AI dashboard.”

## Methodology impact

No V1.2 methodology change is required. The new findings demonstrate the intended section-island and transitive-side-effect checks.

## Retroactive impact

RR-V12-013 has completed its V1.2 Quality Gate. Preserve the newly extracted value and open product issues. No prior accepted section is invalidated; Notifications and Analytics should carry these joins into their own V1.2 migrations.

## Final decision

**ACCEPT WITH OPEN PRODUCT ISSUES.**

Home is V1.2-complete for intelligence purposes. The unresolved Product issues are accurately bounded and do not require another Home correction before moving to the next migration section.
