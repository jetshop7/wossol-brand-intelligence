# Products — Merchant-value candidates (first pass)

Evidence: code-supported paths, not live tests or proven competitor differentiation. Provider integration and internal operations are excluded.

| Rank | Merchant outcome candidate | Evidence | Validation needed |
|---|---|---|---|
| 1 | Manage multi-option Products with distinct Variant prices, SKUs, weights and images | Product create/edit and Variant edit UI | Live multi-Variant journey and market comparison |
| 2 | Understand stock availability without treating unknown quantity as zero | Product quantity projection and refresh | Freshness, failure and user comprehension |
| 3 | Identify and resolve incomplete Shopify Product/Variant links | Attention badge, Product Detail, Connections | Live Shopify integration |
| 4 | Find and organize Products by Product/Variant/SKU, category and connection state | Products list search/filter/sort | Search pagination and usability |
| 5 | Protect Order history when deleting Products or Variants | Historical OrderItem deletion guard | Merchant expectation and archive alternative |

**Not marketing claims:** provider API/identity, internal retries, audit trails, compensation or implementation complexity. No claims of real-time inventory, quantified time savings, automatic Shopify publication, zero errors or competitive uniqueness without proof.

**Known caveats:** payment-settings save and Variant image updates may partially succeed; provider-first deletion has partial-failure risk (RISK-008).