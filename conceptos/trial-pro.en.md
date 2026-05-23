---
title: PRO Trial · 15 days
description: What the trial unlocks, what happens on day 16, and how to cancel if it's not for you.
category: conceptos
slug: trial-pro
order: 20
readMinutes: 3
tags: [Trial, All plans]
lastUpdated: 2026-05-20
author: javier
---

## A quick reflection

Most SaaS trials are a fight for your card. This one isn't.

We don't ask for a card to start. If the Hub doesn't work for you, nothing happens on day 16 — you just go back to Free. No surprises. No "you got caught in the auto-billing without meaning to".

## What it's for

Explains exactly what you get with the trial, what happens when it expires, and how to cancel voluntarily if you decided it's not for you.

## How it works

### On signup

Your account starts in `status='trialing'`, `plan='pro'`, with `trial_end = NOW() + 15 days`. **15 days, not 14, not 30. Fifteen calendar, not business days.**

### What you have unlocked during those 15 days

- **All 21 tools** — the 7 PRO ones (AzPolicy, AzRBAC, AzCompliance, AzZeroTrust, AzResiliency, AzFinOps, AzMigrate, AzAI) are open
- **Unlimited cloud sync** — diagrams, reviews, subnet plans, naming configs persist on your account
- **1 workspace** to start separating contexts
- **Connect Tenant available** if you want to test real audits (you need an Azure tenant of yours or a client's with consent)

### On day 15

At 23:59 UTC on day 15, a cron runs (via pg-boss schedule) that:

1. Automatically downgrades you to `plan='free'`, `status='active'`
2. Sends an email with a CTA to `/pricing?offer=trial-extension` offering "1 month free if you subscribe for 3 months"
3. Your diagrams and configs **are not deleted** — they become read-only in PRO tools. They can be reopened any time on upgrade.

### To cancel before the 15 days are up

Today you can simply let it expire on its own — it costs you nothing and doesn't require a card. When Paddle goes live (Q3 2026), the user menu will have a one-click "Cancel trial".

### Warning banner

When ≤3 days remain, a TrialBanner appears in the top bar with a countdown. It's not an ad — it's information. You can ignore it.

## Architectural decision

**Why 15 days and not 7 or 30?**

| Duration | Trade-off |
|---|---|
| **7 days** | Short trial, fast decision, more pressure. But not enough to integrate with a team |
| **15 days** ✅ | Enough to test the Hub on 1-2 real sprints. Informed decision without it dragging forever |
| **30 days** | Too long, lots of funnel noise, eternal "I'll decide tomorrow" |

15 days aligns with a typical sprint. If in one sprint you haven't found value, it's probably not for you.

## Field hack

**Use the trial to test Connect Tenant.**

It's the most differentiated feature and the one that creates the most fear. But **during the trial you can connect the azurehub28 tenant (your own sandbox) with no commitment**. Run an audit, export CSV, show it to your internal sponsor. That's the conversation that sells — not the screenshots.

## Take it to your AI

```markdown
# Hub28 PRO Trial — quick facts

Duration: 15 calendar days
Card: NOT required
Activation: automatic on signup
Cancellation: automatic on day 16 (downgrade to Free)
Data: persists in read-only, recoverable on upgrade

Includes:
- 21 tools (all PRO unlocked)
- Unlimited cloud sync
- 1 workspace
- Connect Tenant (AzNamer Audit Live)

Post-trial offer: "1 month free with quarterly plan"
(via automated email on expiry)
```

## Official sources

This is an internal Hub28 mechanism, not Azure / Microsoft. If you want to see the code, it's in `api/src/jobs/trialCheck.ts` (pg-boss schedule running cron `17 * * * *`).
