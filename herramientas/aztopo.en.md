---
title: AzTopo — Visualize your Azure network end-to-end
description: Visual topology of VNets, subnets, peerings, NSGs and ExpressRoute with live tenant import.
category: herramientas
slug: aztopo
lang: en
order: 80
readMinutes: 4
tags: [Network, Soon]
lastUpdated: 2026-05-23
sources:
  - https://learn.microsoft.com/azure/virtual-network/network-overview
---

> ⚠️ Coming soon — not yet available. We're finishing the tenant-import integration.

## AzTopo

AzTopo is the end-to-end Azure network visualizer: VNets, subnets, peerings, NSGs and ExpressRoute on a single canvas. It feeds from your connected tenant and highlights overlaps or misconfigurations before they hit production.

## What it's for

- See the real tenant topology without hand-drawing diagrams
- Detect CIDR overlaps between VNets sharing a peering
- Visualize NSG flow between subnets to understand why something stopped talking
- Identify orphan VNets or one-sided peerings

## Getting started

1. **Step 1**: Connect the tenant from the Tenant Import menu (Reader permissions on subscriptions).
2. **Step 2**: Open AzTopo from the Hub. Pick a scope (MG, subscription, or resource group).
3. **Step 3**: AzTopo loads the topology and shows VNets as nodes, peerings as edges, applied NSGs as badges.

## Common use cases

- **Quick audit**: review a new client's network in 5 minutes without asking for an outdated diagram
- **Overlap detection**: validate the new /16 you're about to deploy doesn't collide with an existing peering
- **Living documentation**: exportable snapshot of how the network looks today

## Tips

- For hub-and-spoke setups, group the view by hub to grasp the hierarchy at a glance
- One-sided peerings (one peer linked but not the other) are usually provisioning bugs — AzTopo flags them with a warning

## FAQ

**Why does it say "Coming soon"?**
The feature is in internal beta. Tenant import already works in AzDraw; we're polishing the network-only view to stay snappy on large tenants.

**Will it support ExpressRoute and VPN Gateway?**
Yes, both at first public release.

## Related

- [AzNet — Subnet & IP Address Planner](/AzNet)
- [AzDraw — Architecture canvas](/AzDraw)
- [Azure Virtual Network overview](https://learn.microsoft.com/azure/virtual-network/network-overview)
