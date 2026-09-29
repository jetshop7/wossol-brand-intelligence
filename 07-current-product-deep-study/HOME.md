# HOME — Current Product Deep Study

Status: Current-product evidence study for Brand / Marketing / Website source of truth.
Source: `jetshop7/wossol-platform`, branch `dev/wossol-integration`.
Scope: current implemented Home behavior only. Future vision is intentionally excluded.

## 1. Product role

Merchant Home is not merely a KPI dashboard. Its current role is to compress operational attention:

**Open Wossol → see what needs attention → understand current work → see selected recommendations → move directly to the relevant action.**

## 2. Current feature truth and merchant value

### Needs Attention
Home surfaces non-zero merchant-action priorities rather than forcing the merchant to inspect several sections:
- Failed delivery cases — HIGH.
- Customer-risk / blocked-customer cases — HIGH.
- Orders waiting for stock — MEDIUM.
- Confirmation cases requiring attention — MEDIUM.

Each item deep-links to the relevant filtered Orders view.

**Merchant value:** less monitoring, hunting and manual filtering before useful work begins.

### Waiting-for-stock impact
The Home contract can carry product/variant-level impact:
- product and variant identity,
- affected Orders,
- directly unblockable Orders,
- total demand,
- effective available quantity when known,
- shortage quantity when known.

**Merchant value:** moves from “there is a stock problem” toward **problem → cause → operational impact**.

### Recommended Actions
Home consumes the bounded canonical recommendation projection from Analytics/Decision Center when the merchant has Analytics access. Recommendations retain their canonical priority/order and link to the relevant destination.

If Analytics recommendations are unavailable, Home preserves core Orders information and marks recommendations unavailable rather than fabricating a successful empty source.

**Merchant value:** selected analysis can reach the merchant's starting surface instead of requiring a separate search through Analytics.

### Today
Shows:
- Orders created today,
- Confirmed today,
- Delivered today.

The links preserve the correct event/status/date filters.

“Today” follows the selected Workspace timezone rather than an arbitrary server day.

### Current Work
Shows current operational workload such as:
- in Confirmation,
- in Delivery.

These link to the corresponding Orders scopes.

### Performance Snapshot
Supports TODAY / 7 / 14 / 30-day views.

Confirmation performance is derived from the latest qualifying Confirmation outcome per Order in the selected period, rather than simply reading the Order's current status.

Delivery performance distinguishes terminal outcomes from delivery-attempt failures rather than treating every attempt failure as a final outcome.

**Merchant value:** a more faithful operational reading than a superficial current-status snapshot.

### Recent Updates
Shows bounded Workspace-scoped notifications. Notification failure does not make the whole Home fail.

### Scope and permissions
Home respects:
- merchant identity,
- permitted Workspace,
- permitted Store / All permitted Stores,
- section access,
- Workspace timezone.

Unavailable or unauthorized data is not presented as a misleading zero.

### Quick Actions
The surface exposes direct entry points for recurring merchant actions according to available permissions, reducing navigation before execution.

## 3. Merchant work removed or reduced

Home reduces:
- checking multiple sections to discover problems,
- manually filtering Orders to find affected work,
- reconstructing which operational area requires attention,
- repeated navigation before common actions,
- opening Analytics only to discover selected recommendations,
- interpreting some stock-blockage impact manually,
- ambiguity about Store/Workspace/time scope.

A useful value label is:

**Attention Compression — اختصار الانتباه**

Wossol reduces not only clicks, but the amount of operational scanning the merchant must perform before knowing where intervention matters.

## 4. Control, transparency and trust

Home supports control through direct links from signal to action.

It supports transparency by preserving scope, timezone, historical event logic and explicit unavailable states.

It supports trust by avoiding fabricated zeroes when evidence is unavailable and by keeping optional subsystem failures from destroying the core operational view.

## 5. Cross-section compound value

Home demonstrates value created by connections between:
- Orders → operational priorities,
- Orders + Inventory → waiting-for-stock impact,
- Analytics → recommendations,
- Confirmation history → performance,
- Tracking/Delivery history → performance,
- Notifications → recent operational changes,
- permissions + Workspace + Store → correctly scoped view.

The value is therefore not the existence of dashboard cards. It is the **cross-domain prioritization and routing** built on those sources.

## 6. Strong product / brand patterns

1. **Prioritization** — surface what deserves attention.
2. **Attention Compression** — reduce monitoring work.
3. **Problem → Cause → Impact** — explain selected operational blockage rather than only reporting it.
4. **Truthful Historical Measurement** — use qualifying events where current status would distort the story.
5. **Direct Action** — signals route to the place where work can be done.
6. **Scope Clarity** — Workspace, Store, permissions and timezone remain explicit.
7. **Graceful Reliability** — secondary-source failure does not erase core Home value.
8. **Connected Context** — Home receives useful context from several domains without pretending to own all domain truth.

## 7. Marketing & Website Extraction

Possible website value narratives, subject to later copywriting:

### Start with what needs you
Instead of checking Orders, Confirmation, Delivery and Inventory one by one, Wossol surfaces selected operational cases that require attention and routes the merchant to the relevant work.

### Understand selected blockers, not only counts
For waiting-for-stock cases, Wossol can connect affected Orders with Product/Variant and availability context to show selected impact of the shortage.

### Move from signal to action
Operational priorities, selected recommendations and performance views link into the relevant workflow instead of ending as passive dashboard numbers.

### Performance based on what happened
Selected Confirmation and Delivery measures use operational event history and the chosen Workspace/Store/time scope rather than relying only on a current-status snapshot.

### One operational starting point
Home combines today's movement, current work, priorities, selected recommendations and recent updates into a scoped starting view.

## 8. Website / AEO evidence notes

For future website content, explain the merchant problem and outcome alongside the feature. Prefer explicit, indexable language over vague claims.

Good structure:
**Problem → Wossol capability → merchant benefit → concrete example/proof.**

Avoid claiming that Home is:
- an AI command center,
- autonomous commerce management,
- complete business intelligence,
- full financial reporting,
- causal diagnosis,
- a replacement for every operational system.

## 9. Brand relevance

Home supports the broader Wossol identity principles already identified:
- Trust,
- Safety / reassurance,
- Clarity,
- Control,
- Connection,
- Effort Compression.

A particularly important addition from Home is **Attention Compression**: the system should increasingly bring important work to the merchant rather than making the merchant continuously search for it.

## 10. Design implication for later UI/identity work

The functional intelligence of Home is currently stronger than its visual expression. Future UI work should make priority, cause, impact, freshness, scope and action relationships easier to perceive without increasing visual complexity.

This is a later design task, not a change to current product truth.
