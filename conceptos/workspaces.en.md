---
title: Workspaces
description: The top-level container that separates clients, projects and experiments without mixing governance.
category: conceptos
slug: workspaces
order: 10
readMinutes: 5
tags: [Foundation, All plans]
lastUpdated: 2026-05-20
sources:
  - https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/
  - https://learn.microsoft.com/azure/governance/management-groups/overview
---

## A quick reflection

Are you designing for one client or three? Do your "ProjectX" and "ProjectY" diagrams share storage accounts in your head but really belong to different tenants?

If you work multi-client or multi-project, mixing context is the first sin.

## What it's for

A **Workspace** in Azure Hub is the top-level container of your work. Each workspace has its own set of diagrams (AzDraw), reviews (AzReview), subnet plans (AzNet) and — once you connect it — its binding to a real Azure tenant.

**Problems you probably have:**

- Client A and Client B mixed in the same view
- You don't know which tenant the diagram you're looking at belongs to
- One project's naming convention leaks into another
- Sharing content with the client's team without exposing others

**What you'll be able to do:**

- Isolate content per client / project / initiative
- Bind each workspace to an Azure tenant (PRO Consulting+)
- Keep naming conventions independent
- Switch context with a dropdown in the TopBar

## How it works

Every resource (diagram, review, subnet plan) belongs to exactly **one** workspace. The switcher in the TopBar is the active context:

```bash
# mental model
Workspace "Default"
  ├── Diagrams
  ├── Reviews
  ├── Subnet plans
  └── (optional) Tenant binding

Workspace "Acme Client"
  ├── Diagrams
  ├── Reviews
  ├── Subnet plans
  └── Tenant binding: acme.onmicrosoft.com
```

## Architectural decision

**A workspace is to Azure Hub what a Management Group is to Azure.**

| Path | When it fits |
|---|---|
| **Single workspace** | Freelance working one client / one internal initiative |
| **Workspace per client** | Consultancies, MSPs, multi-account architects |
| **Workspace per environment** | Internal teams with very separated prd/dev/staging |
| **Workspace per initiative** | Large programs with many sibling projects |

There's no "technical" optimal limit. The right question is: **what should share naming convention and tenant binding?** That stays together. The rest separates.

Reference: [CAF Landing Zones](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/) — the separation logic is the same at a different scale.

## Field hack

**Name the workspace the way you'd search for the invoice.**

If the client pays you as "Acme Inc.", the workspace is "Acme". If the internal initiative is called "FinOps 2026", the workspace is "FinOps 2026". Sounds obvious — until you have 12 workspaces called "Client1", "Client2", "New-project".

Trial: 1 workspace. PRO: unlimited. I recommend not creating more than you'll use the same week.

## Take it to your AI

```markdown
# Workspace strategy — quick ref

## When to split
- Different billing entity (client)
- Different Azure tenant
- Different naming convention
- Different governance baseline

## When NOT to split
- Different environment of same app (use tags)
- Different team of same client (use roles)
- Temporary experiment (use tmp- prefix in same workspace)

## Rule of thumb
"What should share the same naming convention
 and tenant binding?" → same workspace.
```

## Official sources

- **[CAF — Landing Zones](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/)** — logical separation at Azure scale
- **[Management Groups overview](https://learn.microsoft.com/azure/governance/management-groups/overview)** — the Azure equivalent of "top-level container"
