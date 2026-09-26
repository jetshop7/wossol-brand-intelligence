# Stores Review — 2026-09-26

## Review metadata
- Section: Stores
- Reviewed intelligence commit: `cc7b3d2ec60ab205183939ac25ee3fd5eeb3ceb2`
- Product evidence commit: `de2bb9bad7c39ce8f6ccf2d237a36cea1a82fb98`
- Reviewer: ChatGPT — Strategic Reviewer / Quality Gate
- Decision: **ACCEPT WITH OPEN PRODUCT ISSUES**
- Full re-audit required: No

## What passes

The audit correctly defines Store as a provider-neutral, Merchant-owned, Workspace-bound operating identity and scope rather than automatically an external storefront, warehouse, physical location or independent inventory pool.

Targeted Product verification confirms layered authorization. Staff Store scope is an additional restriction underneath active Merchant and Workspace authority; it does not grant Workspace, section or Merchant authority. Owner/Admin Store management and operating context remain separately governed.

The audit correctly bounds “All Stores” to the current authorized Workspace context. Persisted browser selection is preference, not authority; server-projected context remains the authorization boundary.

The cross-domain interpretation passes. Store identity can key Products, Orders, delivery pricing, Commerce connections and other records while those domains retain ownership of their own semantics. In particular, Store identity does not itself establish separate physical stock, external provider identity, attribution, Finance truth or Analytics intelligence.

The lifecycle finding is sound: disable/reactivate preserves stable Store identity/history rather than deleting or transferring it. The audit also appropriately distinguishes retained records from guaranteed historical merchant browsing after disable.

The create/edit validation inconsistency is a real P1 issue. The audit records that create-time backend validation is weaker than edit-time validation for Store URL/name constraints; frontend URL input is not treated as a server trust boundary.

The partial-success finding is also directly supported. The Settings flow creates the Store first and only then performs optional logo upload. If the second request fails, the Store already exists while the surrounding create flow reports an error before normal refresh/reset, so an unguarded retry can create another Store. This is a recoverability/idempotency issue, not evidence that Store creation itself rolled back.

Logo handling is appropriately bounded: repository implementation validates supported image content/size and stores private files behind authenticated handling, but deployed durability/backup is not established.

The strategic classification is appropriately conservative. Stable Store scope, lifecycle and layered access are useful operating infrastructure and can support Control, Clarity and Accountability, but are not by themselves a proven differentiator or moat.

Verification discipline passes: the canonical audit records 23 focused backend tests, 8 focused frontend tests and both typechecks passing, with Product source-state movement explicitly checked as unrelated to Store paths.

## Open product issues retained

1. **Create/edit validation parity:** align backend Store creation with edit-time name/URL syntax, protocol and length policy; ensure link rendering remains safe.
2. **Create + logo recoverability:** make partial success explicit/idempotent or allow logo retry against the already-created Store without encouraging duplicate Store creation.
3. **Disabled Store historical access:** define read-only historical visibility where needed without restoring disabled Store operational eligibility.
4. **Store terminology:** preserve the distinction between internal operating Store, external commerce storefront/account, warehouse/location and inventory pool.
5. **Ownership correction:** no self-service delete/Workspace transfer exists; any correction of wrongly assigned Merchant/Workspace ownership needs governed handling if operationally required.
6. **Logo deployment durability:** production storage, backup/recovery and multi-instance behavior remain unverified.
7. **Product-wide Store semantics:** downstream domains must continue to define their own Store eligibility/aggregation rules rather than treating Store identity as universal operational equivalence.

These issues constrain workflow quality and positioning but do not invalidate the Stores intelligence audit.

## Claim / strategic safety

Safe present territory is a stable Workspace-bound operating Store identity, Owner/Admin self-service lifecycle, active Workspace context, layered Staff Store restrictions and Store-scoped joins into owner domains.

Do not claim that creating a Store connects an external storefront, creates an independent inventory pool, gives a complete cross-market view, grants Staff permissions, guarantees historical browsing after disable, or provides an atomic/fully validated setup flow.

## Methodology impact

No methodology change is required.

## Retroactive impact

No prior accepted section requires correction. The Stores audit reinforces the Integrations / Commerce Channels boundary: internal Store identity and external provider identity are separate. Team must preserve Store grants as additional restrictions rather than independent authority. Sourcing/Network must not infer warehouse or physical stock ownership from Store identity.

## Acceptance

**Stores passes the Director Quality Gate and is accepted with the open Product issues above.**

No Codex correction or re-audit is required.

The next section in the canonical sequence is **Team**.
