---
title: AzCAF
description: Cloud Adoption Framework applied for real — Landing Zones without burning 6 months.
category: herramientas
slug: azcaf
order: 70
readMinutes: 6
tags: [Foundation, Free with signup]
lastUpdated: 2026-05-20
author: javier
sources:
  - https://learn.microsoft.com/azure/cloud-adoption-framework/
  - https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/
---

## A quick reflection

The pattern Microsoft saw repeated in every enterprise customer: they adopt Azure fast, 6 months later have 12 subscriptions with no governance, year 2 hire a consultancy to "clean house", year 3 still have tech debt from the initial sprint.

Is it worth investing 4-8 weeks upfront on a Landing Zone, or "just deploy and see"?

CAF assumes yes. This page helps you decide consciously.

## What it's for

AzCAF is the Cloud Adoption Framework operationalized — you design your Management Groups hierarchy, naming convention, networking baseline and governance policies **before** the first workload lands.

**Problems you probably have:**

- Subscriptions created ad-hoc by different teams
- No Management Groups, everything hangs off the tenant root
- Networking patched workload by workload, no central hub
- Every team invents their own naming convention
- "We'll do governance later"

**What you'll be able to do:**

- Define the right MG → Sub → RG hierarchy for your size
- Generate an exportable Landing Zone blueprint
- Map minimum components (networking, identity, governance, monitoring)
- Document the decision before deploying it

## How it works

Three central elements:

```bash
1. Management Groups hierarchy  →  Platform / Workloads / Sandbox
2. Subscriptions strategy        →  per LZ (Corp, Online, etc.) or per env
3. Baseline components           →  Hub VNet, central Log Analytics,
                                    naming + tagging policies,
                                    Defender for Cloud
```

AzCAF helps you decide the **size** of your Landing Zone:

| Tier | When | Setup time |
|---|---|---|
| **Tier 1** | Solo / <5 ppl, 1-2 subs | 2-3 days |
| **Tier 2** | Medium team (10-50), 1 company | 2-3 weeks |
| **Tier 3** | Enterprise 50+ ppl, multi-team | 2-4 months |
| **Tier 4** | MSP / consultancy multi-client | varies |

## Architectural decision

**When NOT to follow CAF strictly?**

| Situation | Recommendation |
|---|---|
| You're solo or team <5 | Tier 1 is enough. Formal CAF is overkill |
| Ephemeral / experimental workloads | Sandbox sub with TTL > proper LZ |
| Predominantly multi-cloud (Azure is 30%) | Own agnostic convention |
| Inherit existing governance that works | Don't break it because CAF is newer |
| Specific regulatory constraints | Follow the regulator's framework |

CAF is a **guideline**, not a **law**. What matters is that your decision is **documented and consciously made**, not defaulted by inertia.

[See AzCAF deep dive →](/learn/herramientas/azcaf-deep-dive)

## Field hack

**Start with the tier decision, not the diagram.**

The most common mistake: drawing the perfect Hub-Spoke VNet first without deciding if your company needs Tier 2 or Tier 3. You spend 2 weeks building something you have to throw away.

Sit down for 30 minutes. How many people touch Azure in your company today? In 12 months? Are you a multi-client consultancy or an internal team? Those 3 questions decide the tier. The diagram comes later.

## Take it to your AI

```markdown
# CAF Landing Zone — decision matrix

## Tier decision (in order)
1. Solo / team <5? -> Tier 1 (no formal LZ)
2. Medium team 10-50 ppl, 1 company? -> Tier 2
3. Enterprise 50+ ppl, multi-team? -> Tier 3
4. Multi-client consultancy? -> Tier 4 (MSP)

## Minimum components per tier
Tier 1: Naming + tags + 1 central KV
Tier 2: + Basic Hub VNet + Log Analytics + Policies
Tier 3: + Azure Firewall + PIM + Sentinel + multi-region DR
Tier 4: + Tenant per client + scheduled audits

## Anti-patterns
- "We'll do governance later"
- Permanent Owner role in production
- Sandbox without TTL
- Naming convention only in a README (not in Policy)
- Optional monitoring
```

## Official sources

- **[CAF — Cloud Adoption Framework](https://learn.microsoft.com/azure/cloud-adoption-framework/)** — the canonical source
- **[CAF — Landing Zones](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/)** — end-to-end concept
- **[AzCAF · Deep dive](/learn/herramientas/azcaf-deep-dive)** — the 18-min version with sprints, anti-patterns, use cases
