# Wossol Positioning Territory Test — Pre-validation V1

**Status:** Evidence-pruning input for merchant validation; no positioning winner, final category, promise, or copy selected.
**Prepared:** 2026-09-29.
**Merchant Value Validation:** **PENDING PRIMARY RESEARCH**; repository records no accepted merchant findings.
**Director gate:** Positioning Pre-Validation Quality Gate pending.

## 1. Evidence boundary and source state

This pre-validation uses accepted Product/Marketing/Brand synthesis, accepted section evidence, the V1.2 competitive refresh, Global Category Framing V1, Future Product Ambition V1 and the Director’s Merchant Value Validation Program. It tests internal coherence and claim/proof readiness; it does not substitute Product evidence or public competitor claims for merchant experience. No merchant importance, problem frequency, language resonance, switching intent, willingness to pay or category comprehension is scored as known.

**Product reference state checked:** `jetshop7/wossol-platform`, `dev/wossol-integration`, committed HEAD `f8a4b22cd1d6d66d93964e7ec5b16f78d70df2bd`, matching `origin/dev/wossol-integration`. The working tree is **not clean**: local edits affect `apps/backend/src/modules/shopify/shopify-order-projection.service.ts`, its spec, `docs/PROJECT_EXECUTION_CONTROL.md`, and `docs/engineering/ENGINEERING_CHANGE_LOG.md`; `apps/backend/tmp-mooncat-graph-trace.ts` is also untracked. The local change explicitly whitelists Shopify projection persistence fields and adds a regression test; Product’s local execution note says one real completed-Shopify-Order/Thank-You retest remains pending. These changes were not made by this task, were preserved, and are not treated as committed or live-verified proof. Their exact impact is bounded to Shopify projection reliability; this pre-validation does not reopen the Shopify or Product audits.

**Canonical inputs:**

- `03-master-synthesis/WOSSOL_PRODUCT_ADVANTAGE_MASTER.md` (accepted product advantage hierarchy / PA-01–06 / CA-01–08).
- `03-master-synthesis/WOSSOL_MARKETING_ASSET_MASTER.md` (MA-01–18, proof points, demo sequences, message territories and claim limits).
- `03-master-synthesis/WOSSOL_BRAND_STRATEGIC_EVIDENCE_MASTER.md` (BT-01–04, EBT-01–03, current/future evidence hierarchy).
- `01-competitive/WOSSOL_COMPETITIVE_DEPTH_REFRESH_V1_2.md` (public claims/artifacts/contracts; rival depth and outcomes remain unverified).
- `03-master-synthesis/WOSSOL_GLOBAL_CATEGORY_FRAMING_V1.md` and `WOSSOL_FUTURE_PRODUCT_AMBITION_V1.md`.
- `04-review-history/GLOBAL_CATEGORY_FUTURE_AMBITION_REVIEW_2026-09-29.md` (accepted as input with open Product documentation issue; no final category/positioning).
- `05-director/MERCHANT_VALUE_VALIDATION_PROGRAM_V1.md`, `06-merchant-research/README.md`, and empty `POSITIONING_TERRITORY_VALIDATION_INPUT.md`.

The Product Advantage/Marketing/Brand masters cite earlier accepted evidence heads, whereas Global Category/Future Ambition reference Product HEAD `f8a4b22…`. This document preserves their accepted V1.2 conclusions and does not claim a new comprehensive Product audit. The local Shopify patch above is disclosed as a bounded, unaccepted delta.

## 2. Executive pre-validation finding

The five starting labels do not represent five equally independent, equally ready positioning territories.

- **Connected Commercial Truth** is a well-supported description of a Product mechanism/system behavior, but on its own is abstract and not shown to be a merchant’s own category of pain.
- **Less Work Between the Work** expresses the most direct merchant-job hypothesis (less re-entry, rematching, searching, chasing, exporting and reconciliation). Product proof is repeated but qualitative and unmeasured; importance and wording remain unvalidated.
- **Control without Fragmentation** is a distinct trade-off hypothesis about retaining consequential agency while context crosses workflows. Product supports bounded agency; public competitors also claim dashboards, visibility, configurability and control, so superiority is unproven and merchant preference may segment by operating model.
- **Evidence before Answers** is better treated as a product/brand behavior and proof principle—show source, scope and uncertainty before overclaiming—than as a standalone buyer-facing territory until merchants identify this as a salient job.
- **From Operations to Better Decisions** is a credible emerging direction, but it depends on bounded recommendations today and future outcome measurement/learning tomorrow. It is not ready to carry a current core promise.

