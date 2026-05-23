---
title: AzResiliency — BCDR & Composite SLA Calculator
description: Composite SLA + BCDR planning with single point of failure detection.
category: herramientas
slug: azresiliency
lang: en
order: 125
readMinutes: 4
tags: [Reliability, PRO]
lastUpdated: 2026-05-23
sources:
  - https://learn.microsoft.com/azure/reliability/
  - https://learn.microsoft.com/azure/architecture/framework/resiliency/
---

> 🔒 Requires PRO — composite SLA modeling and BCDR live in PRO.

## AzResiliency

AzResiliency computes the composite SLA of your architecture (product of per-component SLAs), models BCDR with RPO/RTO targets, and detects single points of failure (SPOFs). Built to answer "what's the real SLA of this workload?" without pulling out a calculator.

## What it's for

- Compute composite SLA by multiplying per-component SLAs
- Detect SPOFs (a component with no redundancy that takes everything down)
- Model RPO/RTO targets vs what your architecture actually delivers
- Generate a documented BCDR plan

## Getting started

1. **Step 1**: Open AzResiliency and add workload components (or import them from AzDraw).
2. **Step 2**: Assign per-component SLA (Microsoft default or custom) and redundancy config.
3. **Step 3**: Review composite SLA + SPOF list + recommendations.

## Common use cases

- **Pre-prod review**: validate the realistic SLA before promising it to the client
- **BCDR planning**: document RPO/RTO targets and validate the architecture meets them
- **SPOF cleanup**: catch the single-region storage account in a "multi-region" workload

## Tips

- Composite SLA is the PRODUCT, not the average — 99.99% × 99.95% × 99.9% ≈ 99.84%
- Availability zones add to SLA only if the service supports them natively
- BCDR you never test isn't BCDR — schedule the game day

## FAQ

**Does it use official Microsoft SLAs?**
Yes, per-SKU SLAs come from the official SLA summary. You can override them.

**Does it generate the BCDR runbook?**
It generates the structure. Operational detail is on you.

## Related

- [AzReview — Well-Architected Review](/AzReview)
- [AzLanding — Landing Zone Studio](/AzLanding)
- [Azure Reliability](https://learn.microsoft.com/azure/reliability/)
- [WAF — Reliability pillar](https://learn.microsoft.com/azure/architecture/framework/resiliency/)
