# WOSSOL CODEX OPERATING PROTOCOL

**Version:** 1.0  
**Status:** Mandatory execution contract  
**Purpose:** Make every section audit executable from a short command such as `Audit Orders.`

---

# 0. ROLE

This file defines **how Codex operates this repository**.

`MASTER_INSTRUCTIONS.md` defines the analytical methodology.  
This protocol defines the repeatable execution lifecycle around that methodology.

Codex is the **Investigator / Extractor / Evidence Builder**.

Codex must inspect the Wossol product deeply, write evidence-backed intelligence into this repository, and leave the work in a state that a separate reviewer can challenge without relying on conversation memory.

The repository — not a chat prompt — is the durable operating memory.

---

# 1. REPOSITORY BOUNDARY

## Product Source of Truth
`jetshop7/wossol-platform`

Use it to determine current product truth.

During intelligence audits:
- read and inspect it;
- do not modify product code;
- do not implement fixes unless a future task explicitly authorizes implementation;
- do not copy application code into this repository.

## Intelligence Source of Truth
`jetshop7/wossol-brand-intelligence`

**GitHub is the shared canonical state of this Intelligence repository.** The local clone is only Codex's working copy/cache. Write methodology, competitive intelligence, section audits, synthesis, and review history here, then commit and push coherent completed work to the configured GitHub upstream.

---

# 2. SHORT COMMAND CONTRACT

A command such as:

`Audit Orders.`

means:

> Perform the complete current-methodology Product → Brand Intelligence audit of the Orders section, using this operating protocol, continue until the Definition of Done is satisfied, write/update the canonical section document, make any justified methodology/retroactive updates, commit and push the intelligence changes to the configured GitHub remote, and return the standard handoff report.

The user does **not** need to restate the methodology, evidence rules, file locations, audit stages, output structure, or completion criteria.

Other equivalent commands include:
- `Audit Home.`
- `Audit Inventory.`
- `Audit Confirmation.`
- `Re-audit Orders.`
- `Continue the Orders audit.`

Resolve the requested section against the canonical section naming/order in `02-section-intelligence/README.md`.

If the command is unambiguous, **do not ask for confirmation**.

---

# 3. MANDATORY STARTUP SEQUENCE

At the start of **every intelligence task** — including audit, re-audit, continuation, reviewer correction, retroactive review, or synthesis — first synchronize the Intelligence repository from GitHub.

## 3.1 GitHub synchronization gate

Before interpreting repository state or claiming that a review/correction file does not exist:

1. Identify the current Intelligence branch, its upstream, and working-tree status.
2. Fetch the configured GitHub remote.
3. If the Intelligence working tree is clean, fast-forward the current branch to its upstream (normally `origin/main`). Do not create merge commits merely to synchronize.
4. If local Intelligence changes or commits would make fast-forward unsafe, **do not reset, discard, overwrite, or force-push them**. Preserve the work and reconcile safely; if reconciliation is not unambiguous, report the blocker and exact divergence.
5. Verify the local HEAD/upstream state after synchronization.
6. Only then resolve the task and read repository instructions/review records.

The user must not be required to run routine `git pull` commands between ChatGPT reviews and Codex tasks. Codex owns this synchronization step when network/authentication are available.

If GitHub/network/authentication is unavailable, continue only when the task can be completed safely from the available state and explicitly report that the shared canonical state could not be refreshed.

## 3.2 Required reads after synchronization

1. Read this `CODEX_OPERATING_PROTOCOL.md`.
2. Read `MASTER_INSTRUCTIONS.md` completely.
3. Read `METHODOLOGY_CHANGELOG.md`.
4. Read `RETROACTIVE_REVIEW_QUEUE.md`.
5. Read `../01-competitive/WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md`.
6. Read `../02-section-intelligence/README.md`.
7. Read the target section document if it already exists.
8. Read previous section intelligence that materially affects the target through cross-domain relationships.
9. Read relevant current product architecture/source-of-truth documents when they materially affect interpretation.
10. Inspect the current state of `jetshop7/wossol-platform`.

Do not rely on remembered methodology from a previous Codex conversation.

Always use the repository's latest committed operating documents **after the synchronization gate**. For reviewer-correction tasks, also read the referenced/latest applicable record under `04-review-history/` before changing the section audit.

---

# 4. SOURCE-STATE GATE

Before strategic conclusions, record:

- product repository;
- branch;
- commit SHA;
- audit date;
- whether local/uncommitted changes exist;
- whether local workspace differs from committed GitHub state;
- methodology version;
- competitive reference version.

If source state cannot be determined, record the limitation.

Do not silently audit an ambiguous product state.

---

# 5. SECTION DESTINATION

Canonical output belongs in:

`02-section-intelligence/<SECTION>.md`

Examples:

- Home → `HOME.md`
- Products → `PRODUCTS.md`
- Inventory → `INVENTORY.md`
- Orders → `ORDERS.md`
- Confirmation → `CONFIRMATION.md`
- Customers → `CUSTOMERS.md`
- Tracking / Delivery → `TRACKING_DELIVERY.md`
- Finance → `FINANCE.md`
- Analytics / Decision Center → `ANALYTICS_DECISION_CENTER.md`
- Market Center → `MARKET_CENTER.md`
- Advertising → `ADVERTISING.md`
- Integrations / Commerce Channels → `INTEGRATIONS_COMMERCE_CHANNELS.md`
- Stores → `STORES.md`
- Team → `TEAM.md`
- Sourcing / Network → `SOURCING_NETWORK.md`

Do not create duplicate section documents under alternate names.

---

# 6. EXECUTION LIFECYCLE

For every section audit execute the complete stages defined by `MASTER_INSTRUCTIONS.md`.

Operationally:

## A. Discover
Build the Audit Coverage Map before claiming strategic completeness.

## B. Inspect
Inspect the relevant product implementation deeply enough to establish current truth.

Do not stop at UI. Follow important workflows through relevant frontend, APIs, business logic, authorization, schema/data, side effects, events/jobs, integrations, tests, history, failure/recovery, and connected domains.

## C. Extract
Extract atomic capabilities and meaningful small proofs before deciding what is important.

## D. Interpret
Apply the current methodology: recipients, value lenses, control depth, transparency/trust, merchant journey, Old Way vs Wossol Way, feature interactions, cross-section compounds, data/intelligence, competitive interpretation, marketing extraction, brand evidence, weaknesses, future potential, claim safety, copyability, accumulation, and other active lenses.

## E. Challenge
Actively search for contradictory evidence, weaknesses, missing depth, false differentiation, inflated claims, and attractive conclusions that do not survive evidence.

## F. Document
Write/update the canonical section document using the required structure from `MASTER_INSTRUCTIONS.md`.

## G. Self-Critique
Run the mandatory self-critique.

## H. Definition of Done
Do not mark the section complete until the active methodology's Definition of Done is satisfied or unresolved blockers are explicitly documented.

---

# 7. EVIDENCE DISCIPLINE

Use the evidence hierarchy and capability-status taxonomy from `MASTER_INSTRUCTIONS.md`.

Material conclusions must be traceable.

Use stable section evidence IDs, for example:
- Home: `EV-HOME-001`
- Products: `EV-PROD-001`
- Inventory: `EV-INV-001`
- Orders: `EV-ORD-001`
- Confirmation: `EV-CONF-001`
- Customers: `EV-CUST-001`
- Tracking/Delivery: `EV-TRACK-001`
- Finance: `EV-FIN-001`

Continue existing numbering when re-auditing a section. Do not silently reuse an evidence ID for a different fact.

Never fabricate line numbers, symbols, file paths, commits, competitor capabilities, or product behavior.

---

# 8. ABSENCE / UNCERTAINTY

Repository silence is not proof of absence.

Before concluding a capability is missing, perform the reasonable search required by the methodology.

Use:
- `NOT FOUND AFTER SEARCH`
- `UNCERTAIN`
- `INSUFFICIENT EVIDENCE`
- `NOT VERIFIED`

where appropriate.

Do not convert uncertainty into a stronger Wossol claim.

---

# 9. CURRENT TRUTH VS FUTURE

Maintain explicit separation between:

- current product truth;
- partial/scaffold behavior;
- approved future direction;
- inferred opportunity;
- long-term strategic/brand territory.

Future architecture may explain direction but cannot prove a current marketing claim.

---

# 10. COMPETITIVE OPERATING RULE

Use the Competitive Intelligence Master as the stable baseline.

Do not infer that a competitor lacks something because the master is silent.

When current external verification is required to support a material competitive conclusion and the available environment permits research, verify it and preserve source/evidence quality. Otherwise mark the conclusion as insufficiently verified.

Compare depth and merchant consequence, not feature labels.

---

# 11. METHODOLOGY EVOLUTION

If the audit reveals a genuinely reusable methodological improvement, follow the full Methodology Evolution Protocol.

Codex may update:
- `MASTER_INSTRUCTIONS.md`;
- `METHODOLOGY_CHANGELOG.md`;
- `RETROACTIVE_REVIEW_QUEUE.md`;

only when the methodology's criteria are satisfied.

Never silently change the methodology.

Do not turn a section-specific observation into a universal rule without testing generalizability.

---

# 12. RETROACTIVE REVIEW

When a methodology change may materially affect completed sections:

1. create/update queue entries;
2. finish the current section coherently;
3. do not recursively derail the current audit;
4. process retroactive work only when instructed or when the active task explicitly includes queue processing.

A short command such as `Audit Orders` does not automatically mean re-audit every prior section.

---

# 13. MASTER SYNTHESIS RULE

The master synthesis files are not dumping grounds.

Update `03-master-synthesis/` only when evidence justifies a reusable cross-section asset or when the task explicitly requests synthesis.