The clearest collision is that Connected Commercial Truth describes **how** selected context/evidence stays related, while Less Work Between the Work describes a possible **consequence** of that mechanism. They should be compared both separately and as one mechanism-plus-consequence territory during research. Control without Fragmentation should remain a separate candidate because it concerns authority/delegation trade-offs, not only continuity. Evidence before Answers should inform proof and product behavior across the candidates. Better Decisions should be tested as a secondary/emerging territory, with current bounded proof separated from future ambition.

This narrows the set for merchant inquiry; it does **not** identify a winner. Commerce Operations remains best treated as the accepted **category context/descriptor hypothesis**, not itself a positioning territory or differentiated claim.

## 3. Test method

For each candidate, this document assesses product truth fit, hypothesized merchant job/consequence, competitive distinctiveness, category comprehension risk, demonstrability, claim safety, future extensibility, international portability, brand depth, and falsification conditions. Judgments are categorical, not numeric or weighted. Product proof can be strong while merchant resonance remains unknown.

Evidence-status terms used below:

- **STRONG CURRENT EVIDENCE** — repeated Product behavior/proof supports the underlying mechanism today.
- **SUPPORTED BUT BOUNDED** — current proof exists, but important scope, outcome, reliability or deployment limits remain.
- **POTENTIAL / COMPETITOR DEPTH UNVERIFIED** — plausible distinction, not demonstrated relative to competitors.
- **UNVALIDATED — PRIMARY RESEARCH REQUIRED** — merchant importance, wording, frequency, consequence or preference is unknown.
- **WEAK / CONTRADICTED** — the promise is broader than current evidence or directly conflicts with constraints.
- **FUTURE ONLY** — depends on capabilities not established as current Product truth.

Interviews must begin with unaided recent-behavior discovery, not these labels. Candidate territories are internal coding hypotheses; do not present them as pitches until real workflow stories and alternatives have been collected. Follow the approved validation program (12–18 qualitative merchant/operator interviews initially, varied operating models, 45–60 minutes, behavior-led; continue until relevant patterns stabilize rather than treating the target count as statistical saturation). Do not populate the existing validation input with conclusions before interviews are coded.

## 4. Territory dossiers

### Territory 1 — Connected Commercial Truth

| Dimension | Pre-validation finding |
|---|---|
| Product truth fit | **STRONG CURRENT EVIDENCE.** Repeated truth-boundary behavior connects selected source/commercial context to canonical Orders and downstream operational/economic records while keeping provider, operational, financial and calculated truth distinct. See PA-01/02/04, CA-01/02/05/06 and MA-01/02/04/05/10/13. |
| Merchant job / consequence hypothesis | **UNVALIDATED — PRIMARY RESEARCH REQUIRED.** Hypothesis: less re-entry, identity matching and reconstruction of what a status/number represents. Frequency, pain and consequence are unknown. |
| Competitive distinctiveness | **POTENTIAL / COMPETITOR DEPTH UNVERIFIED.** Competitors publicly claim connected operations and some expose/document order history, source fields or payout evidence. A specific chain with explicit authority and uncertainty boundaries may be worth comparing; no comparative superiority is established. |
| Category comprehension | **SUPPORTED BUT BOUNDED.** “Commerce operations” supplies a recognizable neighborhood; “commercial truth” is abstract/technical and could sound like a claim of perfect or universal truth. It describes a system quality more clearly than the buyer/job. |
| Proofability / demo | Current chains are demonstrable, subject to environment/data availability: supported Shopify COD or Messenger source → canonical Order → Confirmation/Tracking → Finance; expected vs available/reserved stock; delivered vs separately confirmed collection; exact or unresolved Ad identity. The local Shopify projection patch and pending live retest qualify one proof path. |
| Claim safety | Safe only as “selected context/evidence remains connected” or “preserves where selected facts came from.” Do not claim one source of truth for everything, complete attribution, audited truth, real profit, perfect data or competitor uniqueness. |
| Future extensibility | **STRONG FIT.** The behavior can extend from connected operations to bounded calculations/decisions and, only after valid outcome measurement, possible learning. Future scope does not upgrade today’s claim. |
| International portability | Vocabulary and truth-boundary behavior travel; market/provider, payment and legal coverage does not automatically travel. |
| Brand depth | Can support clarity, groundedness and accountability as hypotheses; emotional value is unvalidated. |
| Falsification conditions | Merchants do not notice/care about source continuity or truth distinctions; connected records do not remove a real task; truth qualifiers create confusion rather than confidence; workflow continuity proves no better than existing systems; current proof proves fragile in live use. |

