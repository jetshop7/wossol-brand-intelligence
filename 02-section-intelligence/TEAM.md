# Team — Section Intelligence

## 1. Audit Metadata

- **Audit date:** 2026-09-26.
- **Methodology:** `00-methodology/MASTER_INSTRUCTIONS.md` v1.1; `00-methodology/CODEX_OPERATING_PROTOCOL.md` v1.0.
- **Competitive reference:** `01-competitive/WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md` v1.0 (2026-09-09).
- **Intelligence source:** `jetshop7/wossol-brand-intelligence`, `main`, synchronized at `cddd1c077a30390d1d8fa781d357666dbeb02fdb` at audit start; clean.
- **Product source:** `jetshop7/wossol-platform`, `dev/wossol-integration`, `de2bb9bad7c39ce8f6ccf2d237a36cea1a82fb98`, clean and equal to local `origin/dev/wossol-integration`; inspected read-only.
- **Evidence standard:** P1 executable source/schema, P2 targeted tests, P3 current product/architecture specifications, P4 historical design. Repository evidence establishes implementation intent/current source, not production deployment, tenant configuration, or outcomes.

## 2. Audit Coverage Map

| Surface | Coverage | Evidence |
|---|---|---|
| Merchant account Team | Owner/Admin/Staff model, member lifecycle, audit and credential flow | EV-TEAM-001–005 |
| Authorization composition | Merchant, Workspace, section, Store gates; Shell navigation | EV-TEAM-006–008 |
| Internal employee administration | Separate employee identity, workspace access, role/direct grants and protection | EV-TEAM-009–012 |
| Operational staffing | Confirmation assignment surface and distinct worker domains | EV-TEAM-013–015 |
| Verification | Targeted tests and source-state checks | EV-TEAM-016 |
| Competition | Existing competitive master baseline | EV-TEAM-017 |

Not established: production deployment/feature flags, live authorization data, incident rates, password-reset delivery/security outside the repository, complete parity of every domain endpoint with UI section gates, nor competitor implementation depth beyond the competitive master’s documented coverage.

## 3. Executive Section Truth

“Team” is not one universal Wossol workforce or authority model. The Merchant Portal’s Team manages **merchant-side user access** (Owner, Admin, Staff) to a Merchant account and composes Workspace, section, and Store restrictions. A separate Admin Employees capability manages **Wossol internal employees** with Workspace membership, catalog roles, and direct permissions. Confirmation and Tracking workers are **operational resources/assignments** managed by their domain systems; they are not interchangeable with either merchant logins or internal employee access records.

Merchant Team is a substantial, auditable access-administration workflow: create, edit, scope, reset credentials, disable, and reactivate, with Owner/Admin restrictions and session invalidation on material changes. It is not proof that every individual operation is safely governed solely by Team settings; the consuming backend service must enforce the independent Merchant, Workspace, section, Store, and domain-specific gates. Basic user/role management is common category infrastructure, not differentiation by feature label alone.

## 4. Scope & Architecture Map

Merchant user → authenticated identity → Merchant membership/role → active Workspace membership and role assignment → optional merchant-section grant → optional Store restriction → domain-specific endpoint policy. Separately: internal employee identity → AdminUser/InternalEmployee record → WorkspaceMembership → role and/or direct permission assignment → backend permission guard. Separately again: confirmation/tracking assignment records and worker-facing access flows.

| Layer | Current role |
|---|---|
| MerchantUser | Links a User to one Merchant with Owner/Admin/Staff label and membership status. |
| UserRoleAssignment | Assigns merchant Admin/Staff role within selected Workspaces; revocable. |
| WorkspaceMembership | Establishes active/revoked Workspace membership; not itself a grant to every function. |
| MerchantSectionPermissionAssignment | Staff section-level grant; Admin/Owner access is derived by role. |
| MerchantUserStoreAccess | Additional Store restriction. Per Stores review (2026-09-26), it cannot independently grant Merchant, Workspace, or section authority. |
| InternalEmployee | Distinct Wossol employee authority record; Workspace roles and direct grants are separately managed and audited. |
| Operational worker | Confirmation/Tracking worker assignment is domain workforce configuration, not a Merchant Team login/role. |

## 5. Current Capability Inventory

