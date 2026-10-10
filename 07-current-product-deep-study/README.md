# Current Product Deep Study — V2

This is the **current-code, merchant-journey-led** product investigation workspace, separate from historical intelligence and review history.

## Shared controls
- **Protocol:** `WOSSOL_CURRENT_PRODUCT_DEEP_STUDY_PROTOCOL_V2.md` — approved V2 document supplied in the study conversation; **repository copy not yet committed**. Do not substitute the older protocol.
- [Pre-launch risk and defect register](PRE_LAUNCH_RISK_AND_DEFECT_REGISTER.md) — single shared register across all sections. Do not duplicate risk records in section-specific files.
- **Code source:** `jetshop7/wossol-platform`; pin the inspected branch and commit in each section.

## Sections
- [Home](sections/home/HOME.md) — existing Home study, relocated without changing content.
- [Products](sections/products/PRODUCTS.md) — previous study retained; **REOPENED / V2 coverage audit in progress**.
  - [Capability coverage](sections/products/CAPABILITY_COVERAGE.md)
  - [Merchant journeys](sections/products/MERCHANT_JOURNEYS.md)
  - [Cross-system trace](sections/products/CROSS_SYSTEM_TRACE.md)

## Folder convention
Create `sections/<section-slug>/` when beginning a new section. Use a primary `<SECTION>.md` study and supporting coverage/journey/trace evidence only when needed. No empty folders or redundant documents. Maintain **one shared protocol and one shared risk register**.

## V2 closure
No section is considered complete merely because its summary exists. Require independent code discovery, merchant-visible journey reconstruction, end-to-end tracing, value extraction, historical reconciliation, explicit evidence grades, and a documented PASS / CONDITIONAL / FAIL decision. Unobserved UI and unexecuted tests remain unverified.

## Migration
Original Home and Products documents and the three initial Products V2 audit documents were relocated into section folders without content edits. Git history preserves their earlier paths.
