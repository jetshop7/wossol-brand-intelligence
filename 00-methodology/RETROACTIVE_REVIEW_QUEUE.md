# RETROACTIVE REVIEW QUEUE

Use this queue when a methodology improvement discovered in a later audit may materially affect earlier section intelligence.

Do not recursively interrupt the current audit. Record the impact, finish the current section coherently, then process the queue deterministically.

| Queue ID | Methodology Change | Affected Section | Reason | Priority | Status | Result |
|---|---|---|---|---|---|---|
| RR-V12-001 | METH-2026-09-26-001 | Orders | Re-extract provenance-to-outcome, source continuity, downstream analytics/decision value | CRITICAL | QUEUED | Preserve accepted evidence; incremental V1.2 re-audit |
| RR-V12-002 | METH-2026-09-26-001 | Messaging / WhatsApp / Messenger Order Capture | Re-extract context continuity, merchant-job removal, deep-link/return path, provenance handoff | CRITICAL | QUEUED | Incremental V1.2 re-audit |
| RR-V12-003 | METH-2026-09-26-001 | Analytics / Decision Center | Reclassify Merchant vs Decision Intelligence; decision-effort reduction; operational→economic→decision chain | CRITICAL | QUEUED | Incremental V1.2 re-audit |
| RR-V12-004 | METH-2026-09-26-001 | Market Center | Reclassify Market Intelligence and Analytics/other-domain inputs; current vs future intelligence depth | CRITICAL | QUEUED | Incremental V1.2 re-audit |
| RR-V12-005 | METH-2026-09-26-001 | Inventory | Re-extract reconciliation/work removal and Inventory→Orders→Finance→Analytics compound value | HIGH | QUEUED | Incremental V1.2 re-audit |
| RR-V12-006 | METH-2026-09-26-001 | Finance | Re-extract economic-truth reconciliation, external/manual expense burden, downstream decision value | HIGH | QUEUED | Incremental V1.2 re-audit |
| RR-V12-007 | METH-2026-09-26-001 | Advertising | Trace provider evidence→Order→delivered/economic outcomes; avoid provider-metric inflation | HIGH | QUEUED | Incremental V1.2 re-audit |
| RR-V12-008 | METH-2026-09-26-001 | Integrations / Commerce Channels | Re-extract setup/tool consolidation, source identity, context continuity, downstream provenance | HIGH | QUEUED | Incremental V1.2 re-audit |
| RR-V12-009 | METH-2026-09-26-001 | Products | Re-extract cross-domain identity/provenance and downstream operational/economic/analytics value | HIGH | QUEUED | Preserve prior Product contract issue |
| RR-V12-010 | METH-2026-09-26-001 | Customers | Re-extract Customer operational intelligence and cross-domain outcome value | HIGH | QUEUED | Incremental V1.2 re-audit |
| RR-V12-011 | METH-2026-09-26-001 | Confirmation | Re-extract merchant-job removal, control, accountability, outcome evidence, analytics compound value | HIGH | QUEUED | Incremental V1.2 re-audit |
| RR-V12-012 | METH-2026-09-26-001 | Tracking / Delivery | Re-extract operational truth→economic truth and merchant effort reduction | HIGH | QUEUED | Incremental V1.2 re-audit |
| RR-V12-013 | METH-2026-09-26-001 | Home | Re-extract decision-effort reduction, action routing, cross-domain command-center value | MEDIUM | UPDATED | Incremental V1.2 audit recorded in `02-section-intelligence/HOME.md`; preserved authoritative UI contract contradiction, identified conditional Analytics materialization/event on Summary GET and inherited Notifications authorization/projection concerns; Director Quality Gate pending |
| RR-V12-014 | METH-2026-09-26-001 | Stores | Re-extract commerce identity, consolidation, scoping, provenance and connected value | MEDIUM | UPDATED | Incremental V1.2 audit recorded in `02-section-intelligence/STORES.md`; preserved 2026-09-26 Director acceptance/open issues, traced Store-scoped Shopify COD session → Order value, flagged Prisma/schema-to-migration mismatch; current Director Quality Gate pending |
| RR-V12-015 | METH-2026-09-26-001 | Team | Re-extract coordination effort, accountability, control and cross-domain action history | MEDIUM | UPDATED | Incremental V1.2 re-audit recorded in `02-section-intelligence/TEAM.md`; prior accepted findings retained; no Team-owned committed source delta; downstream enforcement, grant-resurrection, credential delivery, and outcomes remain qualified; Director Quality Gate pending |
| RR-V12-016 | METH-2026-09-26-001 | Sourcing / Network | Re-extract market-access work removal and future compound/network value with strict current/future split | MEDIUM | UPDATED | V1.2 incremental re-audit recorded in `02-section-intelligence/SOURCING_NETWORK.md`; Quality Gate accepted with open Product issues in `04-review-history/SOURCING_NETWORK_V1_2_MIGRATION_REVIEW_2026-09-27.md`; supplier-payment boundary carried into Local Pickup RR-V12-017 |
| RR-V12-017 | METH-2026-09-26-001 | Local Pickup | Apply V1.2 merchant-job, friction, continuity and compound-value lenses | MEDIUM | UPDATED | V1.2 incremental re-audit recorded in `02-section-intelligence/LOCAL_PICKUP.md`; retained accepted lifecycle findings, reran backend 35/35 and UI 15/15 specs, added supplier-payment UI/ledger contradiction and inbound-value synthesis; current Director Quality Gate pending |
| RR-V12-018 | METH-2026-09-26-001 | External Shipping | Apply V1.2 merchant-job, tool-consolidation, provenance and downstream outcome lenses | MEDIUM | UPDATED | Incremental V1.2 re-audit recorded in `02-section-intelligence/EXTERNAL_SHIPPING.md`; prior accepted truths retained; bounded coordination/evidence consolidation added without measured-efficiency or intelligence claims; Product had no relevant committed delta; backend focused tests 95/95 pass, frontend harness partial; Director Quality Gate pending |
| RR-V12-019 | METH-2026-09-26-001 | Shopify Embedded App / COD Commerce Experience | Apply V1.2 context continuity, setup reduction, provenance and downstream value lenses | HIGH | QUEUED | Incremental V1.2 re-audit |
| RR-V12-020 | METH-2026-09-26-001 | Notifications | Apply V1.2 coordination/recovery effort and action-continuity lenses | LOW | QUEUED | Incremental V1.2 re-audit |
| RR-V12-021 | METH-2026-09-26-001 | Support / Internal Chat | Apply V1.2 context continuity, coordination effort and operational evidence lenses | LOW | QUEUED | Incremental V1.2 re-audit |


Status values: QUEUED; IN REVIEW; UPDATED; REVIEWED - NO MATERIAL CHANGE; BLOCKED.