| Capability | Status | Control depth | Consequence |
|---|---|---:|---|
| Merchant Owner/Admin/Staff management | LIVE | 3 — Access administration | Owner/Admin manage Merchant users; Owner is protected; Admin is limited to managing Staff and cannot create/assign Admin. |
| Workspace scope | LIVE | 3 — Scoped access | Membership and merchant role assignments limit access to selected active Workspace(s); changing scope invalidates sessions. |
| Staff section grants | LIVE | 3 — Scoped access | Staff requires one or more supported sections; Team is excluded from Staff-grantable sections. |
| Staff Store restriction | LIVE | 3 — Additional scope | Selected Store grants further restrict context and do not override other gates. |
| Credential reset | LIVE | 2 — Lifecycle control | Temporary password is hashed; forced change and session invalidation; audit excludes secret. |
| Disable/reactivate | LIVE | 2 — Lifecycle control | Access is revoked/disabled and can be restored with prior Workspace role scope; Owner cannot be mutated through this flow. |
| Internal employee management | LIVE | 3 — Separate authority system | Workspace-bound employee roles/direct grants and audited lifecycle are separate from merchant access. |
| Operational team assignment | LIVE / domain-specific | 2–3 | Confirmation staffing has its own roster/change workflow; do not generalize its controls to other worker domains. |
| Unified cross-domain workforce control | NOT ESTABLISHED | n/a | No single Team control surface was found that manages merchant users, internal employee authority, and all operational workers. |

## 6. Workflow & Lifecycle

1. Merchant Owner/Admin opens Merchant Team; the server derives authenticated Merchant context and checks team-manager authority.
2. On create/edit, backend normalizes identity/role/scope, resolves valid active Workspace and Store scope, writes role and section/Store grants transactionally, and records an audit event. Owner can create Admin or Staff; Admin can manage Staff only.
3. Workspace changes revoke old assignments and Store grants for removed Workspaces; material mutations increment session version. Disable revokes role assignments and deactivates membership; reactivation requires historical active Workspace scope.
4. Password reset hashes the supplied temporary password, marks must-change-password, increments session version and records a non-secret audit event.
5. Internal employee changes use separate Admin Employees endpoints and backend permission checks, including reason requirements on sensitive changes and protection for the Platform Owner.
6. Confirmation roster changes are managed through Confirmation’s domain-specific team workflow, not Merchant Team.

## 7. Value Recipient Map

- **Merchant Owner:** can delegate access across the Merchant account while retaining the Owner authority boundary.
- **Merchant Admin:** can maintain Staff access but cannot escalate colleagues to Admin/Owner.
- **Merchant Staff:** can receive least-scope Workspace, section, and Store access.
- **Wossol operators:** Internal Employee records support workspace-bound separation of duties, distinct from merchant identities.
- **Customers/operations:** indirect benefit is reduced likelihood of inappropriate access if all consuming services consistently enforce the defined gates; no measured incident reduction is available.

## 8. Control & Merchant Agency

Merchant Owner has the broadest Merchant Team authority; Admin manages Staff only. Owner is not mutable/disableable through the ordinary Team endpoints. Admin/Owner are effectively full Merchant sections in current context resolution, while Staff receives explicit section grants. Store restrictions layer under Workspace and section authority and do not independently authorize access. The UI reflects restrictions, but frontend visibility is not the authorization boundary: backend services/guards must decide each request.

Internal employee permissions are not controlled by the merchant Owner. The employee service requires permission checks in an active Workspace, constrains non-Platform-Owner changes to manageable Workspace scope, and protects self-authority changes and protected-owner authority. Department labels do not by themselves establish permission. Operational worker assignment is yet another authority boundary.

## 9. Transparency & Trust

Merchant member create/edit/access/reset/disable/reactivate actions emit audit events; password values are not written to audit metadata, and changes invalidate active sessions through `sessionVersion`. The UI provides role/scope summaries, confirmation dialogs, errors, and test-covered focus/dirty-dialog behavior. This evidence does not establish audit retention/monitoring guarantees or a user-visible complete event history. Internal employee mutations likewise include audit records and reason requirements for sensitive status/password/access operations.

## 10. Merchant Value Extraction

Team access administration can reduce reliance on shared credentials and platform support, and can narrow which merchant users can operate in which Workspaces, sections, and Stores. These are plausible operational benefits, not measured results. The real security value depends on consistency of downstream endpoint enforcement and correct configuration; a role editor alone is not evidence of end-to-end least privilege.

## 11. Feature Clusters

