---
title: AzCompliance — Regulatory Compliance Mapper
description: ENS, NIS2, ISO 27001 without the pain — automated audit on your tenant.
category: herramientas
slug: azcompliance
lang: en
order: 120
readMinutes: 5
tags: [Compliance, PRO]
lastUpdated: 2026-05-23
sources:
  - https://www.boe.es/buscar/act.php?id=BOE-A-2022-7191
  - https://learn.microsoft.com/compliance/
---

> 🔒 Requires PRO — the automated audit runs against your connected tenant.

## AzCompliance

AzCompliance is the multi-framework regulatory mapper: ENS Medio + Alto (75 controls each), NIS2, and ISO 27001 (coming soon). Runs 13+ automated checks on your tenant, lets you complete the manual review with evidence, and exports an audit-ready PDF.

## What it's for

- Audit your Azure tenant against ENS Medio or ENS Alto
- Mix automated checks (auth, network, encryption, monitoring, backup) with manual review
- Upload evidence (PDF, screenshots) per control
- Export an audit-ready PDF report
- Schedule recurring audits with drift alerts (Enterprise)

## Getting started

1. **Step 1**: Connect the tenant (Reader on subscriptions).
2. **Step 2**: Open AzCompliance, pick the framework (ENS Medio / Alto), pick the scope.
3. **Step 3**: Run the audit. Review automated controls, complete manual ones with verdict + note + evidence. Export PDF.

## Common use cases

- **Pre-audit ENS**: close gaps a month before the formal audit
- **Continuous compliance**: weekly schedule with alerts if the score drops
- **Cross-tenant compare** (Enterprise): same framework, multiple tenants, drift visible

## Tips

- `partial` controls need your manual review — they're not failures, they're "we can't tell on our own"
- Always upload evidence — the auditor will ask for it and the PDF includes it
- Configure the drift threshold to whatever you accept (default 85%)

## FAQ

**Does it replace the auditor?**
No — it prepares you for the auditor. The formal audit is still human.

**Does it cover NIS2 / ISO 27001 today?**
The infrastructure is multi-framework from day 1. ENS Medio + Alto are live. NIS2 and ISO 27001 come next phase.

## Related

- [AzPolicy — Policy Builder](/AzPolicy)
- [AzZeroTrust — Zero Trust Assessment](/AzZeroTrust)
- [AzReview — Well-Architected Review](/AzReview)
- [ENS — RD 311/2022 (BOE)](https://www.boe.es/buscar/act.php?id=BOE-A-2022-7191)
- [Microsoft Compliance](https://learn.microsoft.com/compliance/)
