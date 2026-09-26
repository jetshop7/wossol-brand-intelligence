# Team Review — 2026-09-26

## Review metadata
- Section: Team
- Reviewed intelligence commit: `a32560ac99c4b04ba0077f8398d6a17fb167c67c`
- Product evidence commit: `de2bb9bad7c39ce8f6ccf2d237a36cea1a82fb98`
- Reviewer: ChatGPT — Strategic Reviewer / Quality Gate
- Decision: **ACCEPT WITH OPEN PRODUCT ISSUES**
- Full re-audit required: No

## What passes

The audit correctly rejects “Team” as one universal workforce/authority system. It separates merchant-user access administration, Wossol internal employee authority, and domain-owned operational worker assignment.

Targeted Product verification confirms Merchant Team is a real access-administration workflow rather than only UI visibility. Merchant membership, Workspace assignments, Staff section grants and Store restrictions compose as separate gates. Store scope is explicitly an additional restriction and never grants Merchant, Workspace or section authority.

Owner/Admin/Staff boundaries are appropriately characterized. Owner is protected by the ordinary Merchant Team mutation path; Admin delegation is narrower and does not imply Owner-level authority. Backend enforcement, not hidden frontend controls, is the relevant evidence.

Lifecycle/accountability evidence passes. Material Merchant Team mutations increment session version, password resets store hashes rather than plaintext and require a later password change, and audited changes avoid recording the supplied secret. These controls support bounded Accountability/Control evidence, not a claim of complete security governance.

The Internal Employees boundary also passes. Product evidence establishes a separate Wossol authority plane with Workspace-bounded roles/direct permissions, protected Platform Owner behavior, permission-gated management, reason requirements on sensitive changes and audited lifecycle. Department metadata itself is not authority.

Operational worker concepts remain domain-owned. Confirmation/Tracking staffing must not be marketed as if Merchant Team controls those workers or as one unified workforce suite.

The audit appropriately identifies a material lifecycle nuance: disabling a Merchant member revokes active role assignments and relevant Workspace membership state but does not automatically revoke all retained section/Store grant rows. Reactivation reconstructs roles from historical active Workspace assignments. Retained grants can therefore become effective again when the prerequisite authority returns. This is correctly preserved as an open Product/governance issue rather than silently described as fresh least-privilege re-provisioning.

The audit is also appropriately conservative about downstream enforcement. Configured Team scope is not proof that every product endpoint enforces every intended gate; owner-domain authorization remains authoritative.

Strategic classification passes. These are meaningful Control/Accountability foundations, but basic user/role administration is table-stakes and no comparative superiority, security-outcome improvement, workforce intelligence or moat is established.

Verification discipline passes: the canonical audit records 36 focused backend tests, 19 frontend workflow tests, backend/frontend typechecks and `git diff --check` passing, with the Product repository unchanged.

## Open product issues retained

1. **Authorization coverage:** build/maintain a cross-domain route → guard → service/resource-scope matrix, prioritizing high-risk mutations, before claiming platform-wide least privilege.
2. **Disable/reactivate retained grants:** explicitly decide whether section/Store grants should survive disable and automatically regain effect on reactivation; expose/review prior scope before restoration if that is intended.
3. **Temporary credential delivery:** secure delivery, expiry/rotation and production handling of temporary passwords are not established by repository evidence.
4. **Team terminology:** Merchant Team, Internal Employees, Confirmation workers and Tracking workers should remain clearly distinguished in product language and marketing.
5. **Audit governance:** audit-event creation is established, but retention, review/monitoring, alerting and complete user-visible history are not.
6. **Production security:** penetration testing, concurrent grant behavior and live tenant-isolation outcomes remain unverified.
7. **Operational worker authority:** worker authentication, assignment lifecycle and audit depth remain owner-domain questions and should not inherit Merchant Team conclusions.

These issues bound the security/workforce claims but do not invalidate the Team intelligence audit.

## Claim / strategic safety

Safe present territory is merchant Owner/Admin/Staff access administration with Workspace scope, Staff section grants, additional Store restrictions, lifecycle controls, session invalidation and audit events; separately, Workspace-scoped internal employee authority; separately, domain-owned operational staffing.

Do not claim one Team manages all personnel, complete workforce management, guaranteed least privilege across every endpoint, fresh permission review on reactivation, complete security/audit compliance, workforce intelligence, measured productivity/security improvement, or competitor inferiority.

## Methodology impact

No methodology change is required.

## Retroactive impact

No prior accepted section requires correction. Stores remains consistent: Store grants are additional restrictions rather than independent authority. Finance and other sensitive domains must continue to rely on their own action-level authorization rather than treating section visibility as sufficient authority. Later synthesis should use Team as bounded evidence for Control/Accountability, not as proof of universal governance.

## Acceptance

**Team passes the Director Quality Gate and is accepted with the open Product issues above.**

No Codex correction or re-audit is required.

The next section in the canonical sequence is **Sourcing / Network**.
