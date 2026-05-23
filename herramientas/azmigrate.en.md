---
title: AzMigrate — Migration Assessment & Planner
description: A migration plan that lands on first try — 6 Rs, wave planning, cost projection.
category: herramientas
slug: azmigrate
lang: en
order: 130
readMinutes: 4
tags: [Migration, PRO]
lastUpdated: 2026-05-23
sources:
  - https://learn.microsoft.com/azure/cloud-adoption-framework/migrate/
  - https://learn.microsoft.com/azure/migrate/
---

> 🔒 Requires PRO — migration assessment and wave planning are PRO.

## AzMigrate

AzMigrate is the Azure migration assessment: classify each workload under the 6 Rs framework (Rehost, Replatform, Refactor, Rebuild, Repurchase, Retire), build the wave plan, map dependencies, and project costs.

## What it's for

- Classify workloads under the 6 Rs
- Wave planning (what migrates first, what depends on what)
- Dependency mapping between workloads
- Cost projection for the Azure target state

## Getting started

1. **Step 1**: Open AzMigrate and load the inventory (manual, CSV, or import from Azure Migrate).
2. **Step 2**: Classify each workload under the 6 Rs.
3. **Step 3**: Build waves, map dependencies, generate the cost projection.

## Common use cases

- **Migration kickoff**: produce the plan you show the executive sponsor
- **Replan**: migration got stuck and you need to re-prioritize
- **Cost justification**: cost to migrate + cost to operate in Azure

## Tips

- "Rehost" is the fastest but lowest-cloud-value option — balance it
- Wave planning depends on dependency mapping — invest there
- Combine with AzCost to validate the target projection
- "Retire" is usually the most profitable R and the most forgotten

## FAQ

**Does it replace Azure Migrate?**
No — Azure Migrate is Microsoft's discovery + replication tool. AzMigrate is the planner that sits on top.

**Does it cover on-prem → Azure?**
Yes — that's the main case. It also covers cloud-to-cloud and datacenter consolidation.

## Related

- [AzLanding — Landing Zone Studio](/AzLanding)
- [AzCost — Pricing calculator](/AzCost)
- [CAF Migrate](https://learn.microsoft.com/azure/cloud-adoption-framework/migrate/)
- [Azure Migrate (official)](https://learn.microsoft.com/azure/migrate/)
