---
title: AzNet — Subnet & IP Address Planner
description: IPv4/IPv6 subnetting without Excel — overlap detection plus AzDraw integration.
category: herramientas
slug: aznet
lang: en
order: 85
readMinutes: 4
tags: [Network, Free with signup]
lastUpdated: 2026-05-23
sources:
  - https://learn.microsoft.com/azure/virtual-network/virtual-networks-overview
  - https://datatracker.ietf.org/doc/html/rfc4632
---

## AzNet

AzNet is the visual subnet planner. Define VNets, slice them into subnets, compute overlap between ranges, all with IPv4 and IPv6 support. Export to Bicep and to the AzDraw canvas — the plan stops living in an Excel sheet.

## What it's for

- Design the address space for a workload or hub
- Detect overlaps between VNets/subnets before deploying
- Recommend sizes based on expected resources (AKS, App Gateway, Bastion, etc.)
- Export Bicep + AzDraw diagram

## Getting started

1. **Step 1**: Open AzNet and define the base VNet (e.g. `10.50.0.0/16`).
2. **Step 2**: Slice into subnets — AzNet recommends sizes per type (AKS, AppGW, Bastion, etc.).
3. **Step 3**: Validate overlaps against existing peerings and export Bicep or to the canvas.

## Common use cases

- **New landing zone**: plan the address space before creating the first VNet
- **Expansion**: add a new spoke and validate it doesn't collide with existing ones
- **IPv6 dual-stack**: start a workload with native IPv6 and validate the design

## Tips

- Reserve ranges for services that grow: AKS needs more space than it seems
- Managed-service subnets have minimum sizes (Bastion /26, AppGW /24) — AzNet respects them
- Document the plan before deploying — refactoring subnets is painful

## FAQ

**Does it support IPv6?**
Yes — IPv4 and IPv6, including dual-stack.

**Does it detect overlap with peerings?**
Yes, if a tenant is connected or if you declare the hub ranges manually.

## Related

- [AzTopo — Network topology](/AzTopo)
- [AzLanding — Landing Zone Studio](/AzLanding)
- [Azure Virtual Network overview](https://learn.microsoft.com/azure/virtual-network/virtual-networks-overview)
