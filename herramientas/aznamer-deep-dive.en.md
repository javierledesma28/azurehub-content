---
title: AzNamer · Deep dive
description: The CAF convention end-to-end, advanced audit mechanics, naming migration playbook, integration with AzDraw + AzPolicy.
category: herramientas
slug: aznamer-deep-dive
order: 31
readMinutes: 18
tags: [Govern, Deep dive]
lastUpdated: 2026-05-20
author: javier
sources:
  - https://learn.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/resource-naming
  - https://learn.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/resource-abbreviations
  - https://learn.microsoft.com/azure/azure-resource-manager/management/resource-name-rules
  - https://learn.microsoft.com/azure/governance/policy/concepts/definition-structure
---

## When to read this

This is the long version of [AzNamer](/learn/herramientas/aznamer). The concise one is 6 minutes. This is 18.

Read it if:
- You're going to **migrate naming on an existing tenant** (not greenfield)
- You have to **defend the convention** against "but we've always named it that way"
- You want to **automate scheduled audits** (Enterprise tier or Q3 manual)
- You need to **integrate AzNamer with AzPolicy** (deny non-compliant names)
- You're principal architect and you'll **teach the convention** to the team

If you just want to generate a name, [go to the short article](/learn/herramientas/aznamer).

## Full anatomy of a CAF name

The canonical pattern:

```bash
{abbr}-{workload}-{env}-{region}-{instance}
   │       │        │      │        │
   │       │        │      │        └─ instance or sequence: 001, 002, blue, green
   │       │        │      └─ region (short): eus, weu, neu, brs, sea
   │       │        └─ environment: dev, test, stg, prod, sbx
   │       └─ workload or app: payments, identity, shared, finops
   └─ resource type (CAF abbreviation): vnet, rg, st, kv, aks, app
```

Real examples of the full pattern:

| Resource                      | Generated name                | Notes                                |
|-------------------------------|-------------------------------|--------------------------------------|
| Production Virtual Network    | `vnet-payments-prod-eus-001`  | Standard                             |
| Production Storage Account    | `stpaymentsprodeusxxx`        | No hyphens (service limit)           |
| Staging Key Vault             | `kv-payments-stg-eus-001`     | 24 chars max — watch long workloads  |
| Dev AKS cluster               | `aks-payments-dev-eus-001`    | Lowercase only                       |
| Production Resource Group     | `rg-payments-prod-eus`        | No instance (RG is a singleton)      |
| Dev App Service               | `app-payments-dev-eus-001`    | DNS-unique global (random suffix)    |

### Why each segment exists

**`{abbr}`** — the resource type at the start. **Not at the end.**

```bash
# CAF
vnet-payments-prod-eus-001    ✅
# Anti-pattern: type at end
payments-prod-eus-001-vnet    ❌ — sorted alphabetically everything mixes
```

Practical reason: in the Azure portal, when you list resources sorted by name, same-type resources cluster together. Finding all VNets becomes scrolling, not filtering.

**`{workload}`** — the app or initiative. **One word, no internal hyphens.**

```bash
# CAF
payments, identity, shared, finops, infra

# Anti-patterns
customer-portal      ❌ — internal hyphen breaks pattern parsing
CustomerPortal       ❌ — camelCase isn't CAF
portal_v2            ❌ — underscore + version
```

If your workload really needs 2 words (rare), pick one. "Customer Portal" → `cportal` or `portal`.

**`{env}`** — always one of these:

| Code  | Meaning         | When to use                                          |
|-------|------------------|-------------------------------------------------------|
| `dev`   | Development     | Your main / develop branch. Constant changes.        |
| `test`  | Test            | QA, integration tests. Automated tests run here.     |
| `stg`   | Staging         | Mirror of prod. Pre-release validation.              |
| `prod`  | Production      | Production.                                          |
| `sbx`   | Sandbox         | PoCs, experiments. <30 day lifecycle recommended.   |

Resist the urge to invent `uat`, `pre`, `qa1`. If your pipeline has more stages, map them to these 5. More entropy = less possible automation.

**`{region}`** — 3-4 letter abbreviation of the Azure region.