1. **Delegated administration:** Owner/Admin distinction and protected Owner.
2. **Composable merchant scope:** Merchant membership + Workspace role/membership + section grants + Store restriction.
3. **Lifecycle accountability:** reset, disable, reactivate, audit, and session invalidation.
4. **Separate Wossol authority plane:** Internal Employee Workspace roles and direct permissions.
5. **Domain-operational staffing:** Confirmation/Tracking assignments remain domain-owned.

The valuable cluster is composable, separately enforced access with lifecycle accountability—not the word “Team” or roster UI alone.

## 12. Merchant Journey / Old Way vs Wossol Way

An owner can invite/create a named merchant user, set role and operating scope, and later change or revoke access from the Merchant Portal. Admin delegation avoids granting all users Owner-level control. This is a plausible alternative to shared credentials/manual coordination; the audit did not measure setup time or errors. Internal hiring/access administration and confirmation staffing follow separate workflows and should not be included in a Merchant Team product claim.

## 13. Hidden / Non-Obvious Advantages

- Admin’s inability to create another Admin is enforced server-side by `assertRoleMutationAllowed`, not only by hiding a button.
- Workspace/section/Store constraints compose; a Store assignment is an additional restriction, not a standalone grant (Stores review, 2026-09-26).
- Scope and role mutations invalidate sessions to avoid relying on stale authorization state.
- Employee list’s “All Workspaces” mode is restricted to protected Platform Owner; other employee views are workspace bounded.
- `department` metadata is not itself authority; permission checks resolve role/direct permission grants.

## 14. Data & Intelligence Assets

Team records capture identity, role, Workspace assignment, Staff section grants, Store restrictions, lifecycle state and audit transitions. They establish configured access—not actual usage, productivity, trustworthiness, staffing quality, or team performance. These records are sensitive administrative data and should not be reframed as workforce intelligence without additional governed evidence and purpose.

## 15. Cross-Section Compound Advantages

- **Stores:** Store-specific Staff access is an added filter; Workspace/section/merchant authority still applies.
- **Orders/Confirmation:** merchant authorization and confirmation operational assignment are distinct; workers are not simply Merchant Team members.
- **Tracking/Delivery:** tracking workforce follows separate worker/team-lead concepts and must not be collapsed into Confirmation or merchant identities.
- **Finance:** access to Finance section does not alone prove action-level finance permissions or separation-of-duties controls; inspect Finance owner services.
- **Analytics/Market Center:** visible access does not imply that Team assignments create decision intelligence or market insight.
- **Products/Inventory/Commerce:** the same merchant scope model may contextualize access, but each owner service remains responsible for resource-level authorization.

## 16. Competitive Analysis

The competitive master treats basic team/user management and permissions as common operational infrastructure among direct competitors. This supports table-stakes classification, not a claim that competitors lack comparable depth or that their internal models match Wossol. Any differentiating claim would require evidence of superior scope composition, safe enforcement, usability, or outcomes; none is established comparatively here. (EV-TEAM-017.)

## 17. Marketing Intelligence

**Qualified current message:** “Give merchant users role-based access scoped by Workspace, section, and Store.” This describes configuration capability, not a guarantee of every downstream endpoint’s enforcement or a complete identity/workforce suite.

**Proof points:** Owner/Admin/Staff constraints; selected Workspace and Staff section scopes; additional Store restriction; auditable reset/disable flows; session invalidation.

Claim safety: GREEN for the specific configured Merchant Team workflows verified in source; YELLOW for “least-privilege across the platform” until endpoint coverage is systematically verified; RED for “one team manages everyone,” “complete workforce management,” or measured security/productivity outcomes.

## 18. Surprise Findings

1. “Team” names at least three independent product concepts: merchant user access, Wossol internal employee authority, and operational worker assignment.
2. Merchant Admin’s Staff-only mutation limit is enforced in the service layer.
3. Store access never substitutes for Merchant, Workspace, or section access.
4. Internal employee “All Workspaces” aggregation is specially protected and is not a general administrator capability.

## 19. Potential Category Reframes

Merchant Team is most precisely a **merchant access-administration surface**. Internal Employees are a **workspace-scoped platform authority system**. Confirmation/Tracking roster tools are **operational workforce assignment**. This taxonomy clarifies product boundaries; it is not a final market-category or positioning decision.

## 20. Brand Evidence

Potential evidence for Control and Accountability: role boundaries, composable scopes, explicit lifecycle controls, audit events, and session invalidation. Potential evidence for Clarity: separate merchant, employee, and worker authority models. Gaps remain around proving uniform downstream authorization, audit review/retention, and observed outcomes.