**Pre-validation disposition:** retain for testing, but compare as a mechanism-led territory versus the consequence-led Less Work candidate. Do not assume this product-internal phrase itself is buyer language.

### Territory 2 — Control without Fragmentation

| Dimension | Pre-validation finding |
|---|---|
| Product truth fit | **STRONG CURRENT EVIDENCE, BOUNDED.** Human confirmation, scoped access, authorized policy configuration, explicit handoffs, merchant completion and owner-domain authority recur (PA-05; relevant MA-05/08/15/17). Wossol does not control external provider outcomes or every permission boundary. |
| Merchant job / consequence hypothesis | **UNVALIDATED — PRIMARY RESEARCH REQUIRED.** Hypothesis: keep ability to inspect, decide, correct, assign or intervene without manually carrying context between tools. Exact controls valued—and work merchants would rather delegate completely—are unknown. |
| Competitive distinctiveness | **POTENTIAL / COMPETITOR DEPTH UNVERIFIED.** Several providers claim merchant workspaces, dashboards, visibility, customization or control; exact approve/correct/hold/reroute/override/audit rights are often unknown. Managed execution may remove more work for some segments. |
| Category comprehension | Can clarify the tension between delegated execution and merchant agency. “Fragmentation” may be jargon, and “control” may imply authority over providers or universal workflow governance. |
| Proofability / demo | Show merchant-confirmed Finance recognition after delivery; explicit support handoff; bounded Workspace/Store policy; human confirmation and guarded transition. Show the authority boundary—not just a settings screen. |
| Claim safety | Use “selected actions/policies remain merchant-controlled” only with scope. Do not claim complete control, full permissions, provider control, superior autonomy or lower labor. |
| Future extensibility | **GOOD FIT** if future orchestration retains explicit authority and reversible action. Autonomous execution is long-term only and cannot be smuggled into the current story. |
| International portability | Control/delegation is portable, but local workflows, provider dependencies and role norms must be tested across markets. |
| Brand depth | Could support agency and confidence, but some merchants may prefer full-service delegation; do not infer emotional preference. |
| Falsification conditions | Primary research shows convenience of outsourcing dominates; relevant decisions cannot be exercised in Wossol; the workflows create more intervention burden; competitor controls are equal/deeper; “control” is misunderstood as provider authority. |

**Pre-validation disposition:** retain as a distinct candidate, explicitly testing merchant-controlled rights against preferred delegation—not as a generic “control” claim.

### Territory 3 — Less Work Between the Work

