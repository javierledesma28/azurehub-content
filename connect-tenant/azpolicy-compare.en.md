---
title: AzPolicy Compare — designed vs deployed
description: Audit your designed policy assignments against what's actually deployed in the tenant. Shows drift, missing, and rogue assignments side by side.
category: connect-tenant
slug: azpolicy-compare
order: 30
readMinutes: 6
tags: [Connect Tenant, Enterprise, AzPolicy]
lastUpdated: 2026-05-21
author: javier
sources:
  - https://learn.microsoft.com/azure/governance/policy/concepts/assignment-structure
  - https://learn.microsoft.com/rest/api/policy/policy-assignments
---

## What it does

Compares the **policy assignments you designed** in AzPolicy → Assignments against the **actually deployed** ones in the Azure tenant connected to the workspace. For every pair we tell you:

- **✓ In sync** — the deployment matches the design, no drift.
- **⚠ Drifting** — exists on both sides but at least one field changed (scope, parameters, enforcement, etc).
- **✗ Missing** — designed but not deployed yet.
- **🆕 Rogue** — deployed but not designed (someone else created it, or a management group inherited it).

Click any row to open the side-by-side JSON detail with the drift fields highlighted in amber.

## What it's for

The typical use: you're auditing a client's tenant and want to demonstrate **"this is what we agreed in the design, this is what's actually there, this is the gap"** without opening 50 portal tabs.

Concrete cases:

| Situation | What you see |
|---|---|
| Client says "we already implemented the policies" | Missing/drifting count > 0 → not exactly what was agreed |
| Another consultancy touched the tenant | Rogue count > 0 → assignments that didn't come from the formal design |
| Approved but undocumented change | Drifting with the changed field detail |
| Compliance audit closeout | Snapshot of current state with run history |

## How to use it

1. **Design the assignments**: AzPolicy → **Assignments** tab → create the assignments the client should have. They persist in the workspace.
2. **Connect the tenant** (Settings → Connect Tenant). Without this, Compare has nothing to compare against.
3. Click **COMPARE** from the Assignments tab (or navigate to `/azpolicy-compare`).
4. Pick the subscriptions to audit (all pre-selected by default).
5. **Run compare** → see counts at the top + rows with detail below.
6. Click counts to filter (e.g., drifting only).
7. On rogue rows: **Import to design** adopts that assignment into the workspace.

## Required permissions

The Tenant Connector Service Principal needs **Reader** at subscription level (or management group if auditing MGs). It's the same SP used by Tenant Import + AzNamer Audit — if those work, this one works.

It does **not** need Owner, Contributor, or Policy Contributor. Compare is 100% read-only.

## How the match works

The match identifier is the assignment `name` (the ARM-stable id, not the display name), compared **case-insensitive**. This is the most robust choice:

- `displayName` can collide and gets renamed without consequence.
- `policyDefinitionId + scope` breaks when the assignment uses `notScopes` or lives in an MG with inheritance.
- `name` is what the deployer chose and is Azure's actual primary key.

## Drift detection — fields checked

| Field | How compared |
|---|---|
| `scope` | Exact case-insensitive match |
| `policyDefinitionId` | Exact case-insensitive match |
| `enforcementMode` | Exact match (`Default` vs `DoNotEnforce`) |
| `notScopes` | Set equality (order doesn't matter, case-insensitive) |
| `parameters` | Deep equal with dotted paths. We tell you exactly which key changed (e.g., `parameters.tagName.value`) |

`displayName` and `description` are **not** drift-checked — free metadata.

## Pricing

| Tier | AzPolicy Compare |
|---|---|
| Free, PRO | Not included |
| Team | Not included (Team has Tenant Import + AzNamer Audit) |
| **Enterprise** | ✅ Unlimited audits, extended history, scheduled runs (coming) |

Compare is the Enterprise differentiator. The reason: scheduled runs + multi-tenant + drift alerts only justify the Team→Enterprise jump when you handle multiple clients with continuous compliance.

## Troubleshooting

**"Tenant not connected" (412)** — workspace has no active tenant binding. Settings → Connect Tenant.

**"Compare requires Enterprise"** — you're on PRO or Team. Feature is visible but gated. Ask for a trial if you need to validate before upgrading.

**"Compare failed" / 502** — Azure REST returned an error. Usually:
- Token expired (wait a minute, retry)
- SP lost the Reader role (verify in the portal)
- Subscription disabled

**Empty result + 0 designed** — you haven't created assignments in AzPolicy → Assignments yet. The feature compares against what YOU designed, not against a generic baseline.

**I see management group assignments flagged as rogue** — Azure returns them via inheritance. If you want to treat them as part of the design, click **Import to design** on each.

## Official sources

- [Microsoft — Policy assignment structure](https://learn.microsoft.com/azure/governance/policy/concepts/assignment-structure)
- [Microsoft — Policy Assignments REST API](https://learn.microsoft.com/rest/api/policy/policy-assignments)
- Backend endpoint: `POST /api/azpolicy/compare`
- Diff engine: `api/src/lib/policyDiff.ts` (22 unit tests cover all edge cases)
