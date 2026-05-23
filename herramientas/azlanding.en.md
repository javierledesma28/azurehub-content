---
title: AzLanding — Azure Landing Zone Studio
description: Studio to design CAF-compliant Landing Zones with Bicep / Terraform output.
category: herramientas
slug: azlanding
lang: en
order: 75
readMinutes: 4
tags: [Foundation, Free with signup]
lastUpdated: 2026-05-23
sources:
  - https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/
  - https://learn.microsoft.com/azure/architecture/landing-zones/
---

## AzLanding

AzLanding is the studio for designing CAF Enterprise-Scale Landing Zones: management groups, subscriptions, policies, RBAC, and baseline networking. The output is Bicep or Terraform ready to deploy — not loose JSON you have to glue together later.

## What it's for

- Design the MG → Sub → RG hierarchy before workloads land
- Model the hub-and-spoke network baseline
- Assign policies and RBAC to the hierarchy
- Export the blueprint as Bicep or Terraform

## Getting started

1. **Step 1**: Open AzLanding and pick the tier (basic, intermediate, enterprise-scale).
2. **Step 2**: Configure MGs, subscriptions, hub VNet, policies, and RBAC.
3. **Step 3**: Validate pre-deploy and export Bicep/Terraform.

## Common use cases

- **Greenfield enterprise**: starting a fresh tenant and need the CAF foundation in weeks, not months
- **Migration to enterprise-scale**: your tenant grew ad-hoc and you want a blueprint to reorganize
- **Consulting**: replicate the same baseline across multiple clients with variants

## Tips

- Start with the tier decision (see AzCAF) before touching AzLanding
- Don't dump every policy from the CAF catalog — start with the minimum set and grow
- Validate the output with `bicep build` before pasting into pipelines

## FAQ

**Is it the same as the Enterprise-Scale portal accelerator?**
It covers the same ground, but here you edit visually and export the code.

**Does it auto-deploy?**
No — that's on your pipeline. We hand you the code ready to go.

## Related

- [AzCAF — Cloud Adoption Framework](/AzCAF)
- [AzNet — Subnet planner](/AzNet)
- [AzModules — Azure Verified Modules](/AzModules)
- [CAF Landing Zones](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/)
