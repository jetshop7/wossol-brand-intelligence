# Wossol Director Decision Standards

## ACCEPT

Use when:
- material evidence is traceable;
- conclusions are appropriately bounded;
- no material omission or contradiction remains unhandled;
- claim safety is sound;
- any open product issue is accurately preserved.

## ACCEPT WITH OPEN PRODUCT ISSUE

Use when:
- the intelligence audit itself is correct;
- a real product/spec/runtime issue remains unresolved;
- the audit records the uncertainty accurately;
- the unresolved product issue does not invalidate the intelligence conclusions.

Example: executable Home truth is correctly documented while a final UI contract remains unresolved.

## NEEDS TARGETED CORRECTION

Use when the evidence foundation is sound but the artifact needs bounded changes such as:
- overstrong wording;
- wrong evidence classification;
- missing traceability;
- unresolved "by design" inference;
- missed small feature that can be checked without redoing the section;
- incomplete contradiction handling;
- downstream sections needing consistency updates.

## NEEDS TARGETED SOURCE VERIFICATION

Use when one or a few material capabilities were not actually investigated enough.

Examples:
- Test Product appears in a current spec but the audit does not establish whether P1 implements it;
- multi-Store mapping is described in contract but executable support is unclear.

## NEEDS FULL RE-AUDIT

Use only when:
- major audit surfaces were skipped;
- source state is unreliable;
- evidence IDs are broadly untrustworthy;
- conclusions depend on incorrect architecture assumptions;
- the section was effectively audited only from UI/docs;
- methodology was not applied deeply enough to salvage through bounded correction.

## Severity and efficiency rule

Prefer the **smallest intervention that restores intelligence quality**.

Do not spend Sol/High redoing an entire section when a Terra/Luna targeted correction can safely resolve it.

## Evidence authority nuance

P1 executable evidence answers:
> What does the reviewed build currently do?

It does **not** automatically answer:
> What is the approved intended contract?

A current/final specification can remain authoritative intent even when implementation differs. Treat that as a contract gap until supersession or completion is evidenced.

## Marketing/brand acceptance standard

An audit is not acceptable if it:
- markets a future capability as current;
- turns competitor silence into superiority;
- calls visibility control without execution authority;
- calls data intelligence without interpretation/decision support;
- calls architecture a moat without defensibility evidence;
- treats a technical safeguard as a hero feature without merchant consequence.

## Director recommendation style

When asking Codex for corrections:
- be exact;
- be concise;
- point to the authoritative review record;
- do not paste the whole methodology;
- choose the lowest safe Codex model/reasoning setting for the correction.

When launching a fresh deep audit:
- use the section short command;
- rely on repository governance;
- use a stronger reasoning configuration when the domain is strategically interconnected or technically deep.
