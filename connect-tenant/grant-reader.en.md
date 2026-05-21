---
title: Grant Reader access to the Tenant Connector
description: Step-by-step guide to give the Azure Hub Service Principal read access in your tenant.
category: connect-tenant
slug: grant-reader
order: 30
readMinutes: 4
tags: [Connect Tenant, Setup, Required]
lastUpdated: 2026-05-21
author: javier
sources:
  - https://learn.microsoft.com/azure/role-based-access-control/role-assignments-cli
  - https://learn.microsoft.com/azure/role-based-access-control/role-assignments-portal
---

## Why this is needed

When you click **Connect Tenant** in a workspace, Microsoft Entra creates an Enterprise Application for **Azure Hub Tenant Connector** in your tenant. That gives Azure Hub *identity* in your tenant — but **no Azure RBAC permissions yet**.

To list and import resources, the Service Principal needs the **Reader** role on at least one subscription (or a management group covering several subs).

This is the standard Azure model: the consent step lets the app exist; the role assignment step grants it access. We deliberately ask for **Reader** only — never Contributor, Owner, or anything that can write to your tenant.

## Pick a scope

| Scope | What the SP can see | When to use |
|---|---|---|
| **One subscription** | All resources in that sub | Most common — start here, expand later |
| **Multiple subscriptions** | Each granted individually | Hub-spoke or landing zone architectures |
| **Management group** | All subs under that MG | When you manage many subs and want broad coverage |
| **Single resource group** | Resources in that RG only | Most restrictive — useful for proofs of concept |

Start with one subscription. You can always add more later.

## Option A — Azure Portal (UI)

1. Go to **Subscriptions** in the Azure portal.
2. Click the subscription where you want to grant access.
3. Click **Access control (IAM)** in the left blade.
4. Click **+ Add** → **Add role assignment**.
5. Role: search for **Reader** → Next.
6. Members: **Assign access to** = User, group, or service principal → click **+ Select members**.
7. In the search box, paste the Connector app id: `[CONNECTOR_CLIENT_ID]` *(shown in the Tenant Import modal)*.
8. The "Azure Hub Tenant Connector" entry appears → select it → click **Select**.
9. Review + assign.

Wait ~30-60 seconds for propagation, then click the refresh icon in the Tenant Import modal.

## Option B — Azure CLI (recommended)

```bash
# 1. Login to the tenant you connected to Azure Hub
az login --tenant <YOUR_TENANT_ID>

# 2. List your subscriptions and pick one
az account list --query "[].{name:name, id:id}" -o table

# 3. Get the SP object id for the Tenant Connector (one-time)
SP_OBJECT_ID=$(az ad sp show --id [CONNECTOR_CLIENT_ID] --query id -o tsv)
echo "SP object id: $SP_OBJECT_ID"

# 4. Grant Reader on the subscription
az role assignment create \
  --assignee-object-id $SP_OBJECT_ID \
  --assignee-principal-type ServicePrincipal \
  --role "Reader" \
  --scope "/subscriptions/<SUBSCRIPTION_ID>"
```

## Option C — PowerShell

```powershell
# 1. Login
Connect-AzAccount -Tenant <YOUR_TENANT_ID>

# 2. Select the subscription
Set-AzContext -Subscription <SUBSCRIPTION_ID>

# 3. Get the SP object id
$sp = Get-AzADServicePrincipal -ApplicationId "[CONNECTOR_CLIENT_ID]"

# 4. Grant Reader
New-AzRoleAssignment `
  -ObjectId $sp.Id `
  -RoleDefinitionName "Reader" `
  -Scope "/subscriptions/<SUBSCRIPTION_ID>"
```

## Verifying it worked

```bash
az role assignment list --assignee [CONNECTOR_CLIENT_ID] --all -o table
```

You should see at least one row with `Reader` and a scope. If it shows up, return to the Tenant Import modal and click the refresh icon (🔄) in the header.

## Common mistakes

- **Logged into the wrong tenant**: `az account show` should print the tenant ID you connected to Azure Hub. If not, `az login --tenant <correct-id>`.
- **Assigned to the wrong principal**: the App Registration's *application* object is different from the *service principal*. Always use `az ad sp show --id <client-id>` to find the SP — never the application object id.
- **Role with too few permissions**: `Reader` is the minimum. Built-in roles like `Reader and Data Access` work too, but `Storage Account Reader` does not — it's resource-type scoped.
- **Propagation delay**: role assignments can take 30 seconds to 10 minutes to propagate globally. If the refresh in the modal still shows no subs, wait a bit and try again.

## Revoking access

When you no longer want Azure Hub to access your tenant:

1. Click **Disconnect Tenant** in the workspace settings (this revokes the consent).
2. To also revoke the RBAC role:
   ```bash
   az role assignment delete --assignee [CONNECTOR_CLIENT_ID] --scope "/subscriptions/<SUBSCRIPTION_ID>"
   ```
3. Optionally, delete the Enterprise Application from your tenant: Azure portal → **Microsoft Entra ID** → **Enterprise applications** → **Azure Hub Tenant Connector** → **Properties** → **Delete**.

## What we can and cannot do with Reader

| Capability | Reader allows? |
|---|---|
| List resources and properties | ✅ |
| Read ARM templates and configuration | ✅ |
| Read role assignments and Managed Identities | ✅ |
| Read App Service settings (for data plane edges) | ⚠️ Requires `Microsoft.Web/sites/config/list/action` — included in Reader at RG scope |
| Read secret VALUES from Key Vault | ❌ Never. We resolve KV references by URL only. |
| Write any resource | ❌ Reader is read-only by design |
| Delete any resource | ❌ Same |

The connector role assignment is your safety net: as long as it's `Reader`, Azure Hub physically cannot mutate your tenant.
