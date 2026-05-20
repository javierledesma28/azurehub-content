---
title: Plans
description: Free, PRO, Team, Consulting, Enterprise — what you pay, what each unlocks, no marketing.
category: cuenta
slug: planes
order: 10
readMinutes: 4
tags: [Pricing, Account]
lastUpdated: 2026-05-20
author: javier
sources:
  - /pricing
---

## A quick reflection

The pricing page is made to sell. This page is made for you to **decide with info, not emotion**.

If after reading it you're still curious to try PRO, do the trial. If not, no harm done.

## What it's for

Technically documenting what each plan unlocks, what limits it has, and when each one fits. No blinking "MOST POPULAR".

## How it works

### Tabular summary

| Plan       | Price    | Tools          | Cloud sync | Workspaces | Connect Tenant | Ideal for                             |
|------------|----------|----------------|------------|------------|----------------|---------------------------------------|
| Free       | $0       | 13 (of 21)     | 1 diagram  | 0          | ❌             | Trying the Hub without signing up     |
| PRO        | $12/mo   | 21             | Unlimited  | Unlimited  | ❌             | Full-time individual architect        |
| Team       | $29/seat | 21             | Unlimited  | Shared     | ❌             | Internal teams with SSO               |
| Consulting | $49/mo   | 21 + audit     | Unlimited  | Unlimited  | ✅ 1 tenant    | Multi-client freelancers              |
| Enterprise     | $149/mo  | 21 + audit + scheduled | Unlimited | Unlimited | ✅ Unlimited | Consultancies / MSPs                |

Yearly: ~30% discount (e.g., PRO $99 vs $144).

### What each jump unlocks

**Free → PRO ($12/mo)** — when you're already using the Hub and want:
- The 7 PRO tools (AzPolicy, AzRBAC, AzCompliance, AzZeroTrust, AzResiliency, AzFinOps, AzMigrate, AzAI)
- Unlimited diagrams with cloud sync
- Unlimited workspaces to separate contexts

**PRO → Consulting ($49/mo)** — when you work with clients and want:
- **Connect Tenant**: bind a real Azure tenant (read-only) to the workspace
- **Live AzNamer Audit**: cross-check your CAF convention against the client's real resources
- **AzPolicy Compare** (Q3 2026): designed policies vs deployed assignments
- **Audit history**: every audit you run is saved in the workspace

**Consulting → Enterprise ($149/mo)** — when you handle multiple clients and want:
- Unlimited Azure tenants (not just 1)
- **Scheduled audits**: run on their own daily or weekly
- **Delta detection**: tells you what changed between audits
- 90-day history retention
- Dedicated Slack for support

### PRO Trial included on signup

Any new user starts with 15 days of **PRO trial, no card**. You get the full PRO feature set. It's not Consulting/Enterprise (Connect Tenant is visible but no scheduled audits).

If you want to test Connect Tenant during the trial → you can connect your Azure sandbox. It's the best way to evaluate if Consulting is for you.

[See trial details →](/learn/conceptos/trial-pro)

## Architectural decision

**When do you NOT upgrade?**

| Situation | Recommendation |
|---|---|
| You use the Hub <1x per week | Stay Free, you lose nothing |
| You're on a single 3-month project | PRO monthly during the project, downgrade after |
| Interested in Connect Tenant but client doesn't agree | Stay on PRO, raise the feature in the next proposal |
| Your company already has Vantage + Lucid enterprise | Free + occasional PRO for specifics they don't cover |

**The right plan is NOT the most expensive you can afford.** It's the cheapest that covers what you'll use **this week**.

## Field hack

**If you're torn between PRO and Consulting → start with PRO monthly.**

PRO monthly + Connect Tenant in the trial → gives you a week to evaluate Consulting **while you're already paying PRO**. If the audit pays off, upgrade to Consulting next month. If not, stay on PRO with no $49 commitment.

No penalty for moving between plans — automatic proration.

## Take it to your AI

```markdown
# Azure Hub plans — decision matrix

## Decision tree
Do you use the Hub >2x per week?
  NO → Free
  YES → Do you work with external clients?
    NO → Are you solo or in a team?
      Solo → PRO ($12)
      Team → Team ($29/seat, Q3 2026)
    YES → How many simultaneous clients?
      1-2 → Consulting ($49)
      3+ with regular audits → Enterprise ($149)

## Trial
Free 15 days, no card, all PRO unlocked.
Connect Tenant available for sandbox tests.

## Payments
Paddle (merchant of record) — Q3 2026
Until then: launching soon notice.
```

## Official sources

- **[/pricing](/pricing)** — the full pricing page with UI and yearly/monthly toggle
- **[PRO Trial](/learn/conceptos/trial-pro)** — full trial mechanics
- **[Connect Tenant Security](/learn/connect-tenant/security-faq)** — exact permissions if going to Consulting/Enterprise