A single-section observation may be recorded as provisional when strategically important, but must not be promoted into final positioning or brand truth without sufficient evidence.

Do not prematurely decide naming, tagline, archetype, logo, colors, visual concept, or final positioning.

---

# 14. REVIEW HISTORY

Codex is not the final reviewer.

A separate ChatGPT review layer may inspect the audit after completion.

Make the work reviewer-friendly:
- evidence traceable;
- uncertainty visible;
- source state explicit;
- coverage visible;
- methodology version recorded;
- strongest conclusions distinguishable from hypotheses.

Do not mark your own audit as externally approved.

Material reviewer corrections can later be recorded under `04-review-history/`.

---

# 15. WRITE SAFETY

During an audit:
- never modify `wossol-platform`;
- write only intelligence/project-governance artifacts to `wossol-brand-intelligence`;
- preserve existing evidence/history unless evidence justifies correction;
- do not overwrite a completed audit with a shorter summary;
- when correcting prior findings, preserve enough explanation to understand what changed and why.

---

# 16. COMMIT PROTOCOL

When an audit, reviewer correction, retroactive review, or synthesis task reaches a coherent completion point:

1. verify the changed files;
2. ensure no product-repository modifications were made;
3. commit intelligence-repository changes with a clear message such as:
   `Audit Orders product-to-brand intelligence`
4. push the completed intelligence commit to the configured GitHub remote/branch when Git authentication and permissions are available;
5. verify the push succeeded;
6. record/report the resulting intelligence commit SHA and pushed branch.

GitHub is the shared canonical Intelligence Source of Truth. Do not leave completed intelligence work only in the local clone when push access is available. After pushing, verify that the remote branch contains the resulting commit. A later task must begin from the synchronization gate rather than assuming the local clone is current.

Do not make meaningless commits merely to show progress.

If the environment cannot commit or push, clearly report that limitation, the exact uncommitted/unpushed changes, and the command/state needed to finish synchronization.

---

# 17. STANDARD HANDOFF REPORT

After a completed audit, return a concise report containing:

## Source State
- product repo / branch / commit;
- local-change status;
- methodology version.

## Intelligence Output
- intelligence commit SHA;
- pushed remote/branch and push status;
- files created/modified.

## Coverage
- major surfaces inspected;
- blocked/unavailable surfaces;
- material remaining uncertainty.

## Strongest Findings
Only the strongest evidence-backed findings.

## Weaknesses / Risks
Material negative findings.

## Competitive Significance
Only evidence-supported conclusions with appropriate confidence.

## Marketing / Brand Evidence
Separate current truth from future territory.

## Methodology
- methodology changed: Yes / No;
- new version if changed;
- retroactive queue additions.

## Open Questions
Only questions that cannot reasonably be resolved from available evidence.

Do not paste the full audit into the handoff. The repository document is the detailed record.

---

# 18. CONTINUE / RE-AUDIT SEMANTICS

## `Continue <Section> audit`
Read the existing section document and repository state, identify incomplete coverage/open blockers, and continue from there without restarting blindly.

## `Re-audit <Section>`
Treat the current product state and current methodology as authoritative. Re-check prior evidence and conclusions, preserve stable evidence IDs where still valid, explicitly record material changes, and update the canonical document.

## `Review retroactive queue`
Process queued sections systematically according to the active methodology.

---

# 19. FAILURE / BLOCKER PROTOCOL

Do not hide blockers.

If a relevant surface is unavailable:
- mark it `BLOCKED / UNAVAILABLE`;
- explain why;
- state what conclusions it prevents;
- continue with all other reasonable work.

Ask the user only when a missing input is truly required and cannot be obtained from the repositories or available environment.

---

# 20. EFFICIENCY

The goal is not maximum document length.

The goal is maximum **useful, defensible intelligence**.

Follow:

> **Exhaustive discovery of meaningful value, not exhaustive prose about every line of code.**

Prioritize merchant impact, strategic relevance, evidence uncertainty, competitive significance, cross-domain importance, marketing potential, and risk.

---

# 21. SHORT-COMMAND READINESS TEST

This protocol is working correctly if a fresh Codex session with repository access can receive only:

> `Audit Orders.`

and independently know:
- what repositories to use;
- what it may and may not modify;
- what mandatory files to read;
- how to verify source state;
- how to map audit coverage;
- how deeply to inspect;
- what analytical methodology to apply;
- where to write;
- how to handle evidence;
- how to handle uncertainty;
- how to evolve methodology;
- how to handle retroactive impacts;
- when the section is done;
- how to commit and push the completed intelligence work;
- what to report.

If Codex needs a giant task prompt to recover these rules, this operating protocol or repository governance is incomplete.

---

# 22. FINAL OPERATING RULE

A short task command identifies **what section to audit**.

The repository determines **how the audit must be performed**.

Do not require conversation memory to reconstruct the process.

Do not merely catalogue what Wossol has.

> **Discover what Wossol can legitimately own — and preserve the evidence that proves it.**
