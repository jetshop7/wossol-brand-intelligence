# Home Audit Review — 2026-09-25

## Review metadata

- Section: Home
- Reviewed intelligence commit: `3cac1ed`
- Reviewed document: `02-section-intelligence/HOME.md`
- Product evidence commit: `d52fd3e8db087d914efa0d56ad12d9b56f28cb2f`
- Reviewer: ChatGPT — Strategic Reviewer / Quality Gate
- Decision: **NEEDS CORRECTION**
- Re-audit scope: targeted; full Home re-audit is not required unless the correction uncovers wider effects.

## Material finding

The audit correctly detected a material documentation/code contradiction, but it resolves and describes that contradiction too strongly in favor of the executable code.

The current product document `docs/ui/merchant/MERCHANT_HOME_SHELL_UI_SPEC.md` is titled **Merchant Home V1 - Final Functional UI Specification** and contains explicit acceptance criteria. At the audited product commit it requires, among other things:

- four Today cards, including Needs Attention;
- a bounded Created/Delivered Orders trend;
- Operational Overview with up to three groups, including Waiting for stock;
- Recent Updates bounded to six records.

The executable Home instead has:

- three Today metrics;
- no Orders trend;
- two Operational Overview groups;
- notifications sliced to three.

Therefore the audit may use P1 executable code as the authority for **what is live now**, but it must not silently reinterpret the conflicting final functional specification as merely stale intent or conclude that the missing elements are **NOT PRESENT BY DESIGN** without stronger evidence that the specification was superseded.

## Required corrections

1. Preserve the current-truth conclusion: the executable Home is the narrower live implementation.
2. Reclassify the conflicting specification accurately: it is a current-looking final functional specification with acceptance criteria that conflicts with P1/P2 executable behavior; whether it is stale/superseded or the implementation is incomplete is unresolved.
3. Remove or qualify statements such as **Trend chart ... NOT PRESENT BY DESIGN** where the specification explicitly requires the feature.
4. Do not frame H-R01 only as a documentation reconciliation problem. Record both possible resolutions:
   - the specification is superseded and must be updated/marked as such; or
   - the implementation is incomplete against the current functional contract and must be brought into compliance.
5. Re-check every downstream section affected by this interpretation, especially Current Capability Inventory, Weaknesses/Risks/Gaps, Future Strategic Potential, Claim Safety, Action Register, Evidence Register, Contradictions & Uncertainty, and Canonical Section Takeaway.
6. Keep the correct claim-safety rule: absent executable features must not be marketed as live regardless of which side of the contradiction is eventually resolved.

## What passed review

The following major conclusions are provisionally accepted:

- Home is a composition-only, scoped, read-only operational surface rather than a standalone intelligence engine.
- Event-history-based performance semantics are a meaningful trust/data-discipline proof.
- Home preserves owner-domain boundaries for Analytics, Inventory, Orders, Notifications, and Finance.
- The recommendation handoff is evidence of bounded guidance distribution, not proof that Home owns decision intelligence.
- Competitor evidence is insufficient for Home-specific superiority claims.
- Runtime/deployment behavior remains unverified and is correctly separated from code/test evidence.

## Methodology impact

No methodology change is required. The existing contradiction protocol is sufficient; the issue is its application. P1 establishes current executable truth, but P3/P2-style approved/final specification evidence can still establish an unresolved implementation-contract gap. Higher implementation evidence does not automatically prove that conflicting product intent was superseded.

## Retroactive impact

None yet. Home is the first canonical section audit. Apply this interpretation discipline to subsequent audits.

## Resolution status

**OPEN — targeted Codex correction required before Home is accepted and before proceeding to Products.**
