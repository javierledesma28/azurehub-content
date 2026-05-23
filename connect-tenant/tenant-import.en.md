---
title: Tenant Import — auto-generate diagrams from Azure
description: Connect your tenant once, then auto-import any RG into AzDraw with the right boundaries and edges already in place.
category: connect-tenant
slug: tenant-import
order: 20
readMinutes: 5
tags: [Connect Tenant, Team, Enterprise]
lastUpdated: 2026-05-20
author: javier
sources:
  - https://learn.microsoft.com/azure/governance/resource-graph/overview
  - https://learn.microsoft.com/azure/role-based-access-control/built-in-roles
---

## What it does

Pick any Resource Group from your connected Azure tenant and Hub28 draws it for you on AzDraw — with nested boundaries (RG → VNet → Subnet), the right icons per resource type, and the network edges between them (VNet peerings, NIC↔subnet, NSG associations, Private Endpoints, App Service VNet integration).

You stay in control: the imported nodes land on a separate **Reality** layer, so you can still sketch new ideas on the **Design** layer and toggle the imported view on and off at will.

## How to use it

1. **Connect a tenant** to your workspace (Settings → Connect Tenant). One-time admin consent.
2. Open any diagram in AzDraw.
3. Click **Import** in the top bar.
4. Pick one or more Resource Groups from the tree (sub → RG).
5. Confirm. The canvas populates in ~2-10 seconds depending on the RG size.

Need to keep it in sync? Click **Refresh** any time — Hub28 re-queries the tenant and shows a diff (new / changed / removed) so you decide what to apply. The positions you tweaked stay put.

## Permissions required

The Service Principal of the Tenant Connector needs **Reader** at the subscription or management group level. Nothing else — no Contributor, no Owner.

- For Phase 1-3 (import + refresh + network edges + Private Endpoints): `Reader` is enough.
- For Phase 4 (Identity layer with role assignments): you'll add `Reader` + `Role Based Access Control Administrator`-style read scopes when that layer ships.

Connect Tenant uses standard Microsoft Entra **admin consent** for a multi-tenant App Registration. The consent screen lists exactly what we're asking for — review it carefully before clicking Accept.

## What gets imported

Phase 1 + 2 cover ~30 resource types that account for 80% of architectures we see:

- **Boundaries**: Resource Group, Virtual Network, Subnet
- **Networking**: NIC, Public IP, Load Balancer, Application Gateway, Firewall, NSG, Route Table, Private Endpoint, Private DNS, Bastion, VPN Gateway, Front Door, WAF
- **Compute**: VM, VMSS, Disk
- **Storage / data**: Storage Account, SQL Server, SQL DB, PostgreSQL Flex, MySQL Flex, Cosmos DB, Redis, Synapse
- **Web**: App Service Plan, App Service, Static Web App, Function App
- **Identity / Security**: Key Vault, Managed Identity, Defender, Sentinel
- **Containers**: AKS, ACR, Container Instance, Container App
- **Integration**: Service Bus, Event Hub, Event Grid, API Management, Logic App, Data Factory
- **Observability**: Log Analytics, Application Insights
- **AI**: Cognitive Services, ML workspace

Anything not in this list still renders — with a generic Azure icon and the full ARM type as label, so you don't lose information.

## On-demand layers (Identity + Data plane)

Beyond network edges (which come automatically with the import), two extra layers are available on-demand from the right sidebar:

### Identity layer (Phase 4)
- **What it shows**: Managed Identity → resource edges, labeled with the role name (Reader, Storage Blob Data Reader, Contributor, etc.).
- **Source**: cross-checks System+User assigned MIs from your imported resources against Azure role assignments at sub/RG/resource scope.
- **When to use it**: Zero-Trust review, detecting overly permissive scopes, audit of "who can read what".
- **Permission**: read access to `Microsoft.Authorization/roleAssignments` and `Microsoft.Authorization/roleDefinitions` — included in `Reader` at subscription scope.
- **UI**: click **Show identity** in the right sidebar. Click **Hide** to remove the edges from the canvas.

### Data plane layer (Phase 5)
- **What it shows**: App Service → SQL / Storage / Cosmos / Key Vault / Service Bus / Event Hub / Redis edges, inferred from app settings + connection strings.
- **Source**: pattern matching on `*.database.windows.net`, `*.blob.core.windows.net`, `@Microsoft.KeyVault(VaultName=...)`, etc.
- **When to use it**: discover hidden dependencies between an App Service and its backing services without reading deployment YAML.
- **Permission**: `Microsoft.Web/sites/config/list/action` on the App Service's RG — included in `Reader`.
- **Privacy**: the actual setting values (which may contain credentials) are read transiently and discarded. We never persist them. Only the resolved target ARM IDs become edges.
- **UI**: click **Show dependencies** in the right sidebar.

## How the layers work

| Layer | What goes on it | When created | Refreshable? |
|---|---|---|---|
| **Design** | Whatever you drew manually | Default, always present | No |
| **Reality** | Everything imported from the tenant | After the first import | Yes, via Refresh |

You can toggle visibility independently from the right panel. Mix freely: imagine drawing a future "to-be" architecture in Design on top of your current "as-is" Reality.

## Soft cap and big tenants

We warn you at **200 imported resources per diagram**. Beyond that the canvas keeps working, but layouts become hard to read. The right move for landing zones is to split the import: one diagram per hub-spoke pair, one per workload RG, etc. You can have as many diagrams as your plan allows.

## What we never read

- We never read secret values, connection string passwords, or Key Vault secret content.
- We never write to your tenant. Read-only is the contract.
- For the future data plane layer (Phase 5), opt-in per feature — and we still only resolve resource IDs, not secret values.

## Pricing

| Tier | Tenant Import |
|---|---|
| Free, PRO | Not included |
| **Team** | 1 connected tenant, unlimited imports, refresh, 30-day snapshot history |
| **Enterprise** | Multi-tenant, scheduled refresh, drift alerts, 90-day snapshot history |

## Troubleshooting

**"Tenant not connected" error** — your workspace doesn't have an active tenant binding. Settings → Connect Tenant.

**"Insufficient permissions" / empty resource list** — the Service Principal doesn't have Reader at the right scope. Have your admin assign Reader at the subscription or management group level.

**Import takes >30s** — the tenant is very large or Resource Graph is throttled. Try filtering to fewer Resource Groups.

**Some resources show generic icons** — those types aren't in the current top-30 mapper. The data is still imported correctly; we just don't have a custom icon yet. The label shows the full ARM type so you know what it is.