```bash
eus  = East US               weu  = West Europe
eus2 = East US 2             neu  = North Europe
wus2 = West US 2             uks  = UK South
cus  = Central US            brs  = Brazil South
sea  = Southeast Asia        aue  = Australia East
```

See the full list in [AzNamer](/AzNamer) — `Region` category.

**`{instance}`** — 3 digits by convention (`001`, `002`, `003`).

When NOT to use 001:
- For **blue/green deployments**, use `blue`/`green` instead of `001`/`002`
- For **a/b testing**, use `a`/`b`
- For **canary releases**, use `canary`
- For singletons (RG, KV), omit the segment

## Service rules — master table

These are the limits that break patterns most often:

| Service               | Max | Min | Charset                   | Scope        | Hyphens | Notes                          |
|----------------------|-----|-----|---------------------------|--------------|---------|--------------------------------|
| Storage Account       | 24  | 3   | a-z 0-9                   | global       | ❌      | Random suffix recommended      |
| Container Registry    | 50  | 5   | a-zA-Z 0-9                | global       | ❌      | Lowercase recommended          |
| Key Vault             | 24  | 3   | a-zA-Z 0-9 -              | global       | ✅      | DNS-unique global              |
| AKS cluster           | 63  | 1   | a-z 0-9 -                 | RG           | ✅      | Lowercase only                 |
| Virtual Network       | 64  | 2   | a-zA-Z 0-9 - _ .          | RG           | ✅      | OK with special characters     |
| Subnet                | 80  | 1   | a-zA-Z 0-9 - _ .          | VNet         | ✅      | OK with special characters     |
| Resource Group        | 90  | 1   | a-zA-Z 0-9 - _ . ()       | sub          | ✅      | The most permissive            |
| Virtual Machine       | 15  | 1   | a-zA-Z 0-9 -              | RG           | ✅      | **Windows 15 chars** (NetBIOS) |
| App Service           | 60  | 2   | a-zA-Z 0-9 -              | global       | ✅      | DNS-unique global              |
| Function App          | 60  | 2   | a-zA-Z 0-9 -              | global       | ✅      | Same limit as App Service      |
| Cosmos DB account     | 44  | 3   | a-z 0-9 -                 | global       | ✅      | Lowercase only                 |
| SQL Server            | 63  | 1   | a-z 0-9 -                 | global       | ✅      | Lowercase only                 |
| Log Analytics Wkspc   | 63  | 4   | a-zA-Z 0-9 -              | RG           | ✅      | Standard                       |
| Application Insights  | 260 | 1   | varied                    | RG           | ✅      | Most permissive after RG       |

**The one that breaks the convention most: Storage Account.**

24 chars + no hyphens forces compression. If your workload is "payments" (8) and env is "prod" (4) and region is "eus" (3), you've already spent 15. Add the abbr `st` (2) and instance `001` (3) = 20. You have 4 chars left — perfect. But if your workload is "customer-onboarding" (19 without hyphen), you exceed the limit.

**Hack:** for Storage, keep a "short code" per workload in a separate table:

```yaml
# .azure-hub/workload-codes.yml
payments:           pay
customer-onboarding: cob
identity-platform:  idp
shared-services:    sh
finops:             fo
```

## How the audit works in detail

### What the engine evaluates