| Dimension | Pre-validation finding |
|---|---|
| Product truth fit | **STRONG QUALITATIVE CURRENT EVIDENCE.** Accepted synthesis repeatedly identifies reduced re-entry, rematching, locating/searching, cross-domain reconstruction, export/merge/calculation and reconciliation (PA-01/03/04/06; MA-01/02/03/05/06/07/12/14/15/16; Global Search proof). No time/error/savings quantification exists. |
| Merchant job / consequence hypothesis | **UNVALIDATED — PRIMARY RESEARCH REQUIRED.** Hypothesis: eliminate or reduce repeated manual work between systems, people and workflow stages. Actual frequency, cost, error, delay, cognitive load and priority are unknown. |
| Competitive distinctiveness | **POTENTIAL / COMPETITOR DEPTH UNVERIFIED.** Connected tools, imports, call centers, logistics and settlement may remove entire execution tasks. Wossol’s hypothesized niche is narrower: less merchant-side re-entry/reconstruction while retaining agency. Must compare the same real job against both self-managed tools and managed providers. |
| Category comprehension | Consequence-led and less architectural than “connected truth,” but the phrase may be poetic/vague. “Between the work” must be tested unaided; do not assume it is natural merchant language. |
| Proofability / demo | Strongest candidates: exact Product/Variant/Store → Order context; Global Search → owner workflow; Order → delivery/finance evidence; inbound declaration → receipt/availability boundary; Chat → Support draft; analytics evidence already assembled for bounded review. Each proves a workflow slice, not quantified time saved. |
| Claim safety | Qualitative “reduces the need to re-enter/re-match/reconstruct selected context” is supportable by named flows. No hours saved, headcount reduction, lower error rate, full tool replacement or complete workflow automation. |
| Future extensibility | **STRONG FIT.** More validated connectors and connected outcomes could extend the benefit; learning is not required for this territory to be true. |
| International portability | High conceptual portability beyond COD/Libya if validated across merchants with different stacks. Product readiness across those markets is a separate question. |
| Brand depth | Could translate into less operational burden/fragmentation; those feelings and business consequences remain unvalidated. |
| Falsification conditions | Merchants do not perform these tasks often; existing provider/service eliminates them; Wossol adds another system/reconciliation step; linked context is not trusted or usable; benefit is too small versus setup cost; phrase fails comprehension. |

**Pre-validation disposition:** retain for testing as the clearest job/consequence hypothesis. Compare it against Connected Commercial Truth both independently and as a combined mechanism → consequence proposition; no quantified benefit claim.

### Territory 4 — Evidence before Answers

| Dimension | Pre-validation finding |
|---|---|
| Product truth fit | **STRONG AS A PROOF PRINCIPLE; BOUNDED AS A PRODUCT PROMISE.** Wossol can preserve unknown/uncovered evidence, distinguish delivered from collected, distinguish identity from causality, and qualify Market signals; deterministic recommendations remain bounded (PA-02/06; MA-04/05/10/13/14). |
| Merchant job / consequence hypothesis | **UNVALIDATED — PRIMARY RESEARCH REQUIRED.** Hypothesis: avoid acting on a confident but misleading status, profit or recommendation. Salience and acceptable explanation depth are unknown. |
| Competitive distinctiveness | **POTENTIAL / COMPETITOR DEPTH UNVERIFIED.** Public competitor claims vary in evidence semantics, but limited access prevents claiming Wossol is more evidence-aware. Ximpli, COD Network and others publish profit/history/data claims; methods are not comparable. |
| Category comprehension | Does not state clearly what Wossol is or which everyday job it solves. “Before answers” may imply slower answers or no answers. Likely a proof/behavior standard, not the category bridge. |
| Proofability / demo | Missing FIFO cost remains uncovered; delivery remains distinct from Finance collection recognition; exact Messenger Ad identity can remain unresolved; Market Center signal is explicitly Wossol-observed and thresholded; recommendation is not auto-executed. |
| Claim safety | Avoid abstract “truth engine,” “evidence you can trust,” or accuracy superiority. Describe exact source/scope/unknown behavior. |
| Future extensibility | Strong behavioral guardrail for future recommendations and learning; it must persist if intelligence grows, but is not a claim of current intelligence. |
| International portability | Highly portable as a system behavior, subject to language clarity and local evidence practices. |
| Brand depth | Could express precision and groundedness; merchant emotional response is unvalidated. |
| Falsification conditions | Merchants prioritize fast answer over qualifiers; explicit unknowns frustrate without improving decisions; missing/incorrect data undermines the examples; rival products demonstrate equivalent or stronger evidence provenance. |

**Pre-validation disposition:** demote from standalone territory to **proof principle / product behavior** informing all surviving concepts. Reconsider as a territory only if merchants independently describe evidence quality/uncertainty as a priority problem.

### Territory 5 — From Operations to Better Decisions

