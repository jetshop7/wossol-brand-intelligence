# Wossol Director Review Protocol

## 1. Review sequence

For every completed Codex audit:

### Stage A — Verify the artifact exists on GitHub
Confirm:
- section file path;
- reported intelligence commit;
- pushed branch;
- latest GitHub content.

Do not review only the Codex chat handoff.

### Stage B — Read the full section audit
Assess:
- audit metadata;
- coverage;
- strongest conclusions;
- capability inventory;
- evidence register;
- contradictions;
- weaknesses;
- claim safety;
- strategic classification;
- canonical takeaway.

### Stage C — Challenge against product evidence
Open the Product Source of Truth for the claims that matter most.

Verify at minimum:
- core workflows;
- important permissions/scope;
- any unusual/strategic capabilities;
- any "not implemented" or "by design" conclusion that materially affects strategy;
- any current/final product spec that could contradict the executable build;
- any small capability that appears in specifications but is absent from the audit.

### Stage D — Review strategic interpretation
Test:
- merchant value vs technical novelty;
- differentiation vs table stakes;
- control depth;
- transparency/trust depth;
- current truth vs future territory;
- feature vs system advantage;
- cross-section compound value;
- copyability/defensibility;
- marketing claim safety.

### Stage E — Decide
Choose one:
- ACCEPT;
- ACCEPT WITH OPEN PRODUCT ISSUE;
- NEEDS TARGETED CORRECTION;
- NEEDS TARGETED SOURCE VERIFICATION;
- NEEDS FULL RE-AUDIT.

Use `DECISION_STANDARDS.md`.

### Stage F — Persist the review
If anything material needs correction, create:
`04-review-history/<SECTION>_REVIEW_<YYYY-MM-DD>.md`

### Stage G — Correction loop
Tell Codex to apply the review corrections.
After push:
- read the corrected artifact from GitHub;
- verify every required correction;
- ensure no new overclaim was introduced;
- inspect correction/review traceability;
- accept or issue another narrowly bounded correction.

---

## 2. Evidence challenge checklist

Ask:

### Coverage
- Did Codex inspect UI and backend?
- Did it inspect schema/data where relevant?
- Did it inspect permissions/scoping?
- Did it inspect tests and current/final specs?
- Did it inspect connected systems when strategically relevant?

### Truth
- Is "LIVE" actually supported?
- Is "NOT IMPLEMENTED" based on reasonable search?
- Is "BY DESIGN" actually evidenced?
- Is a current spec being incorrectly treated as stale?
- Is a historical summary being treated as current truth?

### Value
- Would a merchant care?
- Is the real value hidden in backend behavior?
- Did Codex miss a micro-proof?
- Is the strength from a feature, workflow, architecture, history, or data accumulation?

### Competitive
- Did Codex claim absence from silence?
- Did it distinguish value from distinctiveness?
- Did it avoid superiority claims without depth evidence?

### Marketing
- Are current claims bounded to current evidence?
- Are future claims clearly future?
- Are proof points stronger than generic copy?
- Is any attractive wording stronger than the evidence?

### Brand
- Is the audit producing evidence rather than forcing a preselected territory?
- Does the finding support Control, Transparency, Trust, Guidance, Learning, Access, etc., or is the connection weak?

---

## 3. Review record template

# <Section> Review — <YYYY-MM-DD>

## Review metadata
- Section
- Reviewed intelligence commit
- Product evidence commit
- Reviewer
- Decision
- Full re-audit required

## What passes
List major accepted conclusions.

## Material finding 1
State:
- problem;
- evidence;
- why it matters;
- required correction.

Repeat as needed.

## Claim / strategic safety
Record claims that must remain bounded.

## Methodology impact
State whether methodology needs change or whether the issue is application.

## Retroactive impact
State whether previous sections are affected.

## Required resolution
State whether Codex should perform targeted correction, targeted source verification, or re-audit.

---

## 4. Re-verification protocol

When Codex reports corrections complete:

1. Fetch corrected files from GitHub at the pushed commit.
2. Search specifically for each required correction.
3. Read surrounding sections, not only the edited line.
4. Verify review traceability.
5. Confirm the correction did not create an unsupported stronger conclusion elsewhere.
6. Decide ACCEPT or continue the correction loop.

Do not accept based solely on Codex's summary.
