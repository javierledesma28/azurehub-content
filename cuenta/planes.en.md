---
title: Plans
description: Free, PRO, Team, Enterprise — what you pay, what each unlocks, no marketing.
category: cuenta
slug: planes
order: 10
readMinutes: 4
tags: [Pricing, Account]
lastUpdated: 2026-05-21
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

| Plan       | Price                | Tools                       | Cloud sync | Workspaces  | Connect Tenant | Ideal for                             |
|------------|----------------------|-----------------------------|------------|-------------|----------------|---------------------------------------|
| Free       | $0                   | 14 (of 21)                  | 1 diagram  | 0           | ❌             | Trying the Hub without signing up     |
| PRO        | $12/mo               | 21                          | Unlimited  | Personal    | ❌             | Full-time individual architect        |
| **Team**   | **$29/seat (min 2)** | **21 + audits**             | Unlimited  | Shared      | ✅ 1 tenant    | Small consultancy · internal team     |
| Enterprise | $149/mo + seats      | 21 + audits + AzPolicy Compare + scheduled | Unlimited | Unlimited   | ✅ Unlimited   | Large consultancies · MSPs            |

Yearly: ~30% discount (e.g., PRO $99 vs $144).

> **Model change (2026-05-21):** the Consulting tier ($49) was absorbed into Team per-seat. If you were on Consulting, you're now on Team with all the audits you already had.

### What each jump unlocks

**Free → PRO ($12/mo)** — when you're already using the Hub and want:
- The 7 PRO tools (AzPolicy, AzRBAC, AzCompliance, AzZeroTrust, AzResiliency, AzFinOps, AzMigrate, AzAI)
- Unlimited diagrams with cloud sync
- Export Markdown, Mermaid, Bicep, Terraform
- No watermark · personal workspace + 30-day version history

**PRO → Team ($29/seat, min 2)** — when you work with clients or in a team and want:
- Shared workspaces + team templates + SSO (SAML/Entra) + custom branding
- **Connect Tenant**: bind 1 real Azure tenant (read-only) to the workspace
- **Live AzNamer Audit**: cross-check your CAF convention against the client's real resources
- **Tenant Import (AzDraw Live)**: import real topology and compare against the diagram
- 30-day audit history

**Team → Enterprise ($149/mo + seats)** — when you handle multiple clients and want:
- Unlimited connected Azure tenants (not just 1)
- **AzPolicy Compare**: designed policies vs deployed assignments
- **Scheduled audits**: run on their own daily or weekly
- **Email/Slack alerts** on drift
- 90-day history retention
- API + IaC export (REST + Terraform/Bicep)
- SLA 99.9% · priority support

### PRO Trial included on signup

Any new user starts with 15 days of **PRO trial, no card**. You get the full PRO feature set. It's not Team/Enterprise (Connect Tenant is visible but cannot be activated).

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

**If you're torn between PRO and Team → start with PRO monthly.**

If after a month you find yourself needing to audit real tenants, that's when you jump to Team (min 2 seats = $58). A single proposal won with the audit already pays it 30 times over.

No penalty for moving between plans — automatic proration.

## Take it to your AI

```markdown
# Azure Hub plans — decision matrix (post 2026-05-21)

## Decision tree
Do you use the Hub >2x per week?
  NO → Free
  YES → Do you need to audit real Azure tenants?
    NO → PRO ($12/mo)
    YES → How many tenants/clients simultaneously?
      1 + small team → Team ($29/seat, min 2 = $58)
      Multiple + need AzPolicy Compare → Enterprise ($149 + seats)

## Trial
Free 15 days, no card, all PRO unlocked.

## Payments
Paddle (merchant of record) — Q3 2026
Until then: launching soon notice.
```

## Official sources

- **[/pricing](/pricing)** — the full pricing page with UI and yearly/monthly toggle
- **[PRO Trial](/learn/conceptos/trial-pro)** — full trial mechanics
- **[Connect Tenant Security](/learn/connect-tenant/security-faq)** — exact permissions if going to Team/Enterprise
