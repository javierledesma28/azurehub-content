---
title: Connect Tenant security
description: What we do with your tenant. What we don't. And how you verify it yourself.
category: connect-tenant
slug: security-faq
order: 10
readMinutes: 6
tags: [Security, Trust]
lastUpdated: 2026-05-20
author: javier
sources:
  - https://learn.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent
  - https://learn.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow
  - https://learn.microsoft.com/azure/role-based-access-control/built-in-roles
---

## A quick reflection

The client you're selling Connect Tenant to **will be afraid**. Good.

Connecting a tenant to a third-party SaaS isn't trivial — the question "what does this token do when no one's watching" is totally legitimate. This page exists so you can answer it with confidence, and so the client's admin can audit us before clicking.

## What it's for

It explains **exactly** what Hub28 can and cannot do in a connected tenant. No marketing abstractions — specific permissions, scopes, and the commands the admin can run to verify it in their own tenant.

**What you'll be able to do:**

- Show the client the exact permissions before asking for consent
- Answer security objections with concrete data
- Prove the binding is revocable in seconds
- Audit the SP's activity in their tenant logs

## How it works

### What we ask

A single multi-tenant App Registration ("Hub28 Tenant Connector") with these **Application permissions**:

| Permission | API | Why we ask for it |
|---|---|---|
| `Directory.Read.All` | Microsoft Graph | Resolve tenant displayName, list users for AzRBAC audit (Q4) |
| `RoleManagement.Read.All` | Microsoft Graph | Role inventory for AzRBAC Review (Q4) |
| `Policy.Read.All` | Microsoft Graph | AzCompliance Mapper (Q4) |
| `user_impersonation` | Azure Service Management | ARM API for listing subs and resources (AzNamer Audit — live) |

**All are `.Read.*` or equivalent.** We never ask for any `.ReadWrite`, any `Directory.AccessAsUser.All`, or any permission that allows modifying anything.

### What Hub28 does with those permissions

| Action | Do we do it? |
|---|---|
| Read subscription list | ✅ Yes — for the audit |
| Read resources (names, types, tags, location) | ✅ Yes — for the audit |
| Read policy assignments | ✅ Yes — when AzPolicy Compare ships |
| Create, modify, delete resources | ❌ No — no write permissions |
| Deploy ARM/Bicep templates | ❌ No |
| Access Key Vault / Storage / DB content | ❌ No (those require data-plane perms) |
| Read secrets, passwords, tokens | ❌ No |
| Change role assignments | ❌ No |
| Sell / share audit data with third parties | ❌ No |

### How to verify from the client's tenant

Once the admin accepts consent, an Enterprise Application called **"Hub28 Tenant Connector"** appears in THEIR tenant. To audit it:

```bash
# Show the app, its permissions and assignments
az ad sp show --id <APP_OBJECT_ID>

# Show the SP's usage logs (last 7 days)
az monitor activity-log list \
  --caller "<SP_OBJECT_ID>" \
  --start-time $(date -u -d '7 days ago' +%Y-%m-%dT%H:%M:%SZ)

# Revoke the binding from Azure (no need to come back to our app)
az ad sp delete --id <APP_OBJECT_ID>
```

Or via portal: **Microsoft Entra → Enterprise applications → Hub28 Tenant Connector → Permissions / Sign-in logs / Delete**.

### How the token works

1. The admin accepts consent on `login.microsoftonline.com` (never in our UI)
2. Microsoft creates the Service Principal in THEIR tenant
3. Our backend uses `client_credentials grant` with our app secret to request tokens
4. Each token lives ~60 min, scope `https://management.azure.com/.default`, tenant-id of the client
5. We use it only for the audit you triggered; **no background processes touching your tenant**

## Architectural decision

**What sets Hub28 apart from other SaaS that ask for consent**:

| Model | When it fits | Risk |
|---|---|---|
| **Delegated permissions** (acting "as user") | Basic auth, your user acting | If user is Owner, the SaaS is too |
| **Application permissions read-only** ✅ Hub28 | Audits, inventories, reporting | Low — no write, no data-plane |
| **Application permissions full** | Provisioners, deployers, IaC managers | High — they can modify everything |

Hub28 deliberately sits in the middle: **enough permissions to audit, zero permissions to touch anything**. If you need a provisioner, Hub28 isn't the tool — go to Terraform Cloud or Bicep deployments.

Reference: [Microsoft consent and permissions framework](https://learn.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

## Field hack

**Before connecting production, connect a sandbox tenant.**

If the client has a separate dev/sandbox tenant, connect that first. Run the full audit, watch it end-to-end, show the result to the admin. Then connect production with confidence based on evidence, not promises.

Any provider that insists on "just connect production directly" without offering this option deserves another question.

## Take it to your AI

```markdown
# Hub28 Tenant Connector — security one-pager

## Permissions
- Read-only Application permissions:
  Directory.Read.All, RoleManagement.Read.All,
  Policy.Read.All, ARM user_impersonation
- Zero write permissions. Zero data-plane.

## What it does
- Lists subscriptions and resources via ARM API
- Audits naming, policies, RBAC, compliance (some on roadmap)
- Exports CSV/Markdown report

## What it does NOT do
- Create / modify / delete resources
- Read KV secrets, Storage data, or DB content
- Change permissions or assignments
- Sell or share data

## How to revoke
Microsoft Entra → Enterprise apps → Hub28 Tenant Connector → Delete
(instant, no need to come back to Hub28)
```

## Official sources

- **[Permissions and consent framework](https://learn.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)** — how Entra's consent model works
- **[client_credentials grant flow](https://learn.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)** — the flow we use for tokens
- **[Azure built-in roles](https://learn.microsoft.com/azure/role-based-access-control/built-in-roles)** — Reader is the minimum for ARM audits
