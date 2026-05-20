---
title: AzReview
description: A Well-Architected toolkit without the 2-hour meetings nobody wants.
category: herramientas
slug: azreview
order: 20
readMinutes: 5
tags: [Validate, Free with signup]
lastUpdated: 2026-05-20
author: javier
sources:
  - https://learn.microsoft.com/azure/well-architected/
  - https://learn.microsoft.com/assessments/azure-architecture-review/
---

## A quick reflection

How many Well-Architected Reviews you started ended up as a PDF nobody reads?

The problem isn't WAF — it's the format. 2-hour workshops, infinite Excel templates, scoring that feels subjective. AzReview is the attempt to keep WAF's rigor while losing the ceremony.

## What it's for

A structured questionnaire across the 5 Well-Architected pillars, with automatic scoring, prioritized gap identification, and Markdown export to integrate into your project's ADR.

**Problems you probably have:**

- You do WAF reviews once, then they hang
- The Microsoft Assessments Excel is good but heavy to integrate into the workflow
- Your team doesn't remember criteria between reviews
- You lack a baseline to compare across projects

**What you'll be able to do:**

- Run a review in ~20 minutes instead of 2 hours
- Persist the result to the workspace (cloud sync)
- See score per pillar + prioritized gaps
- Export Markdown for your repo / Notion / Confluence

## How it works

### The 5 WAF pillars

```bash
Reliability        →  Does your workload keep running if X fails?
Security           →  Who accesses what, how it's protected, what you audit?
Cost Optimization  →  Paying for what you use, or what you forgot to turn off?
Operational Excel. →  Can you diagnose, deploy, and roll back?
Performance        →  Does it scale under load? How many regions do you really need?
```

AzReview shows between 8 and 15 questions per pillar, with structured answers (not open-ended) that map to a 0-100 score.

### Output

- **Score per pillar** (0-100)
- **Global weighted score**
- **Top 5 priority gaps** — questions where you scored lowest
- **Recommendations** linked to Microsoft Learn

### Cloud sync

- **Free**: 1 active review per workspace
- **PRO / Trial**: unlimited + history
- Auto-save while you answer (signature-based, no useless pings)

## Architectural decision

**AzReview doesn't replace Microsoft's official WAF Assessment.**

| When AzReview fits | When the Microsoft Assessment fits |
|---|---|
| Sprint reviews, retros, internal alignment | Formal audit with executive sign-off |
| Workloads <50 resources | Large / enterprise workloads |
| Your team uses it every release | Once a year for compliance |
| You want Markdown for your repo | You need a PDF with Microsoft branding |

For formal audits keep using [Microsoft Assessments](https://learn.microsoft.com/assessments/azure-architecture-review/). For everything else, AzReview.

## Field hack

**Do the review in pairs, not alone.**

Solo-shooter bias is brutal. "Yes, this is Resilient" says the person who designed it, while the one who operates already knows the database has no replica.

Sit with someone who **didn't design** the workload (preferably operations). The first 2-3 questions will surface gaps you didn't even see. That's worth more than the whole review.

## Take it to your AI

```markdown
# AzReview workflow — quick ref

## When to run
- Sprint end: light review (~15 min, focus on 1 pillar)
- Quarter end: full review (~45 min, 5 pillars)
- Pre-prod: mandatory full review

## Output format
Markdown with sections:
1. Workload context (name, owners, criticality)
2. Score per pillar + global score
3. Top 5 gaps + suggested actions
4. Relevant Microsoft Learn links

## Anti-patterns
- Reviewer = the architect who designed it (bias)
- Score without concrete commitments
- Result in a PDF nobody opens

## Best practice
Score → top 3 gaps → owners + due date → next review
```

## Official sources

- **[WAF — Well-Architected Framework](https://learn.microsoft.com/azure/well-architected/)** — the 5 pillars in detail
- **[Microsoft Assessment — Azure Architecture Review](https://learn.microsoft.com/assessments/azure-architecture-review/)** — Microsoft's formal tool (complementary)