| Dimension | Pre-validation finding |
|---|---|
| Product truth fit | **SUPPORTED BUT BOUNDED.** Analytics calculates selected metrics and comparisons and offers deterministic guidance, including a narrowly qualified Wossol-observed market recommendation. Recommendations do not execute; H-C04 does not mean national demand, causality, growth or profit prediction. Outcome measurement and learning are not current. |
| Merchant job / consequence hypothesis | **UNVALIDATED — PRIMARY RESEARCH REQUIRED.** Hypothesis: merchants spend significant effort assembling evidence before recurring decisions and want guidance. Which decisions, frequency, stakes and authority merchants would delegate are unknown. |
| Competitive distinctiveness | **POTENTIAL / COMPETITOR DEPTH UNVERIFIED.** Competitors publicly claim dashboards, profit metrics, benchmarks, AI and product/country insights. Their methods/outcomes are unverified; this does not create Wossol whitespace. |
| Category comprehension | Can describe an aspirational value arc, but may trigger assumptions of predictive/causal “decision intelligence.” It is not a substitute for a current category descriptor. |
| Proofability / demo | Show scoped calculations → deterministic threshold/eligibility → explanation/evidence → recommendation → merchant decision. Include no automatic execution and disclose missing measurement/feedback. H-C04 demonstration must preserve its Libya period/cohort/suppression caveats. |
| Claim safety | Current “helps prepare selected decisions using selected evidence” is qualified; “knows what to do,” “optimizes,” “predicts,” “learns,” “grows your business” and measured impact are unsupported. |
| Future extensibility | **STRONG STRATEGIC FIT; FUTURE STEPS GATED.** Need recommendation quality validation, explicit action identity, outcome definitions/baselines/windows, causal safeguards and repeatable measurement before learning claims. |
| International portability | General decision-preparation idea travels; current Market Center/coverage does not establish multi-market decision capability. |
| Brand depth | Could support confidence/foresight, but “better decisions” and business outcomes cannot be inferred from feature architecture; primary validation and outcome evidence required. |
| Falsification conditions | Merchants do not experience evidence-preparation burden; advice is unwanted/low utility; decision authority belongs to external provider; recommendation eligibility/quality is too narrow; no measured outcomes; competitors' demonstrated intelligence is materially deeper. |

**Pre-validation disposition:** demote as a current lead territory; retain as an **emerging/secondary research hypothesis** and future ambition. It cannot become the core through product architecture alone.

## 5. Cross-territory collision / overlap map

| Pair / relationship | Collision finding | Test treatment |
|---|---|---|
| Connected Commercial Truth ↔ Less Work Between the Work | Truth/context continuity is a mechanism; less work is a hypothesized merchant consequence. Strongest overlap and likely nested rather than independent. | Compare a mechanism-led territory, a consequence-led territory, and one combined mechanism→consequence articulation only after unaided workflow discovery. Ask which specific task disappeared and whether the mechanism matters independently. |
| Connected Commercial Truth ↔ Evidence before Answers | Both foreground evidence, provenance and correct interpretation. “Evidence before Answers” is a narrower behavioral quality that can make Connected Truth credible. | Treat evidence-before-answers as proof standard; test standalone only if merchants spontaneously cite misleading/unsupported numbers as a high-priority problem. |
| Control without Fragmentation ↔ Less Work Between the Work | Fewer handoffs may reduce effort, but agency is a separate preference/authority question. Control can add work for some merchants and remove uncertainty for others. | Ask which actions merchants must retain, what they delegate, and when control creates rather than removes burden. Segment by managed vs self-operated/hybrid model. |
| Control without Fragmentation ↔ Connected Commercial Truth | Shared context may make controlled handoffs usable, but preserved truth is not the same as decision rights. | Demonstrate both context and exact rights; do not use “connected” as evidence of control. |
| From Operations to Better Decisions ↔ Connected Truth / Less Work | Connected evidence and reduced assembly are prerequisites for decision preparation; they do not prove decisions improve. | Keep a visible progression: evidence assembled → interpreted → recommendation → action → measured outcome. Test each separately; no leap to learning. |
| From Operations to Better Decisions ↔ Evidence before Answers | Evidence quality constrains recommendation credibility, but bounded deterministic rules are not a full intelligence platform. | Test what proof justifies acting on a recommendation, what missing/unknown condition should suppress it, and acceptable explanation/authority. |

