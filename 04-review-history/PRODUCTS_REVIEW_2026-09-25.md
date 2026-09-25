# Products Review — 2026-09-25

## Review metadata

| Field | Value |
|---|---|
| Section | Products |
| Reviewed intelligence artifact | `02-section-intelligence/PRODUCTS.md` |
| Reviewed intelligence commit | `dffc7d4` |
| Product evidence commit used by audit | `e842e8e4e45ba4748412232c914fdeb20063b8ab` |
| Reviewer | ChatGPT — Strategic Reviewer / Quality Gate |
| Review date | 2026-09-25 |
| Decision | **ACCEPT WITH OPEN PRODUCT ISSUE** |
| Full re-audit required | No, unless correction exposes broader source contradictions |

## What passes

The central section conclusion is strong and appropriately bounded: Products is currently a controlled execution-identity/catalog system, not Product performance/opportunity intelligence.

The audit also correctly identifies:
- Product vs Variant ownership/execution distinction;
- guarded activation and provider-dependent preparation;
- Inventory as the owner of stock truth;
- explicit Shopify Product/Variant mapping rather than name/SKU inference;
- versioned payment-policy evidence;
- history/audit and archive-over-erasure behavior;
- current lack of Product performance/profit/opportunity intelligence;
- the need to keep future Product intelligence separate from present-tense claims.

These conclusions can remain unless correction evidence materially changes them.

## Material correction 1 — UI specification evidence tier is internally inconsistent

Audit Metadata says the current UI specification is **P2**, but `EV-PROD-015` labels specification comparison as **P2/P3**, while section 28 says:

> "Where wording and code diverge, P1 code controls current truth."

That last rule is too broad.

Executable P1 can establish **what the reviewed build currently does**. It does not automatically establish that a conflicting current/final product contract was superseded, cancelled, or stale. This is the same contradiction-discipline issue already corrected in Home.

Required correction:
1. Apply one evidence classification consistently to `MERCHANT_PRODUCTS_UI_SPEC.md` according to the canonical methodology.
2. When a current/final specification conflicts materially with executable behavior, state both:
   - current executable truth; and
   - unresolved product-contract intent.
3. Do not resolve a product-intent contradiction merely by saying P1 "prevails" or "controls" unless there is evidence that the specification was superseded.
4. Re-check `EV-PROD-015`, section 28, and any downstream finding that depends on this rule.

## Material correction 2 — Test Product capability appears omitted from the audit

The reviewed current Products specification begins with an explicit **Test Product eligibility** rule:

> Product Create and Edit expose a Merchant-authorized Test Product setting, defaulting to off. It controls only future Manual Test Order eligibility and never changes historical Order purpose, stock, or commercial Analytics.

The Products audit does not record this capability, its evidence status, merchant value, cross-section consequence, or whether executable code/tests actually implement it.

This matters because the Product → Brand methodology requires atomic inspection and small-feature extraction. It is also strategically connected to the existing Wossol Test Order / demand-validation direction, while requiring strict claim safety because the specification says it controls **future Manual Test Order eligibility** and must not rewrite historical/commercial truth.

Required correction:
1. Search P1/P2 evidence for the Test Product field/control and its permission/authorization behavior.
2. Classify it accurately as LIVE / PARTIAL / DOCUMENTED / NOT FOUND AFTER SEARCH / other canonical status.
3. If implemented, add it to the relevant capability, workflow/data/cross-section/marketing or supporting-proof sections without inflating it.
4. If only documented, preserve it as documented intent/future eligibility and do not market it as live.
5. Explicitly preserve the boundary that it does not alter historical Order purpose, stock, or commercial Analytics.

## Material correction 3 — Store mapping claims need sharper separation

The audit says Products manages "store usage context" and later notes single-store creation and unclear broader Store-mapping lifecycle. The current specification contains broader approved behavior around Store selection/mapping, including All Stores creation requiring at least one Store and optional mapping management where supported.

Required correction:
- Separate **implemented ProductStore/store-scoped behavior** from **documented Store-mapping UX/contract**.
- Do not imply that general multi-Store mapping management is live unless P1 verifies it.
- Preserve any unresolved contract/implementation gap rather than reducing it to a generic future question.