When you run AzNamer Audit against a connected tenant, the engine ([source](https://github.com/javierledesma28/hub28/blob/master/api/src/lib/azNamerAudit.ts)) does:

1. **List subscriptions** via ARM API (`GET /subscriptions`)
2. **For each subscription**, list resource groups + resources (`GET /subscriptions/<id>/resources`)
3. **For each resource**, look up the type in `ABBREV_RULES` (~50 types mapped)
4. **If a rule exists**, evaluate the name against 6 checks:

| Issue code                      | Severity | What it detects                              | Common false positive                         |
|---------------------------------|----------|----------------------------------------------|-----------------------------------------------|
| `missing_abbr`                  | high     | The name doesn't contain the type code       | Workloads that **are** that type (e.g., "vnet" as workload) |
| `abbr_not_prefix`               | low      | Has the abbreviation but not at the start    | Conventions that use the type at the end      |
| `too_long`                      | high     | Exceeds the service's max-length             | None — this is an Azure hard limit            |
| `whitespace`                    | high     | Contains spaces                              | None — spaces are invalid                     |
| `uppercase_in_lowercase_only`   | medium   | Uppercase in Storage/CR/AKS                  | None — this is an Azure hard limit            |
| `placeholder_name`              | high     | Matches `test`, `temp`, `final`, `real`, `copy` patterns | Legit workloads called "test"     |

### Resources NOT evaluated

If the type isn't in `ABBREV_RULES`, it's counted as `unmappedResources`. **Not a violation.** The audit reports it as a metric but doesn't penalize.

Common types without a rule yet:
- `Microsoft.Authorization/*` (policy assignments, role assignments)
- `Microsoft.Insights/dataCollectionRules`
- `Microsoft.OperationsManagement/solutions`
- Most things in Microsoft.PowerBI, Microsoft.Synapse extensions

If many "unmapped" entries in your audit bother you, [open an issue](https://github.com/javierledesma28/hub28/issues) listing the types you'd like covered.

### How to read the report

The raw audit JSON (`POST /api/workspaces/:id/audits/naming`) looks like this:

```json
{
  "generatedAt": "2026-05-20T15:42:11.000Z",
  "tenantId": "...",
  "totalSubscriptions": 3,
  "totalResources": 127,
  "evaluatedResources": 89,
  "unmappedResources": 38,
  "violations": [
    {
      "resourceId": "/subscriptions/.../virtualNetworks/MyVnet",
      "resourceName": "MyVnet",
      "resourceType": "Microsoft.Network/virtualNetworks",
      "subscriptionId": "...",
      "subscriptionName": "Production",
      "resourceGroup": "rg-shared",
      "expectedAbbr": "vnet",
      "category": "Networking",
      "severity": "high",
      "issues": ["missing_abbr"]
    }
  ],
  "subscriptions": [...],
  "warnings": []
}
```

**Compliance rate** = `(evaluatedResources - violations.length) / evaluatedResources`.

If you have 89 evaluated and 12 violations → 86.5% compliance. For a mature tenant, aim for >90%. Greenfield starting in CAF: >98%. Old brownfield tenant: 60-75% is realistic.

### Audit performance

| Resources per tenant | Approx time | Notes                                |
|---------------------|--------------|---------------------------------------|
| <100                | <5 seconds   | 1 sub, short pagination               |
| 100-500             | 5-15s        | Typically 2-3 subs                    |
| 500-2000            | 15-60s       | ARM paging starts to weigh            |
| 2000-5000           | 1-3 minutes  | Consider scheduled (Enterprise tier)      |
| >5000               | >3 minutes   | Sync timeout possible — use pg-boss   |

Today all audits run sync (direct HTTP request). In Q3 2026 we move to job queue for large tenants — Enterprise tier is the first use case.

## Migration: how to fix existing naming

**You're not renaming 2000 resources in one afternoon.** Do the migration incrementally.

### Recommended strategy

```text
Week 1-2  →  Baseline audit + compliance tag
Week 3-4  →  New resources: Policy "deny non-CAF naming"
Week 5+   →  Critical legacy resources: rename via new deploy + cutover
Quarter 2 →  Non-critical legacy: bulk rename with scripting
Quarter 3+→  Untouchable legacy: accepted, documented in exception list
```

### Step 1 — Baseline audit + exception tagging

Run the audit, export CSV, identify legacy resources you **won't rename**. Tag them:

```bash
# Bulk exception tag via az CLI
for rid in $(cat legacy-resources.txt); do
  az tag update --resource-id "$rid" \
    --operation merge \
    --tags caf:naming-exception=approved-by-arch-2026Q2
done
```

In the Hub, you can then ignore these when exporting the report (frontend filter).

### Step 2 — "Deny" policy for new resources

Define an Azure Policy that rejects new deploys that don't match the pattern. Base template:

```json
{
  "if": {
    "allOf": [
      { "field": "type", "equals": "Microsoft.Network/virtualNetworks" },
      { "not": { "field": "name", "match": "vnet-#*-???-???-###" } }
    ]
  },
  "then": { "effect": "deny" }
}
```

Apply this policy with effect `audit` first (1-2 weeks), then `deny`. Your team will complain the first week. Expected.

[Full Policy patterns doc](https://learn.microsoft.com/azure/governance/policy/concepts/definition-structure)

### Step 3 — Rename critical resources

**Azure doesn't allow renaming most resources.** You have to recreate:

```bash
# Typical VM pattern
1. Snapshot current disk
2. Deploy new VM with correct name (pointing to snapshot)
3. Cutover DNS / Load Balancer / app config
4. Decommission old VM (after 7-30 day grace period)
```

Services that DO allow live rename (no recreation):
- Resource Group (via `az group update`, but you'll need to fix broken references)
- Tag values
- Description / display names (don't affect ARM ID)

Services that do NOT allow live rename:
- VMs, Storage Accounts, Key Vaults, App Services, AKS clusters, SQL Servers, Cosmos accounts
- Practically every resource with public DNS
- Anything with its own ARM ID (most things)

### Step 4 — Report progress

Run the audit weekly. Compliance rate should rise progressively. If it's stuck at 70% for 6 months, it's a cultural problem, not a technical one.

## Integration with other Hub tools

### With AzDraw

When you draw in AzDraw, node names can follow the AzNamer convention. **Today it's manual** — no automatic sync yet.

Roadmap (Q4 2026): a "Generate names" button in AzDraw that takes the list of resources on the canvas, infers types (it already knows a VNet is a `vnet`), and applies the workspace's convention.

### With AzPolicy

[AzPolicy](/AzPolicy) (PRO tier) has naming policy templates. The integration:

1. You define the convention in AzNamer (future: persisted in workspace DB)
2. AzPolicy generates the policy JSON automatically
3. You deploy it to your tenant via Bicep / Terraform

Status: **roadmap Q3 2026**. Today policies are written by hand using the patterns you see above.

### With AzCAF (Landing Zone Studio)

When you build a Landing Zone with AzCAF, foundation resources (Mgmt Groups, hubs, identity) already come with CAF names. AzNamer audits those resources correctly with no extra config — they're in `ABBREV_RULES` by default.

## Common use cases

### Use case 1: Greenfield startup

**Situation**: You start a new tenant, 0 resources, you have to define the convention before the devops team starts deploying.

**Workflow**:
1. Define workload codes in `.azure-hub/workload-codes.yml` (committed to infra repo)
2. Share the convention with the team via the [short article](/learn/herramientas/aznamer)
3. Apply Policy "deny non-CAF" from day 1 (effect `audit` in sprint 0, `deny` in sprint 1)
4. Run audit every sprint, target 100% compliance always

**Expected result**: 6 months later, 100% compliance, 0 naming tech debt.

### Use case 2: Brownfield enterprise

**Situation**: 5-year-old production tenant, 3000 resources, "historical" naming (read: chaos).

**Workflow**:
1. Baseline audit → export CSV → sort by high severity
2. Identify top 50 critical resources → migrate those first (1-2 sprints)
3. Exception tag for untouchable legacy
4. Policy `deny` for new
5. Monthly audit with report to sponsor

**Expected result**: 12 months later, 80-85% compliance. The rest is documented exception.

### Use case 3: External consultant

**Situation**: You're hired for a "governance review" on a client tenant. 1 week engagement.

**Workflow**:
1. Day 1: Connect Tenant + audit + export Markdown
2. Day 2: Identify top 10 violations + classify by risk
3. Day 3: Document the proposed convention (based on CAF + client tweaks)
4. Day 4: Workshop with the client team — share the convention and the report
5. Day 5: Deliverable: convention + migration roadmap + reproducible audit

**Pricing tier**: Team $29/seat (min 2 = $58) is enough for 1 engagement with audits included. If you have 3+ clients with regular audits, Enterprise $149 with scheduled audits pays the delta on its own.

## Common pitfalls — the 12 I see most

| # | Pitfall | Symptom | Fix |
|---|---|---|---|
| 1 | Type at end instead of start | Alphabetical sort doesn't cluster by type | Move abbr to the start in next deploy |
| 2 | Workload with internal hyphens | `customer-portal-prod-eus-001` parsing breaks | Compress to 1 word: `cportal` |
| 3 | Storage with hyphens | Deploy fails, cryptic error | Remove hyphens, lowercase |
| 4 | Windows VM with >15 chars | Deploy OK, Windows login has issues | Truncate to 15 chars on Win VMs |
| 5 | Multiple envs in same RG | `vm-payments-{prod,dev}-eus-...` in the same RG | 1 RG per env, clean lifecycle |
| 6 | Region hardcoded in name when going multi-region | `vnet-payments-prod-eus-001` and `vnet-payments-prod-weu-001` become different workloads in your head | Consider parameterized ARM templates |
| 7 | Instance number colliding across RGs | `vm-payments-prod-eus-001` in RG-A and RG-B | Instance is per-workload-per-region, not per-RG |
| 8 | Convention change mid-stream | New resources with new pattern, old with old | Pick one pattern, don't migrate ever |
| 9 | KV/Storage with DNS-unique global | Deploy fails "name already exists" | Random suffix or `{tenant-code}` |
| 10 | App Service / Function with same name as another app | Silent DNS clash | Same as 9 |
| 11 | Resources without owner tag | "Whose is this RG-old-stuff?" | Mandatory tag policy |
| 12 | Naming convention undocumented | Every new architect invents their own | This page + commit in infra repo |

## When NOT to follow CAF

CAF is a **guideline**, not a **law**. Legitimate cases to deviate:

1. **Existing tooling compatibility** — if your CMDB / monitoring depends on legacy naming, breaking that costs more than the CAF benefit
2. **Multi-cloud strategy** — if you have AWS + Azure + GCP, an agnostic convention might be better
3. **Already-standardized corporate convention** — if your company has a 10-year-old convention that works, don't break it because CAF is newer
4. **Specific regulatory constraints** — some regulated sectors have naming imposed by mandate

In these cases, **document the deviation** and still run the audit with custom mapping. The audit doesn't "fail" if you score 80% — it fails if you can't justify the other 20%.

## Take it to your AI

```markdown
# CAF Naming Convention — full pattern

## Pattern
{abbr}-{workload}-{env}-{region}-{instance}

## Segments
- abbr: CAF resource abbreviation (vnet, rg, st, kv...)
- workload: 1 word, no internal hyphens
- env: dev | test | stg | prod | sbx
- region: 3-4 char Azure code (eus, weu...)
- instance: 001, 002... or blue/green/canary

## Service quirks
- Storage Account: 24 chars, no hyphens, lowercase
- Key Vault: 24 chars, DNS-unique global
- VM Windows: 15 chars max (NetBIOS limit)
- AKS: lowercase only, 63 chars

## Workflow
1. Define workload codes once (.azure-hub/workload-codes.yml)
2. Policy "audit" for 1-2 weeks
3. Policy "deny" for new resources
4. Brownfield: migrate critical, tag exception for legacy
5. Audit weekly, target >90% compliance

## Anti-patterns
- abbr at end (kills alphabetical grouping)
- workload with internal hyphens
- multiple envs in same RG
- mid-stream convention change
- naming convention with no docs in repo
```

## Official sources and references

- **[CAF — Resource Naming Best Practices](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/resource-naming)** — the canonical source
- **[CAF — Resource Abbreviations](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/resource-abbreviations)** — official abbreviations by type
- **[Resource Naming Rules per service](https://learn.microsoft.com/azure/azure-resource-manager/management/resource-name-rules)** — service limits
- **[Policy Definition Structure](https://learn.microsoft.com/azure/governance/policy/concepts/definition-structure)** — for writing the deny policies

### Related articles in this KB

- [AzNamer · concise](/learn/herramientas/aznamer) — the 6-minute version
- [AzNamer Audit · Connect Tenant security](/learn/connect-tenant/security-faq) — what permissions we ask for
- [Workspaces](/learn/conceptos/workspaces) — how to separate conventions by client / project