## 21. Weaknesses / Risks / Gaps

- **P1 — endpoint enforcement coverage not audited exhaustively:** Team scopes are valuable only where all domain APIs enforce each relevant gate; a comprehensive endpoint-by-endpoint authorization matrix was beyond this section’s targeted pass.
- **P1 — overloaded “Team” language:** Merchant Team, Confirmation Team, Tracking team leads/workers, and Internal Employees can be conflated by users or marketing; labels and claims should name the relevant identity domain.
- **P2 — reset-password delivery unknown:** Repository shows supplied temporary password hashing and must-change-password state, not a secure delivery/rotation channel or production process.
- **P2 — retained grant semantics on disable/reactivate:** Disable revokes Workspace role assignments but does not revoke Merchant section or Store grant rows. Reactivation recreates roles for historical active Workspaces, so those still-live grants can become effective again. UI must make retained prior scope clear; validate that this retention behavior is intentional and tested across Staff/Admin transitions.
- **P2 — validation and cross-merchant isolation:** Tests and service paths should continue to cover malformed IDs, tenant boundaries, concurrent grants, and all scope combinations; this audit did not establish production penetration-test evidence.
- **P3 — outcomes/competition:** No controlled comparison or measured reduction in shared credentials, access errors, or administration time was available.

## 22. Strategic Implications

Team can support trust in multi-user operations when permissions remain understandable and consistently enforced. Its strategic importance is as enabling control infrastructure, not as an independent intelligence capability. The separate employee/worker systems suggest domain-specific authority models rather than one universal workforce product; preserve that architecture in future synthesis.

## 23. Research Questions

1. Which active endpoints enforce Merchant, Workspace, section, and Store scope, and where do those gates intentionally differ?
2. Is retaining section and Store grants across disable/reactivate intentional, and is that prior scope made clear before reactivation?
3. How are temporary credentials delivered, rotated, and audited in production?
4. How do Confirmation and Tracking worker identities authenticate and how are their assignment changes audited?
5. What user research or operational metrics establish that scope configuration is understood and reduces access errors?

## 24. Evidence Register

| ID | Claim supported | Source / evidence class |
|---|---|---|
| EV-TEAM-001 | Merchant Team UI role/scope workflow, labels, dialogs and retry behavior | P1 `apps/frontend/src/app/merchant/team/page.tsx`; `team-data.ts` |
| EV-TEAM-002 | Merchant member create/list/update/workspace access/reset/reactivate service behavior | P1 `apps/backend/src/modules/merchant-portal/merchant-portal.service.ts` |
| EV-TEAM-003 | Authenticated Team endpoints route requests to service authorization | P1 `apps/backend/src/modules/merchant-portal/merchant-portal.controller.ts` |
| EV-TEAM-004 | Admin only manages Staff; owner protected; Staff requires supported section grant | P1 merchant portal service `assertTeamManager`, `assertRoleMutationAllowed`, `normalizeStaffSectionKeys`, `findTeamMember` |
| EV-TEAM-005 | Team workflow behavior, guardrails and UX regressions | P2 `apps/frontend/src/app/merchant/team/merchant-team-workflow.spec.ts`; `apps/backend/src/modules/merchant-portal/merchant-portal.service.spec.ts` |
| EV-TEAM-006 | Merchant roles, section access, Store filters compose in actor context | P1 merchant portal service context resolution; `apps/backend/src/modules/auth/merchant-section-access.service.ts`; `merchant-store-access.service.ts` |
| EV-TEAM-007 | Merchant user/role/workspace/section/store assignment schema | P1 `apps/backend/prisma/schema.prisma` models `MerchantUser`, `UserRoleAssignment`, `WorkspaceMembership`, `MerchantSectionPermissionAssignment`, `MerchantUserStoreAccess` |
| EV-TEAM-008 | Shell Team navigation and section presentation are UI controls, not authorization | P1 `apps/frontend/src/app/merchant/MerchantShell.tsx`; backend endpoint/service checks |
| EV-TEAM-009 | Internal Employees use separate identity and workspace role/direct-grant authority | P1 `apps/backend/src/modules/admin-employees/admin-employees.service.ts`; Prisma schema |
| EV-TEAM-010 | Internal employee service checks permissions, bounds Workspace authority, audits changes, and protects owner/self changes | P1 same service and `admin-employees.controller.ts` |
| EV-TEAM-011 | Internal employee catalog and role assignments are distinct from merchant team roles | P1 `apps/backend/src/modules/permissions/permission-catalog.ts`; P2 `admin-employees.service.spec.ts` |
| EV-TEAM-012 | Approved Internal Employee / Workspace role architecture and explicit limits | P3 `docs/architecture/Internal Employees, Workspace Access, Roles & Permissions Foundation V1.md` |
| EV-TEAM-013 | Confirmation roster is a separately managed operational team | P1 `apps/frontend/src/app/merchant/confirmation/team/page.tsx`; associated confirmation-team APIs/service/specs |
| EV-TEAM-014 | Confirmation Worker and Team Lead concepts are domain-specific | P3 `docs/wossol-system-design/01-system-design/admin-platform/confirmation/Worker System (Final V1).md`; `Team Lead System.md`; `Confirmation Team System.md` |
| EV-TEAM-015 | Tracking worker/team lead is another distinct operational workforce domain | P3 `docs/wossol-system-design/01-system-design/admin-platform/tracking/Tracking Worker — Official System Design Document.md`; `Tracking Team Lead.md` |
| EV-TEAM-016 | Product source branch/commit and clean state | Read-only Git state check at audit start/final verification |
| EV-TEAM-017 | Basic team/user management is common category capability; depth is the relevant comparison | P3 `01-competitive/WOSSOL_COMPETITIVE_INTELLIGENCE_MASTER_V1.md` |