## Claim / strategic safety

No correction is required to the current conclusion that Product performance/profit/opportunity intelligence is not established.

Do not promote:
- "winning product" discovery;
- Product profitability intelligence;
- generalized omnichannel synchronization;
- sourcing/Network catalog;
- advertising attribution;
- broad competitor superiority

from this Products audit.

The potentially valuable current territory remains the combination of exact execution identity, operational readiness gates, explicit mappings, scoped ownership, and preserved evidence — subject to the corrections above.

## Methodology impact

No methodology change is required at this stage. The existing atomic-inspection, small-feature, contradiction, evidence-hierarchy, current-vs-future, and claim-safety rules are sufficient. The issue is application.

## Retroactive impact

No retroactive queue entry yet. Home already contains the stricter contract-vs-executable interpretation. Products should be brought into the same discipline.

## Required resolution

Perform a **targeted Products correction**, not a full re-audit.

Re-check affected sections at minimum:
- Audit Metadata / Coverage Map;
- Current Capability Inventory;
- Workflow / lifecycle if Test Product or Store mapping affects it;
- Merchant value / hidden advantages where justified;
- Data & Intelligence Assets;
- Cross-Section Compound Advantages;
- Weaknesses / Risks / Gaps;
- Future Strategic Potential;
- Claim Safety;
- Action Register;
- Evidence Register;
- Contradictions & Uncertainty;
- Open Questions;
- Canonical Section Takeaway only if materially affected.

After correction, run consistency checks, commit, push, and report the resulting SHA.


## Final Quality Gate — 2026-09-25

### Re-verification metadata
- Corrected intelligence commit: `784b73257b81242c4ff9e602c27211ecf190c355`
- Product repository state checked at correction: `e3912a967827bde06450d3510228e5a5ca9e78a7`
- Director decision: **ACCEPT WITH OPEN PRODUCT ISSUE**
- Full re-audit required: **No**

### Correction verification
1. **Specification evidence discipline — PASS.** `MERCHANT_PRODUCTS_UI_SPEC.md` is now treated consistently as P3 approved/final Product intent, while the dated architecture-reading summary is P4. The audit preserves executable P1 truth separately from unresolved contract intent and no longer uses P1 alone to declare the current/final contract superseded.
2. **Test Product capability — PASS.** The corrected audit records the merchant create/edit flag, default-off behavior, Product authorization boundary, and downstream Manual Test Order eligibility. Targeted Product evidence confirms the create control and Orders eligibility tests. The audit preserves the safety boundary that the flag does not rewrite historical Order purpose, stock truth, or commercial Analytics.
3. **Store mapping separation — PASS WITH OPEN PRODUCT ISSUE.** The corrected audit distinguishes implemented ProductStore/store-scoped behavior from the broader Final V1 Store-mapping contract. General merchant Store-mapping mutation behavior is not claimed as live; the unresolved implementation/contract gap is explicitly retained in risks, evidence, contradictions, actions, open questions, and the canonical takeaway.
4. **Claim and strategic safety — PASS.** The correction does not promote Product profitability/opportunity intelligence, generalized omnichannel synchronization, sourcing/network catalog, advertising attribution, or broad competitor superiority. Current differentiation remains bounded to supported operational controls and evidence.
5. **Traceability and consistency — PASS.** The corrected artifact identifies this review as correction provenance, adds `EV-PROD-016`–`EV-PROD-018`, and carries the affected findings through capability, workflow, cross-section, risk, claim-safety, action, contradiction, open-question, and takeaway sections without introducing a new material overclaim.

### Open product issue retained
The Final V1 Store-mapping contract remains broader than the targeted executable ProductStore lifecycle verified for this gate. This is an unresolved Product contract/implementation issue, not an intelligence-audit defect. Marketing and downstream synthesis must remain bounded to verified Store-scoped behavior until the contract is explicitly narrowed/superseded or the broader behavior is implemented and evidenced.

### Acceptance
**Products passes the Director Quality Gate and is accepted with the open Store-mapping Product issue above.**

The next section in the canonical audit sequence is **Inventory**.
