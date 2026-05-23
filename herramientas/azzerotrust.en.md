---
title: AzZeroTrust — Zero Trust Maturity Assessment
description: Assessment of Microsoft's Zero Trust model with per-pillar scoring and an actionable roadmap.
category: herramientas
slug: azzerotrust
lang: en
order: 115
readMinutes: 4
tags: [Security, Free with signup]
lastUpdated: 2026-05-23
sources:
  - https://learn.microsoft.com/security/zero-trust/
  - https://www.microsoft.com/security/business/zero-trust
---

## AzZeroTrust

AzZeroTrust is the maturity assessment for Microsoft's Zero Trust model. Covers the 6 pillars (Identity, Endpoints, Apps, Data, Infrastructure, Network), gives you per-pillar scoring, and an actionable roadmap to move from "Traditional" to "Optimal".

## What it's for

- Measure Zero Trust maturity across 6 pillars
- Identify the weakest pillar to prioritize
- Generate a "Traditional → Advanced → Optimal" roadmap
- Document the assessment as a baseline for future reviews

## Getting started

1. **Step 1**: Open AzZeroTrust from the Hub.
2. **Step 2**: Answer the per-pillar questionnaire (~10 minutes per pillar).
3. **Step 3**: Review scoring + roadmap. Export Markdown for your repo/Confluence.

## Common use cases

- **CISO baseline**: measure where the org stands today vs the Zero Trust target
- **Pre-audit**: identify gaps before a security audit
- **Quarterly**: re-measure each quarter to track progress

## Tips

- Don't run the assessment alone — architect + ops as a pair is ideal
- "Optimal" isn't for everyone: define a realistic target for your org (Advanced is usually enough)
- Combine with AzRBAC and AzCompliance to close specific gaps

## FAQ

**Does it replace Microsoft's Zero Trust Assessment Tool?**
It complements it. Ours lives integrated with the rest of the Hub (RBAC, Compliance).

**Can I compare across tenants?**
Each workspace has its own assessment. Cross-tenant comparison is manual today.

## Related

- [AzRBAC — RBAC Designer](/AzRBAC)
- [AzCompliance — Regulatory Mapper](/AzCompliance)
- [Microsoft Zero Trust](https://learn.microsoft.com/security/zero-trust/)
