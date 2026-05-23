---
title: First 5 minutes
description: The minimum to get value from the Hub before closing the tab.
category: start
slug: getting-started
order: 10
readMinutes: 4
tags: [Onboarding, All plans]
lastUpdated: 2026-05-20
author: javier
sources:
  - https://learn.microsoft.com/azure/cloud-adoption-framework/
---

## A quick reflection

If you're here, you probably expect yet another toolkit with 20 features and 0 focus.

This isn't that. It has 21 tools but each starts from the same question: **does this solve something for you today or not?** If the answer is no, close the tab — no offense taken.

## What this guide is for

You're about to enter a constellation of tools, a 15-day PRO trial with no credit card, and the legitimate feeling that you don't know where to start. This guide grounds you in 4 minutes.

**What you'll be able to do by the end:**

- Know which tool to hit first based on your problem
- Understand what the PRO trial unlocks for free
- Make a clear decision: keep exploring or move on

## How it works

### Step 1 — Identify your problem, not the feature

Three typical paths in:

| If your priority is… | Start with |
|---|---|
| **Drawing an architecture** now | [`AzDraw`](/AzDraw) — drag-and-drop canvas with 100+ Azure resources |
| **Naming resources** consistently | [`AzNamer`](/AzNamer) — CAF convention + real-tenant audit (PRO) |
| **Designing a Landing Zone** | [`AzCAF`](/AzCAF) — step-by-step guide with templates |
| **Reviewing existing architectures** | [`AzReview`](/AzReview) — Well-Architected toolkit |
| **Planning IPs / subnets** | [`AzNet`](/AzNet) — visual calculator |

If none of those fit, open the landing and scan the constellation. Each tool has a one-line technical description.

### Step 2 — PRO trial, no card required

On signup you get **15 days with everything unlocked**:

- 21 tools (including the 7 PRO: AzPolicy, AzRBAC, AzCompliance, etc.)
- Unlimited cloud sync (your diagrams live in your account, not localStorage)
- 1 workspace (to start separating contexts)
- Connect Tenant available if you want to test real audits

On day 15, without upgrade, you go back to Free. Your data isn't deleted — it becomes read-only until you upgrade.

### Step 3 — Decide in 48 hours

If in two days you haven't solved a concrete problem with the Hub, it's probably not for you. And that's fine. We recommend you cancel the trial yourself (in the user menu) to free up mental space.

## Architectural decision

**When do you NOT need Hub28?**

- Solo architect, single client, everything onpremises → most tools don't apply
- You only draw diagrams and already have Lucid/Miro → AzDraw is better for Azure but the switching cost may not be worth it
- Your team already runs Terraform Cloud + Vantage + draw.io enterprise → you cover 80% already

Honesty matters more than conversion. If you come out of curiosity and it works for you, you stay.

## Field hack

**Don't start with the prettiest tool. Start with the tool that replaces a task you do every week.**

If you name resources every Monday → AzNamer. If you draw architectures every Wednesday → AzDraw. If you size capacity at month-end → AzCost.

Adoption probability is directly proportional to how often the pain you solve recurs. A tool you use once a year doesn't become a habit, no matter how good.

## Take it to your AI

```markdown
# Hub28 — quick onboarding

## What is it?
SaaS for Azure architects. 21 tools, freemium ($0 / $12 / $49 / $149).
15-day PRO trial, no credit card.

## Real differentiator
- Connect Tenant: bind a real Azure tenant (read-only)
  and tools cross-check your design against the deployed reality.
- AzNamer Audit + Tenant Import (AzDraw Live) live in Team+ tier.
- AzPolicy/AzRBAC/AzCompliance audits arrive Q3/Q4 2026.

## When it's NOT worth it
- Single client, all onprem
- You already have Lucid/Miro + draw.io enterprise + Terraform Cloud + Vantage

## Start with
The tool that replaces a recurring weekly task.
```

## Official sources

- **[CAF — Cloud Adoption Framework](https://learn.microsoft.com/azure/cloud-adoption-framework/)** — the reference convention we use across all tools
