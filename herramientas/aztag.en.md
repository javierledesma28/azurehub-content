---
title: AzTag — Tagging Strategy Builder
description: Visual tag builder that also generates the Azure Policy to enforce compliance.
category: herramientas
slug: aztag
lang: en
order: 60
readMinutes: 4
tags: [Govern, Free forever]
lastUpdated: 2026-05-23
sources:
  - https://learn.microsoft.com/azure/azure-resource-manager/management/tag-resources
  - https://learn.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/resource-tagging
---

## AzTag

AzTag is the visual builder for your tagging strategy. Define mandatory vs optional tags, allowed values, and AzTag generates the Azure Policy that enforces compliance. Export to Bicep, Terraform, or CSV — the convention stops living only in a README.

## What it's for

- Design a coherent tagging strategy aligned with CAF
- Distinguish mandatory tags (CostCenter, Owner, Env) from optional (Project, Phase)
- Auto-generate the "require tag" Azure Policy
- Export the convention ready to deploy

## Getting started

1. **Step 1**: Open AzTag and pick the CAF preset or start from scratch.
2. **Step 2**: Define each tag: name, mandatory/optional, allowed values, scope (subscription/RG).
3. **Step 3**: Export Bicep/Terraform/CSV or copy the policy straight into Cloud Shell.

## Common use cases

- **Greenfield**: starting a fresh tenant and want tagging from day 1
- **Audit cleanup**: you spot resources without owner or cost center and need to close the gap
- **FinOps**: you need consistent tags so Cost Management groups properly

## Tips

- Limit mandatory to 4-5 tags max — more and it falls apart in practice
- "Inheritance from RG" is your friend: define at RG and children inherit
- Combine with AzPolicy for real enforcement (deny when tag missing)

## FAQ

**Are the generated policies deny or audit?**
Audit by default. You can flip to deny in the export if you want hard enforcement.

**Does it support tags with dynamic values?**
Yes, via Azure Policy `modify` effect.

## Related

- [AzNamer — Naming conventions](/AzNamer)
- [AzPolicy — Policy Builder](/AzPolicy)
- [CAF Resource Tagging](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/resource-tagging)