## 6. Territories rejected or demoted

No starting territory is rejected as a product behavior. The following are **not viable as current standalone positioning territories on the evidence now available**, or are demoted:

- **Evidence before Answers — demote to proof principle.** Stronger as a rule for how Wossol should show source, scope and uncertainty than as a buyer-recognizable category/job. Standalone viability requires spontaneous merchant evidence.
- **From Operations to Better Decisions — demote from current core to emerging/secondary hypothesis.** Bounded decision support is real, but impact is unmeasured and outcome/learning links are incomplete; public competitor claims make generic intelligence language non-distinctive.
- **Connected Commercial Truth — do not reject; narrow its role.** Treat as mechanism/system behavior and candidate test territory, not established merchant language or a simple category label.
- **Control without Fragmentation — do not reject; narrow and segment.** Test exact control rights against managed execution preferences. Broad “control” is generic and competitor claims overlap.
- **Less Work Between the Work — do not reject; constrain.** Best explicit job hypothesis, but “less work” and the phrase itself remain unvalidated and unmeasured. Compare with outsourcing alternatives, not only manual spreadsheets.

“Commerce Operations” is **not** promoted as a sixth territory: it is category context/descriptor to aid buyer orientation. “All-in-one,” COD-first, logistics-first, AI-first, complete OMS/ERP/WMS, intelligence-platform, global access, and learning/autonomy claims remain outside current positioning based on accepted boundaries.

## 7. Surviving candidates for merchant validation

Retain three **non-ranked** tests:

1. **Less Work Between the Work** — consequence/job-led hypothesis.
2. **Connected Commercial Truth** — mechanism/evidence-led hypothesis, to test both alone and combined with #1.
3. **Control without Fragmentation** — agency/delegation-tradeoff hypothesis.

Keep **From Operations to Better Decisions** as a secondary/emerging hypothesis to probe only after concrete decision stories; keep **Evidence before Answers** as a proof principle rather than a lead candidate unless it emerges unaided from merchant stories. This set is for interview coding and later concept-comprehension testing, not a shortlist of winners.

## 8. Category bridge relationship

The accepted Global Category Framing review supports **commerce operations** as the strongest descriptive bridge for comprehension testing, not a final category. Pre-validation recommends treating it as **category context/descriptor**, not as positioning and not as a territory by itself. In research, test whether it helps merchants locate the product and what they assume Wossol owns.

Do not equate “commerce operations” with a complete ecommerce platform or assume the phrase is self-explanatory. Contrast carefully against buyer interpretations of OMS, ERP, WMS, fulfillment platform, omnichannel and commerce OS. Record whether merchants infer storefront/checkout, all-channel synchronization, inventory allocation, physical fulfillment, accounting, payments, causal analytics or global coverage. Reject any bridge wording that requires a long architecture explanation or hides current market/provider limits.

## 9. Future ambition relationship

The future arc most consistent with accepted Product evidence is:

**deeper connected operations/evidence → bounded decision support → validated outcome measurement → only then possible learning.**

Territories 1 and 3 can remain meaningful without the later steps. Territory 2 may extend to stronger orchestration only if agency and external authority remain explicit. Territory 5 belongs mainly to the second step and cannot borrow credibility from outcome measurement or learning that is not current. Global expansion, market/network intelligence, financial services, sourcing and autonomous execution remain future/unsupported unless separately evidenced and approved.

The uncommitted local Shopify projection change with live retest pending is a proof-reliability caveat, not evidence for or against a broad territory. Future tests should demonstrate committed, repeatable source-to-outcome workflows rather than rely on a local patch or architecture diagram.

## 10. Merchant validation discriminators

The strongest unresolved discriminator is **whether the repeated context/reconciliation work is frequent and consequential enough to matter more than fully delegating execution—and whether merchants describe that problem in their own language**. Product architecture cannot answer this.

