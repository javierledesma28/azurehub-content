---
title: AzDraw
description: A canvas for Azure architecture diagrams, without draw.io's friction.
category: herramientas
slug: azdraw
order: 10
readMinutes: 5
tags: [Design, Free forever]
lastUpdated: 2026-05-20
author: javier
sources:
  - https://learn.microsoft.com/azure/architecture/icons/
  - https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/
---

## A quick reflection

How many architecture diagrams in your team survive past the sprint they were drawn in?

If the average is "less than half", the problem isn't the tool — it's friction. A diagram that takes 2 hours to make and 3 minutes to become stale doesn't get maintained. The right question isn't "what diagramming tool do we use" but "how much friction is it to update an existing diagram".

## What it's for

AzDraw is a drag-and-drop canvas with 100+ pre-modeled Azure resources, visual boundaries (Subscription / Resource Group / VNet) that auto-parent their children, and cloud sync so the diagram doesn't die in your downloads folder.

**Problems you probably have:**

- draw.io has Azure icons but not the semantics (it doesn't know what a boundary is)
- Lucid/Miro don't have decent Hub-and-Spoke templates
- Your team draws on a whiteboard and nobody digitizes it
- Each new PowerPoint starts from scratch because finding the last deck is worse

**What you'll be able to do:**

- Drag resources to the canvas with official Microsoft icons
- Create boundaries that automatically contain their children
- Sync the diagram to your account (1 free, unlimited on PRO/trial)
- Switch between T28 (generic) and AZ (Azure official) palettes without losing content

## How it works

### Visual boundaries

The three boundary types that already understand the Azure model:

```bash
Subscription   →  contains Resource Groups, Policy Assignments, MGs
Resource Group →  contains most resources (VMs, AKS, Storage…)
VNet           →  contains Subnets, NSGs, Route Tables
```

Drag a boundary first, then drag resources inside. AzDraw auto-parents them. If you move the boundary, children move with it.

### Multi-diagram + workspaces

Every diagram lives in a workspace. Switcher in the top bar to change workspace; dropdown next to the title to change diagram within the workspace.

- **Free**: 1 diagram
- **PRO / Trial**: unlimited
- **Team**: unlimited + tenant binding for AzNamer Audit + Tenant Import

### Smart auto-save

Edit, wait 2 seconds — already saved to the cloud. No "Save" button. Signature-based change detection avoids useless pings when nothing actually changed.

## Architectural decision

**AzDraw assumes you want to diagram Azure. If not, there are better options:**

| Path | When it fits |
|---|---|
| **AzDraw** | You diagram Azure / hybrid. You want boundaries that behave. Sync without thinking |
| **draw.io** | Multi-cloud agnostic, absolute style control, already adopted at your company |
| **Lucid / Miro** | Live collaborative workshops, brainstorming, non-Azure |
| **Pen + paper** | 80% of initial diagrams need nothing more. Honesty. |

We don't try to beat the field on raw flexibility. We try to win on "Azure-specific + low maintenance cost".

## Field hack

**Start with the boundary, not the resource.**

If you draw the VMs first and then try to enclose them in a Resource Group, auto-parent doesn't apply retroactively. The flow that works:

1. Subscription boundary → 2. Resource Groups inside → 3. VNet boundary (if applicable) → 4. Child resources

It costs you 30 extra seconds upfront and saves rearranging 30 nodes later.

## Take it to your AI

```markdown
# AzDraw — quick reference

## Recommended flow
1. Create workspace (optional, "Default" works)
2. Create new diagram (auto-save enabled)
3. Start with boundaries (Sub → RG → VNet)
4. Drag resources inside
5. Connect with edges (drag from the handle)
6. Switch T28 ↔ AZ palette if needed

## Key shortcuts
- Delete / Backspace → remove selection
- Ctrl+Z / Ctrl+Y → undo / redo
- Ctrl+A → select all
- Right-click → context menu

## What it does NOT do (yet)
- Generate Bicep / Terraform from the diagram
- Validate design against WAF (that's AzArch)
- Import from draw.io (.drawio) or Visio
```

## Official sources

- **[Azure Architecture Icons](https://learn.microsoft.com/azure/architecture/icons/)** — the official icons we use
- **[CAF — Landing Zones](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/)** — boundary semantics align with CAF
