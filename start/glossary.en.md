---
title: Glossary
description: CAF, WAF and Azure terms that appear across the Hub — without abstractions.
category: start
slug: glossary
order: 20
readMinutes: 5
tags: [Reference, All plans]
lastUpdated: 2026-05-20
author: javier
sources:
  - https://learn.microsoft.com/azure/cloud-adoption-framework/
  - https://learn.microsoft.com/azure/well-architected/
---

## A quick reflection

Half of the communication problems in an Azure team come from terms we use as synonyms when they're not.

"Subscription", "tenant", "directory" are not the same thing. Neither are "workload" and "application". This glossary is to go back to the right usage, not to invent new definitions.

## What it's for

A compact reference for the terms we use most in the Hub. Each entry links to Microsoft Learn if you want to go deeper — this is a starting point, not an encyclopedia.

## How it works

### Identity and tenancy

- **Tenant (Microsoft Entra)** — the "organization" in the Microsoft world. An email `@company.onmicrosoft.com` lives in a tenant. A typical company has 1 production tenant + 1-2 sandbox.
- **Directory** — informal synonym for tenant. It's Entra ID (formerly Azure AD).
- **Subscription** — the billing container inside a tenant. A company can have N subscriptions, all under the same tenant.
- **Management Group** — groups subscriptions to apply policies/RBAC in bulk. Forms a hierarchy up to 6 levels deep.
- **Service Principal (SP)** — application identity (not human). When you grant consent to Azure Hub Tenant Connector, an SP is created in your tenant.

### Resources

- **Resource Group (RG)** — bucket to group resources with the same lifecycle. Deleting the RG deletes everything inside.
- **Resource** — what Azure bills: VM, Storage Account, AKS cluster, etc.
- **Location / Region** — where the resource physically lives (eus, weu, brs…).
- **ARM ID** — the resource's full path (`/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Network/virtualNetworks/<name>`). It's Azure's universal primary key.

### Frameworks

- **CAF (Cloud Adoption Framework)** — Microsoft guide on **how to adopt Azure**: strategy, plan, ready, adopt, govern, manage, secure. Naming conventions, landing zones and abbreviations come from here.
- **WAF (Well-Architected Framework)** — Microsoft guide on **how to design well**: reliability, security, cost, operations, performance. AzReview and AzArch use this model.
- **Landing Zone** — pre-configured Azure environment following CAF (networking, identity, governance already set up). Landing point for new workloads.

### Networking

- **VNet (Virtual Network)** — the "private LAN" in Azure. Has its own CIDR.
- **Subnet** — segment inside a VNet with its own CIDR.
- **Hub-and-Spoke** — topology where a central VNet (hub) connects to satellite VNets (spokes). Canonical enterprise pattern.
- **Peering** — direct connection between two VNets. Allows communication without VPN/firewall.
- **Private Endpoint (PE)** — private IP inside your VNet pointing to a PaaS service (Storage, KV, SQL). Avoids public exposure.

### Governance

- **Policy** — declarative rule that evaluates or restricts resources (e.g., "don't allow public Storage Account").
- **Initiative** — collection of policies applied in bulk (e.g., ISO 27001 baseline).
- **RBAC (Role-Based Access Control)** — permission model by role. Reader / Contributor / Owner are most common.
- **Tag** — key-value pair to classify resources. Essential for cost allocation, lifecycle, contact info.

### Azure Hub specific

- **Workspace** — top-level container for your work (multi-client, multi-project). [See Workspaces](/learn/conceptos/workspaces).
- **Tenant binding** — relationship between a workspace and a connected Azure tenant. Enables audits.
- **PRO Trial** — 15 days with everything unlocked, no card. [See Trial PRO](/learn/conceptos/trial-pro).

## Architectural decision

**A definition is not a team consensus.**

Having the glossary clear doesn't guarantee your team uses it well. What does help: when confusion shows up, drop the relevant Microsoft Learn link in the thread and move the discussion to the right term. Reduces the cost of clarifying.

## Field hack

**Diagnostic question when someone says "subscription":**

"Do you mean the subscription that pays, or the tenant where users live?"

30% of the time the person was mixing the two. Resolving that early saves you 20 minutes of Zoom later.

## Take it to your AI

```markdown
# Azure tenancy hierarchy — quick ref

Tenant (Entra ID, "the organization")
  └── Management Group (up to 6 levels deep)
        └── Subscription (billing unit)
              └── Resource Group (shared lifecycle)
                    └── Resource (VM, Storage, AKS, etc.)

Each level inherits policies and RBAC to the lower one.
```

## Official sources

- **[CAF — Cloud Adoption Framework](https://learn.microsoft.com/azure/cloud-adoption-framework/)** — the "how to adopt Azure" guide
- **[WAF — Well-Architected Framework](https://learn.microsoft.com/azure/well-architected/)** — the "how to design well" guide
- **[Azure resource hierarchy](https://learn.microsoft.com/azure/azure-resource-manager/management/overview)** — tenants, MGs, subs, RGs