Use recent-event prompts from the validation program, then code evidence for H1–H8 without prompting territory labels:

- **Reconstruction / connected context:** last time an Order, source, customer, shipment or financial item had to be found/matched across systems; steps, people, delay, error, consequence, and what record was trusted.
- **Reconciliation / less work:** last payout/collection/stock/cost mismatch; exact files/tools/people, frequency, resolution time, unresolved amount/risk, workaround and who bears the burden.
- **Agency vs delegation:** last consequential change/intervention; what merchant could change directly, what required provider/support, what they deliberately delegated, and what they would never delegate.
- **Evidence / uncertainty:** last number/status later found misleading; evidence required before trusting delivery, collection, profit, ad performance or market signal; whether explicit unknown/partial state helps or frustrates.
- **Decision preparation:** last recurring decision requiring exports/calculation/advice; what evidence was assembled, decision delayed, stakes, current advisor/tool, desired recommendation authority, and what would count as success.
- **Category comprehension:** only after unaided narrative, use neutral, randomized descriptors/comparisons and ask what a buyer expects Wossol to own, connect, calculate, recommend, execute and measure. Record misinterpretations, not just preference.
- **Segment split:** compare self-operated, hybrid and managed-provider merchants; early and growing operations; single- and multi-tool/channel/market contexts where available. Averages may hide opposite preferences.

After coding, use the program’s evidence dimensions—frequency, severity, economic and decision consequence, workaround burden, cross-segment recurrence, Wossol proof, competitive defensibility, language resonance and future fit. Do not produce statistical prevalence from qualitative interviews, and do not score live during interviews. Advance a territory only if merchants independently describe its job, repeated material consequence and deficient alternatives; Wossol proof is current; claim avoids future intelligence; competitor claims do not render it generic; and the language is portable and understood without architecture explanation.

## 11. Claim-safety guardrails

- Keep all merchant problem/frequency/value/emotion statements labeled **UNVALIDATED — PRIMARY RESEARCH REQUIRED** until supported by coded primary evidence.
- Describe specific workflows and boundaries; never turn “connected” into universal synchronization or “truth” into perfect truth.
- Treat reduced work qualitatively; no time, error, revenue, profit, growth or headcount results without measurement.
- Distinguish merchant authority from provider execution; do not imply control over external providers.
- Distinguish competitor public claims/artifacts/contracts from verified behavior; no comparative superiority or competitor absence.
- Treat a recommendation as neither execution nor measured outcome. Keep H-C04 geography/period/cohort/suppression qualifiers where referenced.
- Do not claim full OMS/ERP/WMS/fulfillment/omnichannel/commerce OS, payment processing, global market coverage, causal attribution, predictive intelligence, autonomous execution, or learning.
- Keep Shopify demo claims tied to committed/currently verified flows; local uncommitted changes and pending live acceptance are not public proof.

## 12. Inputs to later final Positioning Territory Test

1. Begin with merchant stories and observed work, not these five labels; the existing `POSITIONING_TERRITORY_VALIDATION_INPUT.md` remains empty until coded evidence exists.
2. Carry three non-ranked candidates into research: job/consequence-led less work; mechanism-led connected evidence; agency-led bounded control.
3. Test connected evidence alone versus evidence→reduced-work combined framing; retain evidence-before-answers as a cross-cutting proof behavior unless merchants elevate it unaided.
4. Probe decision preparation after identifying actual recurring decisions; keep “better decisions” secondary until outcome evidence exists.
5. Test commerce operations as category context/descriptor, never as a proxy for differentiation or global readiness.
6. Compare against realistic alternatives: spreadsheets/manual tools, connected software stacks, internal teams/agencies, and provider-managed execution.
7. Reconcile the accepted open Product H-C04 documentation contradiction only in Product governance; it does not change this pre-validation’s bounded current interpretation.
8. Return surviving/demoted candidates to Director after merchant interviews and coded evidence. Do not select a winner or lock category, positioning, promise, name, tagline, archetype or identity here.

**Pre-validation conclusion:** evidence is sufficient to prune and structure the primary merchant test; it is not sufficient to answer that test. Director Positioning Pre-Validation Quality Gate remains pending.
