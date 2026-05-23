---
title: AzPolicy — Policy & Initiative Builder
description: Design Azure Policies and initiatives and compare them against production.
category: herramientas
slug: azpolicy
lang: en
order: 100
readMinutes: 4
tags: [Govern, PRO]
lastUpdated: 2026-05-23
sources:
  - https://learn.microsoft.com/azure/governance/policy/overview
---

> 🔒 Requires PRO — drift detection and scheduled audits depend on the connected tenant.

## AzPolicy

AzPolicy is the visual builder for Azure Policies and initiatives. You design your org's policy set, compare it against what's deployed in the tenant to detect drift, and configure scheduled audits with email alerts (Enterprise).

## What it's for

- Design policies and initiatives without fighting JSON
- Compare the design vs what's deployed in production (drift detection)
- Spot "rogue" policies (deployed but not in the design)
- Schedule daily/weekly audits with automated drift email alerts (Enterprise)

## Getting started

1. **Step 1**: Open AzPolicy and build the design (definitions + assignments).
2. **Step 2**: If a tenant is connected, run "Compare deployed" to see the diff.
3. **Step 3**: (Enterprise) configure schedule + emails for automated drift alerts.

## Common use cases

- **Governance baseline**: design the CAF + custom set and track it against prod
- **Audit prep**: before an audit, validate there are no out-of-design policies
- **Multi-tenant**: with Enterprise, compare multiple tenants against the same design

## Tips

- Start with a small set (5-10 policies) — large initiatives are hard to operate
- For hard enforcement use `deny`; for initial visibility use `audit`
- AzPolicy Compare (Enterprise) is the tool that tells you "someone deployed something by hand"

## FAQ

**Does it replace the Azure Policy portal?**
No — the Hub helps you design, compare, and track. Apply happens via pipelines or portal.

**Does it detect policies created directly in the portal?**
Yes — that's exactly "rogue policy" in compare.

## Related

- [AzCAF — Cloud Adoption Framework](/AzCAF)
- [AzRBAC — RBAC Designer](/AzRBAC)
- [AzCompliance — Regulatory Mapper](/AzCompliance)
- [Azure Policy overview](https://learn.microsoft.com/azure/governance/policy/overview)
