---
title: AzRBAC — RBAC Role Designer
description: Custom RBAC roles with least-privilege scoring and built-in role comparison.
category: herramientas
slug: azrbac
lang: en
order: 110
readMinutes: 4
tags: [Security, PRO]
lastUpdated: 2026-05-23
sources:
  - https://learn.microsoft.com/azure/role-based-access-control/overview
  - https://learn.microsoft.com/azure/role-based-access-control/best-practices
---

> 🔒 Requires PRO — least-privilege scoring against your tenant.

## AzRBAC

AzRBAC is the custom RBAC role designer. It shows the least-privilege score of each role you design, compares it against equivalent built-in roles, and detects over-privileged assignments in your tenant.

## What it's for

- Design custom RBAC roles with least-privilege scoring
- Compare your custom role against equivalent built-in roles
- Detect Owner / Contributor assignments where something smaller would do
- Export to ARM/Bicep ready to deploy

## Getting started

1. **Step 1**: Open AzRBAC and pick "New custom role" or start from a built-in.
2. **Step 2**: Define actions, notActions, dataActions, and scope.
3. **Step 3**: Review the least-privilege score and export ARM/Bicep.

## Common use cases

- **FinOps operator**: needs to see costs but not touch resources — built-in doesn't fit, custom does
- **Audit cleanup**: detect who got Owner when Reader + Cost Management would have done
- **Pre-prod cleanup**: validate nobody has Contributor in production

## Tips

- Before building custom, see if a built-in covers it — less operational overhead
- notActions is your friend for "Contributor minus X"
- The wider the scope, the higher the risk — prefer RG scope over subscription

## FAQ

**Does it auto-detect Owner in production?**
Yes — over-privileged assignments show up in the tenant scan.

**Does it support PIM?**
We don't integrate with PIM directly today, but the output is compatible.

## Related

- [AzPolicy — Policy Builder](/AzPolicy)
- [AzZeroTrust — Zero Trust Assessment](/AzZeroTrust)
- [Azure RBAC overview](https://learn.microsoft.com/azure/role-based-access-control/overview)
- [RBAC best practices](https://learn.microsoft.com/azure/role-based-access-control/best-practices)
