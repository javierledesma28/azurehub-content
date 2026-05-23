---
title: AzCost — Pricing & FinOps Calculator
description: Calculator with official Microsoft pricing, comparable by region and SKU.
category: herramientas
slug: azcost
lang: en
order: 90
readMinutes: 4
tags: [FinOps, Free with signup]
lastUpdated: 2026-05-23
sources:
  - https://azure.microsoft.com/pricing/calculator/
  - https://learn.microsoft.com/azure/cost-management-billing/
---

## AzCost

AzCost is the Azure pricing calculator with official Microsoft data, comparable by region, SKU, and scenario (reservation / PAYG / spot). Built to price workloads before you turn the resource on, not to discover the cost on the invoice.

## What it's for

- Price a workload using up-to-date official pricing
- Compare regions (West Europe vs East US 2 vs North Europe)
- Model 1y / 3y reservations vs PAYG vs Spot
- Export the detailed quote for the client

## Getting started

1. **Step 1**: Open AzCost and add the workload resources (VM, AKS, Storage, etc.).
2. **Step 2**: Set region, SKU, hours/month, and model (PAYG / 1y RI / 3y RI / Spot).
3. **Step 3**: Review the monthly total and export PDF/CSV.

## Common use cases

- **Client quote**: build the TCO of a project before closing the deal
- **Region compare**: validate whether moving from West Europe to North Europe is worth it
- **What-if**: how much you'd save with 3y reservations vs PAYG on the prod AKS

## Tips

- Reservations only make sense for steady workloads — Spot doesn't apply to critical production
- Prices change: re-run the quote if a deal sits idle >30 days
- For analysis on what you ALREADY have, use AzFinOps

## FAQ

**Does it have Dev/Test pricing?**
Yes — select it as a separate model.

**Does it support Enterprise Agreements?**
The calculator shows list pricing. Apply your EA discount on top.

## Related

- [AzFinOps — Rightsizing advisor](/AzFinOps)
- [Azure Pricing Calculator (official)](https://azure.microsoft.com/pricing/calculator/)
- [Microsoft Cost Management](https://learn.microsoft.com/azure/cost-management-billing/)
