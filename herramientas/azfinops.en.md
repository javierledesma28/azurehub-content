---
title: AzFinOps — Rightsizing & Reservations Advisor
description: Find the money you're leaving on the table with live tenant analysis.
category: herramientas
slug: azfinops
lang: en
order: 95
readMinutes: 4
tags: [FinOps, PRO]
lastUpdated: 2026-05-23
sources:
  - https://learn.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices
  - https://learn.microsoft.com/azure/advisor/advisor-cost-recommendations
---

> 🔒 Requires PRO — AzFinOps analyzes your connected tenant live.

## AzFinOps

AzFinOps is the FinOps advisor that looks at your live tenant and tells you where you're leaving money on the table: over-provisioned VMs, unapplied reservations, orphaned resources. It doesn't charge you for a "FinOps audit" — it surfaces top opportunities in minutes.

## What it's for

- Detect over-provisioned VMs using real usage data
- Compute ROI for moving PAYG → 1y / 3y Reservations
- Identify orphaned disks, unattached IPs, resources with no owner tag
- Prioritize top opportunities by estimated monthly savings

## Getting started

1. **Step 1**: Connect the tenant (Reader + Cost Management Reader).
2. **Step 2**: Open AzFinOps and choose the scope.
3. **Step 3**: Review recommendations sorted by estimated monthly savings.

## Common use cases

- **Quarterly cleanup**: run AzFinOps each Q and apply the top 10
- **Pre-renewal**: before buying reservations, validate which ones really apply
- **Client quick win**: kick off a new engagement showing 3 concrete savings

## Tips

- Microsoft Advisor recommendations are useful but generic — AzFinOps prioritizes by real impact
- Before accepting a downsize, look at CPU/RAM peaks for the last month, not just the average
- 3y reservations only if you trust the workload survives 3 years

## FAQ

**Does it look at real metrics or just SKU?**
Real metrics (CPU, memory, network) for the last 30 days via Azure Monitor.

**Can I apply recommendations from the Hub?**
No — we give you the az/Bicep command ready, but apply happens in your pipeline.

## Related

- [AzCost — Pricing calculator](/AzCost)
- [Azure Advisor Cost Recommendations](https://learn.microsoft.com/azure/advisor/advisor-cost-recommendations)
- [Microsoft Cost Management best practices](https://learn.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices)
