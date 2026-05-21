---
title: Dar acceso Reader al Tenant Connector
description: Guía paso a paso para darle al Service Principal de Azure Hub permiso de lectura en tu tenant.
category: connect-tenant
slug: grant-reader
order: 30
readMinutes: 4
tags: [Connect Tenant, Setup, Obligatorio]
lastUpdated: 2026-05-21
author: javier
sources:
  - https://learn.microsoft.com/azure/role-based-access-control/role-assignments-cli
  - https://learn.microsoft.com/azure/role-based-access-control/role-assignments-portal
---

## Por qué hace falta esto

Cuando hacés click en **Conectar Tenant** en un workspace, Microsoft Entra crea una Enterprise Application del **Azure Hub Tenant Connector** en tu tenant. Eso le da a Azure Hub *identidad* dentro de tu tenant — pero **todavía sin permisos RBAC de Azure**.

Para listar e importar recursos, el Service Principal necesita el rol **Reader** en al menos una subscription (o un management group que cubra varias subs).

Es el modelo estándar de Azure: el consent permite que la app exista; el role assignment le da acceso. Pedimos **sólo Reader** — nunca Contributor, Owner ni ningún rol que pueda escribir en tu tenant.

## Elegí un scope

| Scope | Qué ve el SP | Cuándo usarlo |
|---|---|---|
| **Una subscription** | Todos los recursos de esa sub | Lo más común — empezá acá, ampliás después |
| **Múltiples subs** | Cada una asignada individualmente | Hub-spoke o landing zones |
| **Management group** | Todas las subs debajo del MG | Si gestionás muchas subs y querés cobertura amplia |
| **Un Resource Group** | Sólo los recursos de ese RG | Lo más restrictivo — útil para POCs |

Empezá con una sub. Después podés agregar más.

## Opción A — Azure Portal (UI)

1. Andá a **Subscriptions** en el portal de Azure.
2. Click en la sub donde querés dar acceso.
3. Click en **Access control (IAM)** en el menú izquierdo.
4. Click en **+ Add** → **Add role assignment**.
5. Role: buscá **Reader** → Next.
6. Members: **Assign access to** = User, group, or service principal → click en **+ Select members**.
7. En la caja de búsqueda pegá el client id del Connector: `[CONNECTOR_CLIENT_ID]` *(aparece en el modal de Tenant Import)*.
8. Aparece "Azure Hub Tenant Connector" → seleccionalo → click en **Select**.
9. Review + assign.

Esperá ~30-60 segundos para propagación, después click en el ícono refresh del modal.

## Opción B — Azure CLI (recomendado)

```bash
# 1. Login al tenant que conectaste con Azure Hub
az login --tenant <TU_TENANT_ID>

# 2. Listá tus subs y elegí una
az account list --query "[].{name:name, id:id}" -o table

# 3. Obtené el object id del SP del Tenant Connector (una sola vez)
SP_OBJECT_ID=$(az ad sp show --id [CONNECTOR_CLIENT_ID] --query id -o tsv)
echo "SP object id: $SP_OBJECT_ID"

# 4. Asigná Reader en la subscription
az role assignment create \
  --assignee-object-id $SP_OBJECT_ID \
  --assignee-principal-type ServicePrincipal \
  --role "Reader" \
  --scope "/subscriptions/<SUBSCRIPTION_ID>"
```

## Opción C — PowerShell

```powershell
# 1. Login
Connect-AzAccount -Tenant <TU_TENANT_ID>

# 2. Seleccioná la sub
Set-AzContext -Subscription <SUBSCRIPTION_ID>

# 3. Obtené el object id del SP
$sp = Get-AzADServicePrincipal -ApplicationId "[CONNECTOR_CLIENT_ID]"

# 4. Asigná Reader
New-AzRoleAssignment `
  -ObjectId $sp.Id `
  -RoleDefinitionName "Reader" `
  -Scope "/subscriptions/<SUBSCRIPTION_ID>"
```

## Verificar que funcionó

```bash
az role assignment list --assignee [CONNECTOR_CLIENT_ID] --all -o table
```

Tiene que aparecer al menos una fila con `Reader` y un scope. Si aparece, volvé al modal de Tenant Import y click en el ícono refresh (🔄) del header.

## Errores comunes

- **Logueado al tenant equivocado**: `az account show` tiene que mostrar el tenant ID que conectaste a Azure Hub. Si no, `az login --tenant <id-correcto>`.
- **Asignado al principal equivocado**: el *application object* del App Registration es distinto del *service principal*. Siempre usá `az ad sp show --id <client-id>` para encontrar el SP — nunca el object id de la application.
- **Rol con permisos insuficientes**: `Reader` es el mínimo. Roles built-in como `Reader and Data Access` también sirven, pero `Storage Account Reader` no — está scopeado a un tipo de recurso.
- **Demora de propagación**: los role assignments pueden tardar 30 segundos a 10 minutos en propagarse. Si el refresh en el modal sigue mostrando 0 subs, esperá un poco y volvé a probar.

## Revocar acceso

Cuando ya no quieras que Azure Hub acceda a tu tenant:

1. Click en **Disconnect Tenant** en la configuración del workspace (revoca el consent).
2. Para revocar también el rol RBAC:
   ```bash
   az role assignment delete --assignee [CONNECTOR_CLIENT_ID] --scope "/subscriptions/<SUBSCRIPTION_ID>"
   ```
3. Opcional: borrar la Enterprise Application del tenant: Azure portal → **Microsoft Entra ID** → **Enterprise applications** → **Azure Hub Tenant Connector** → **Properties** → **Delete**.

## Qué podemos y NO podemos hacer con Reader

| Capacidad | ¿Reader permite? |
|---|---|
| Listar recursos y sus propiedades | ✅ |
| Leer templates ARM y configuración | ✅ |
| Leer role assignments y Managed Identities | ✅ |
| Leer settings de App Service (para edges de data plane) | ⚠️ Necesita `Microsoft.Web/sites/config/list/action` — incluido en Reader a scope RG |
| Leer VALORES de secretos en Key Vault | ❌ Nunca. Resolvemos las KV references sólo por URL. |
| Escribir cualquier recurso | ❌ Reader es read-only por diseño |
| Borrar cualquier recurso | ❌ Idem |

El role assignment del connector es tu red de seguridad: mientras sea `Reader`, Azure Hub físicamente no puede modificar tu tenant.