## 25. Confidence Assessment

- Merchant Team current-source role model and service workflows: **High**.
- Store as an additional restriction: **High**, corroborated by current source and accepted Stores review (2026-09-26).
- Internal Employees are a separate authority plane: **High**.
- Operational worker boundaries: **Medium-High**; separate frontend/design domains inspected, but not every worker API/auth path was exhaustively traced here.
- End-to-end authorization consistency across every product endpoint: **Medium-Low / not established**.
- Differentiation or business outcomes: **Low / not established**.

## 26. Source-State & Verification Notes

Product checkout was clean at `de2bb9bad7c39ce8f6ccf2d237a36cea1a82fb98` on `dev/wossol-integration` and matched `origin/dev/wossol-integration` at inspection. Product files were not modified. Focused source/spec inspection covered Merchant Team UI/service/controller/schema and tests; Internal Employee service/controller/spec/schema; Shell navigation; and Confirmation team UI plus current worker specifications. No production deployment or live data was queried.

## 27. Methodology / Retroactive Review

No methodology change is proposed. The Stores review record dated 2026-09-26 is applied as a cross-section constraint: Store grants remain additional restrictions and are never treated as independent authorization. No retroactive audit correction is triggered by this Team audit.

## 28. Open Questions / Blockers

No blocker prevented this source audit. The unresolved research questions in §23 and endpoint-coverage limitation in §21 remain open; they are not silently treated as product defects or proof of correct production behavior.

## 29. Audit Limitations

Repository inspection cannot confirm deployed version, production flags/data, operational behavior, external credential delivery, or actual authorization decisions in every downstream domain. Worker systems were checked for boundary and design evidence, not as complete Confirmation or Tracking audits. Competitor comparison relies on the current Intelligence master rather than fresh primary-source competitor research.

## 30. Section-Level Claim Boundaries

Supported: merchant Owner/Admin/Staff access management with Workspace, section, and additional Store scope; separately, Workspace-scoped internal employee role/direct-permission administration; separately, domain-owned operational roster configuration.

Not supported: one unified Team controls all Wossol and merchant personnel; every service enforces all configured scopes without exception; Team data measures performance or creates intelligence; competitors lack equivalent controls; access management measurably improves security/productivity.

## 31. Recommended Next Evidence

Build a cross-domain authorization matrix from route → guard → service scope predicate → resource ownership, prioritizing high-risk mutations and verifying every merchant gate. Separately document the real temporary-password delivery process and reactivation semantics. Do not fold those investigations into a broader marketing claim before evidence is collected.

## 32. Canonical Section Takeaway

Wossol’s Team capability is a set of **three distinct control domains**, not a unified workforce manager: merchant-user access administration, internal employee authority, and operational worker assignment. Merchant Team’s composable scopes and audited lifecycle are useful enabling controls, but their security value depends on downstream enforcement and they are not, by themselves, differentiated or outcome-proven.
