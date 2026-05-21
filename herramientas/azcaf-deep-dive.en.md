---
title: AzCAF · Deep dive
description: The Cloud Adoption Framework actually applied — Landing Zones, Management Groups, the 7 pillars, adoption anti-patterns, and how not to burn 6 months.
category: herramientas
slug: azcaf-deep-dive
order: 71
readMinutes: 20
tags: [Foundation, Deep dive]
lastUpdated: 2026-05-20
author: javier
sources:
  - https://learn.microsoft.com/azure/cloud-adoption-framework/
  - https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/
  - https://learn.microsoft.com/azure/cloud-adoption-framework/ready/enterprise-scale/
  - https://learn.microsoft.com/azure/governance/management-groups/overview
---

## When to read this

This is the long guide to AzCAF. If you want a quick overview of what CAF is, skip this and go to [CAF on Microsoft Learn](https://learn.microsoft.com/azure/cloud-adoption-framework/). Come back when you have base context.

Read it if:
- You're going to **design a Landing Zone from scratch** (greenfield enterprise)
- You have to **refactor an in-flight Azure adoption** that drifted
- You're a **consultant** and need a common framework with the client
- Your CTO/CFO asks "why does it take us so long to deploy anything?"
- You're going to **defend the cost** of a proper Landing Zone vs "let's just deploy and see"

If you only want the high-level CAF steps, go to the short article (placeholder — AzCAF concise article on roadmap).

## Why CAF exists

CAF was born from a pattern Microsoft saw repeated in every enterprise client:

1. **They adopt Azure fast** — a dev team starts with a couple of PoCs
2. **3-6 months later** there are 12 subscriptions, 0 governance, chaotic naming
3. **Year 1** the security team finds out, panic mode
4. **Year 2** a consultancy is hired to "clean house"
5. **Year 3** there's still tech debt from phase 1

CAF tries to avoid all this **before** starting. The premise: invest 4-8 weeks upfront in governance, and save the 12 months of remediation later.

Does it work? Yes, when followed. Most teams skip it and regret it later.

## The 7 CAF pillars (not the WAF ones)

**Don't confuse with Well-Architected Framework.** WAF is 5 technical design pillars (reliability, security, cost, ops, performance). CAF is 7 pillars of **adoption process**.

```text
Strategy    →  Why cloud? Business goals
Plan        →  Skills, change management, timeline
Ready       →  Landing Zones, naming, governance baseline
Migrate     →  Lift-and-shift, rehost, refactor
Innovate    →  Cloud-native, AI/ML, modernization
Govern      →  Policies, RBAC, continuous compliance
Manage      →  Operations, monitoring, lifecycle
Secure      →  Zero trust, threat protection
```

AzCAF (the tool) focuses on **Ready** + **Govern** because that's where 80% of avoidable work is. The other pillars are organizational processes, not directly toolable.

## The core concept: Landing Zone

A **Landing Zone** is a pre-configured Azure environment where your workloads can "land" with all the plumbing already done. Networking, identity, governance, monitoring — all set up before a dev writes their first `terraform apply`.

### Before vs after having a Landing Zone

| Without Landing Zone | With Landing Zone |
|---|---|
| Dev creates a new subscription on their own | Subscription provisioned from IaC |
| Improvised naming per workload | CAF convention applied by policy |
| Networking patched workload by workload | Central hub + consistent spokes |
| Quarterly audits that catch issues late | Policies that prevent drift at deploy time |
| Every workload reinvents monitoring | Centralized Log Analytics |

### Typical anatomy

```text
Management Group: Tenant Root
  │
  ├─ Platform MG ──────────────────── Shared services
  │   ├─ Identity sub                 Entra Connect, AD DS, PIM
  │   ├─ Management sub               Log Analytics, Sentinel, Azure Monitor
  │   └─ Connectivity sub             Hub VNet, Firewall, ExpressRoute
  │
  ├─ Landing Zones MG ─────────────── Production workloads
  │   ├─ Corp LZ                      Internal workloads
  │   │   ├─ Sub: corp-prod
  │   │   ├─ Sub: corp-dev
  │   │   └─ Sub: corp-shared
  │   └─ Online LZ                    Internet-facing workloads
  │       ├─ Sub: online-prod
  │       ├─ Sub: online-dev
  │       └─ Sub: online-shared
  │
  ├─ Sandbox MG ──────────────────── Experimentation
  │   └─ Sandbox subs                 1 per team, TTL 30 days
  │
  └─ Decommissioned MG ─────────────── Sunset workloads
      └─ Subs marked for deletion
```

This is **enterprise-scale landing zone** (CAF's "complete" version). Simpler versions exist for smaller companies — see "Adoption sizing" below.

### Minimum LZ components

```yaml
Networking:
  - Hub VNet (with Firewall + DNS Resolver)
  - Spoke VNets per workload with peering
  - VPN / ExpressRoute for onprem connection
  - Network Security Groups per subnet

Identity:
  - Entra ID Connect (sync with onprem AD if exists)
  - PIM (Privileged Identity Management) for privileged roles
  - Conditional Access policies baseline
  - Application registrations governance

Governance:
  - Naming convention (CAF) deployed via Policy
  - Mandatory tagging policy (CostCenter, Owner, Environment, DataClassification)
  - Allowed locations policy (limit to your approved regions)
  - Allowed VM SKUs policy (avoid accidental giant VMs)

Monitoring:
  - Centralized Log Analytics workspace
  - Azure Monitor + Application Insights baseline
  - Sentinel (SIEM) if you'll do SecOps
  - Diagnostic settings policy (force logs to Log Analytics)

Security:
  - Microsoft Defender for Cloud enabled
  - Central Key Vault for shared secrets
  - Just-in-Time VM access policy
  - Network watcher in all regions
```

A proper LZ has 50-80 resources before a single workload lands. If it seems like a lot, **it is**. But then every new workload is ~10 resources instead of 60.

## Adoption sizing — how complete an LZ do you need?

CAF has "enterprise-scale" as the ideal. The reality is not every company needs that.

### Tier 1 — Solo or small team (<5 people)

**You don't need formal Landing Zone.** You have:
- 1 Entra tenant
- 1-2 subscriptions (dev + prod)
- Resource groups per workload
- Naming convention in a README

What's worth investing in:
- Naming convention (AzNamer)
- Mandatory tagging (CostCenter, Owner)
- 1 central Key Vault

Total setup time: **2-3 days**.

### Tier 2 — Medium team (10-50 people, 1 company)

**Simplified LZ, 3-tier:**

```text
Tenant Root
  └─ Platform MG (1 sub: management + connectivity combined)
  └─ Workloads MG
       ├─ Prod sub
       ├─ Dev sub
       └─ Shared sub
  └─ Sandbox MG
       └─ Sandbox sub (TTL policy)
```

Components:
- Basic Hub VNet (no Firewall, NSGs are enough)
- Centralized Log Analytics
- Mandatory naming + tagging policies
- Defender for Cloud (Free tier)

Total setup time: **2-3 weeks**.

### Tier 3 — Enterprise (50+ people, multi-team)

**Full enterprise-scale landing zone** like the diagram above.

Components:
- Hub VNet with Azure Firewall + DDoS Protection
- VPN / ExpressRoute for onprem
- Entra ID Connect + PIM + advanced Conditional Access
- Sentinel for continuous SecOps
- Defender Standard + custom policies
- Multi-region (DR site)

Total setup time: **2-4 months** with a dedicated team.

### Tier 4 — Multi-company / MSP consultancy

**Tenant per client + your own management tenant.**

Each client has their own Tier 2 or 3 LZ. You as MSP:
- Workspace in Azure Hub per client
- Connect Tenant to each (Enterprise tier $149)
- Scheduled daily/weekly audits with delta detection
- Consolidated reports

Time: depends on client. Your internal setup is 1 week, then each new client onboarding is 1-2 weeks.

## Step-by-step implementation (Tier 2 as example)

This is what you build in ~2 weeks for a medium company. We break it into sprints.

### Sprint 1 — Foundation (week 1)

**Day 1: Architecture decisions**
- Tenant naming: `<company>.onmicrosoft.com` already exists, ok
- Subscription strategy: 4 subs (mgmt, prod, dev, shared)
- Approved regions: `eus` (primary) + `weu` (DR)
- Naming convention: strict CAF (see [AzNamer deep dive](/learn/herramientas/aznamer-deep-dive))

**Day 2-3: Management Groups**
```bash
az account management-group create --name "Platform"     --parent <root-id>
az account management-group create --name "Workloads"    --parent <root-id>
az account management-group create --name "Sandbox"      --parent <root-id>
```

**Day 4-5: Subscriptions**
- Move existing subs to their corresponding MGs
- If greenfield, create via EA portal or Enterprise Agreement

### Sprint 2 — Networking (week 1-2)

**Day 6-7: Hub VNet in Platform MG**
- `vnet-hub-prod-eus-001` with CIDR `10.0.0.0/16`
- Subnets: `GatewaySubnet`, `AzureFirewallSubnet`, `snet-shared-services`
- No Firewall yet if your budget is small — NSGs are enough in Tier 2

**Day 8-9: Spoke VNets**
- For each workload sub: 1 spoke VNet with peering to the hub
- Non-overlapping CIDRs (use AzNet planner)

**Day 10: DNS**
- Private DNS Resolver in the hub (not Azure Firewall DNS if no Firewall)
- Forwarder to your onprem DNS if applicable

### Sprint 3 — Governance + Monitoring (week 2)

**Day 11-12: Policies**
- Naming convention policy (audit mode first, deny in 2 weeks)
- Mandatory tagging: `CostCenter`, `Owner`, `Environment`, `Project`
- Allowed locations: only `eus` and `weu`
- Allowed VM SKUs: block `Standard_M*` and other monsters

**Day 13: Log Analytics + diagnostic settings**
- 1 workspace in management sub: `log-mgmt-prod-eus-001`
- Diagnostic settings policy: every resource must send logs to this workspace
- Retention: 90 days (more is PRO)

**Day 14: Defender for Cloud**
- Enable on all subs (Free tier to start)
- Standard tier only on prod if budget allows

**Day 15: Documentation**
- LZ diagram in AzDraw
- Naming convention in IaC repo README
- Runbook for new workload onboarding

## How NOT to ruin the LZ after having one

Having the LZ is 30% of the work. Maintaining it is 70%. The most common anti-patterns:

### Anti-pattern 1: "Let's just deploy here, we'll move it later"

**Symptom**: A new workload deploys to the sandbox sub or an old sub, "to get moving".

**Why it happens**: No formal onboarding process for new workloads.

**Fix**: Document a clear runbook:
1. Decide which LZ applies (Corp vs Online)
2. Request new sub provisioning or use existing
3. Apply baseline IaC (network peering, tags, initial RBAC)
4. Only then deploy the workload

### Anti-pattern 2: "Firewall is too expensive, we'll skip it"

**Symptom**: The hub has no Azure Firewall. Each spoke has its own NSGs but east-west traffic is open.

**When it's OK**: Tier 1-2 without strict compliance.

**When it's not**: Any regulated compliance (PCI, HIPAA, SOC2). Firewall ($1000+/month) is cheaper than a breach.

### Anti-pattern 3: "Granular RBAC later"

**Symptom**: Owner role in production for 20 people. "We'll limit it to Contributor later".

**Why it happens**: PIM (Privileged Identity Management) seems complex upfront.

**Fix**: From day 1:
- Production = NOBODY has permanent Owner. PIM elevation for critical tasks.
- Contributor only in dev/sandbox
- Reader is the default for everyone

### Anti-pattern 4: "Sandbox without TTL"

**Symptom**: You have 47 sandbox subscriptions, 15 with untouched resources for 8 months, none of which you know who owns.

**Fix**: Policy that enforces `caf:ttl-days=30` tag on every sandbox resource. Automation Account that deletes resources past that TTL.

### Anti-pattern 5: "Naming convention that's just a README"

**Symptom**: You have the convention documented but devs keep creating `vm-test-2-final`.

**Fix**: Policy with `deny` effect that matches your convention's regex. Audit first (1-2 weeks, alerts), then deny. The team complains the first week. Expected and necessary.

### Anti-pattern 6: "Optional monitoring"

**Symptom**: Log Analytics is there but workloads don't send logs because "it's expensive".

**Fix**: Mandatory diagnostic settings policy → Log Analytics. If cost scares you, adjust retention to 30 days in dev and 90 in prod. But logs **must** be there.

## Continuous audit — how to know if your LZ holds up

An LZ that's built and never audited **decomposes in 3-6 months**. Recurring anti-pattern.

### Metrics to track

| Metric | Target | How to measure |
|---|---|---|
| **Compliance rate** (naming) | >90% | [AzNamer Audit](/learn/herramientas/aznamer-deep-dive) |
| **Tag coverage** | 100% on mandatory tags | Azure Policy compliance dashboard |
| **Subs without owner tag** | 0 | Custom Policy + dashboard |
| **Resources in non-approved regions** | 0 | Allowed locations policy |
| **VMs with direct Public IP** | 0 (should go through LB / Firewall) | Custom Policy |
| **Resources without diagnostic settings** | 0 | Diagnostic settings policy |
| **Sandbox subs >30 days without tag refresh** | 0 | Custom Automation |

### Recommended cadence

- **Daily**: Defender for Cloud alerts (automatic)
- **Weekly**: Policy compliance dashboard review (10 min)
- **Monthly**: AzNamer Audit + AzReview update (1 hour)
- **Quarterly**: LZ diagram review + roadmap update (half day)

### How AzCAF integrates with audits

AzCAF (the tool) today helps you:
1. Generate the naming convention → export for AzNamer + AzPolicy
2. Design the Management Groups hierarchy
3. Define baseline policies
4. Generate diagrams of the proposed LZ (handoff to AzDraw)

On roadmap (Q3 2026):
- Generate Bicep templates of the full LZ
- Live audit: connect tenant + compare against the blueprint you designed
- Drift detection between designed and deployed

## Use cases

### Use case 1: Startup growing to enterprise

**Situation**: 18-month-old startup, went from 5 to 50 employees, Azure became chaotic.

**Workflow**:
1. Month 1: Inventory current + Tier 2 vs Tier 3 decision
2. Month 2: Sprint 1-2 (Foundation + Networking) in parallel with current prod
3. Month 3: Sprint 3 (Governance) + workload-by-workload migration plan
4. Months 4-6: Workload migration (1-2 per sprint) into proper LZ
5. Month 7+: Only new workloads in LZ, legacy on sunset path

**Expected result**: 12 months later, 90% of workloads in proper LZ, 10% legacy with migration plan.

### Use case 2: Multinational with M&A

**Situation**: You acquire a company, you inherit its Azure tenant. You have to integrate it into yours.

**Workflow**:
1. **Don't merge tenants**. Keep the acquired one a while.
2. Complete audit of the acquired tenant (AzNamer, AzPolicy, AzRBAC reviews)
3. Map their LZ (or lack thereof) against yours
4. Integration plan per workload, not per sub
5. Gradual migration to your LZ + progressive decommission

**Time**: 6-18 months depending on size.

### Use case 3: Consultant building an LZ for a client

**Situation**: New client asks "set up governance in Azure for us". 6-week engagement.

**Workflow**:
1. Week 1: Discovery + tier decision (Tier 2 covers 90%)
2. Week 2-3: Sprint 1-2 (Foundation + Networking)
3. Week 4: Sprint 3 (Governance + Monitoring)
4. Week 5: Workshop with their team, knowledge transfer
5. Week 6: Baseline audit + 12-month roadmap

**Pricing tier**: Team $29/seat (min 2 = $58/mo) is enough for one client with audits included. Enterprise $149/mo if you handle 3+.

## When NOT to use CAF

CAF isn't a silver bullet. Legitimate cases for not following it:

1. **You're solo or team <5** — Tier 1 is enough. Formal CAF is overkill.
2. **Ephemeral / experimental workloads** — Sandbox sub with TTL is better than proper LZ.
3. **Predominantly multi-cloud** — If Azure is 30% of your footprint, your own multi-cloud convention makes more sense.
4. **You inherit a different existing LZ** — If you arrive at a company with working own governance, don't break it because CAF says otherwise.
5. **Specific regulatory constraints** — Some regulators have their own framework. Follow theirs, not Microsoft's.

CAF is what I'd recommend to a new Tier 2-3 client. For the other cases there are variants.

## Honest roadmap (of AzCAF the tool)

**Q3 2026:**
- Generable Bicep templates of complete Tier 2 (ready-to-deploy LZ)
- AzPolicy integration (export naming + tagging policies)
- Diagram exportable to AzDraw

**Q4 2026:**
- Connect Tenant + drift audit between designed and deployed LZ
- Tier 3 templates (complete enterprise-scale)

**Q1 2027:**
- Parallel Bicep + Terraform output
- Multi-tenant LZ design for MSPs (Enterprise tier)

**Not promised (yet):**
- LZ design via natural language ("help me design a regulated fintech LZ")
- Automatic workload-by-workload migration tooling

## Take it to your AI

```markdown
# CAF Landing Zone — adoption playbook

## Decision: which tier?
Solo / <5 ppl:        Tier 1 (no formal LZ, naming + tags)
Medium team:          Tier 2 (simplified LZ 3-MG)
Enterprise 50+ ppl:   Tier 3 (full enterprise-scale)
MSP / multi-client:   Tier 4 (tenant per client)

## Tier 2 sprint plan (2 weeks)
1. Foundation (MGs + Subs strategy)
2. Networking (Hub-Spoke + DNS)
3. Governance (Policies + Monitoring + Defender)

## Anti-patterns
- "We'll move it later" for new workloads
- Optional Firewall in regulated prod
- Permanent Owner role in prod
- Sandbox without TTL
- Naming convention only in README (not in Policy)
- Optional monitoring

## Audit cadence
Daily:      Defender alerts (auto)
Weekly:     Policy compliance dashboard
Monthly:    AzNamer + AzReview update
Quarterly:  LZ diagram + roadmap update

## Minimum components per layer
Networking:  Hub VNet, peering, NSGs
Identity:    Entra Connect, PIM, Conditional Access
Governance:  Naming Policy, Tag Policy, Allowed Locations
Monitoring:  Central Log Analytics, Diagnostic Settings
Security:    Defender, Central Key Vault
```

## Official sources and references

- **[CAF — Cloud Adoption Framework](https://learn.microsoft.com/azure/cloud-adoption-framework/)** — the canonical source
- **[CAF — Landing Zones](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/)** — LZ concept end-to-end
- **[Enterprise-scale Landing Zone](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/enterprise-scale/)** — the complete Tier 3 version
- **[Management Groups](https://learn.microsoft.com/azure/governance/management-groups/overview)** — MG hierarchy in detail

### Related articles in this KB

- [AzNamer · deep dive](/learn/herramientas/aznamer-deep-dive) — the naming convention applied across the LZ
- [AzDraw · deep dive](/learn/herramientas/azdraw-deep-dive) — to visually diagram the LZ
- [Workspaces](/learn/conceptos/workspaces) — how to separate LZ designs by client / project
- [Connect Tenant security](/learn/connect-tenant/security-faq) — what permissions the deployed LZ audit requests
