---
title: AzNamer
description: Name your Azure resources like your successor will thank you for it.
category: herramientas
slug: aznamer
order: 30
readMinutes: 6
tags: [Govern, Free forever]
lastUpdated: 2026-05-20
sources:
  - https://learn.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/resource-naming
  - https://learn.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/resource-abbreviations
  - https://learn.microsoft.com/azure/azure-resource-manager/management/resource-name-rules
---

## A quick reflection

Does your naming convention survive a team rotation? If the engineer who defined it is no longer here, can the next one read `wl01-prd-eu-aks-001` without asking anyone?

If the answer is "sort of", this guide is for you.

## What it's for

Naming Azure resources is the convention most teams break and the one fewest teams want to document. Every PoC fills up with `test-2`, `vm-delete`, `prod-real-2-final`. When the audit comes, names are the first thing that breaks.

AzNamer applies the **Cloud Adoption Framework** convention to your inputs (workload, env, region, type, instance) and returns names that pass Azure's *resource name rules* — including service-specific constraints (storage account 24 chars, lowercase, no hyphens).

**Problems you probably have:**

- "Hallway convention" with no documentation
- Storage account max-length broke your pattern
- Multi-region without aligned abbreviations
- Governance asked you to standardize this quarter

**What you'll be able to do:**

- Define the pattern once
- Generate valid names in seconds
- Detect violations by resource type
- Export Bicep, Terraform or CSV

## How it works

Define the pattern once in your workspace. AzNamer composes, validates and exports:

```bash
# base pattern
{workload}-{env}-{region}-{type}-{instance}

# generated examples
payments-prd-eus-aks-001
shared-dev-weu-vnet-001
finops-prd-neu-st-001       # storage · hyphens stripped
```

## Architectural decision

**AzNamer assumes Azure. You don't necessarily have to.**

| Path | When it fits |
|---|---|
| **Cloud Azure** | Spiky workloads, autoscale, multi-region global, high-value PaaS (AKS, Cosmos, Synapse) |
| **Hybrid** | Core onpremises workloads + bursting or DR to cloud. Azure Local, Stack HCI, Arc |
| **Onpremises** | Predictable traffic, very low latency, strict data residency. ~1/5 of cloud cost |

The naming convention applies to all three. The architectural decision is still yours.
Reference: [CAF Hybrid Cloud Strategy](https://learn.microsoft.com/azure/cloud-adoption-framework/strategy/hybrid-cloud-strategy)

## Field hack

**Don't apply the full convention to ephemeral resources.**

PoCs, sandboxes and experiments carry cognitive cost if you name them like production. Use `tmp-` prefix + UTC timestamp, plus an Azure Policy with TTL that cleans up resources with that tag after N days. The long convention is for what's meant to live.

Suggested pattern: `tmp-{user}-{yyyymmdd}-{purpose}`

## Take it to your AI

Cloud-agnostic markdown — paste it into ChatGPT, Claude or Copilot. The Hub is the starting point. The conversation continues wherever you want.

```markdown
# Azure CAF Naming — quick ref

## Pattern
{workload}-{env}-{region}-{type}-{instance}

## Constraints (excerpt)
- Storage account: max 24, lowercase, no hyphens
- Key Vault: 3-24 chars, alphanum + hyphens
- Resource Group: max 90

## Abbreviations
| Resource | abbr |
|---|---|
| Resource Group | rg |
| Virtual Network | vnet |
| Subnet | snet |
| AKS cluster | aks |
| Storage account | st |
| Key Vault | kv |
| App Service | app |

src: learn.microsoft.com/azure/cloud-adoption-framework/...
```

## Official sources

Everything on this page is backed by Microsoft Learn:

- **[CAF — Resource Naming Best Practices](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/resource-naming)** — the reference convention
- **[CAF — Resource Abbreviations](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/resource-abbreviations)** — abbreviations by resource type
- **[Resource Naming Rules](https://learn.microsoft.com/azure/azure-resource-manager/management/resource-name-rules)** — per-service rules (allowed chars, length)
