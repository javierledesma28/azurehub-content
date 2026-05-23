---
title: Tenant Import — generá diagramas desde Azure
description: Conectá tu tenant una vez y después importá cualquier RG a AzDraw con boundaries y edges ya armados.
category: connect-tenant
slug: tenant-import
order: 20
readMinutes: 5
tags: [Connect Tenant, Team, Enterprise]
lastUpdated: 2026-05-20
author: javier
sources:
  - https://learn.microsoft.com/azure/governance/resource-graph/overview
  - https://learn.microsoft.com/azure/role-based-access-control/built-in-roles
---

## Qué hace

Elegís cualquier Resource Group de tu tenant Azure conectado y Hub28 te lo dibuja en AzDraw — con boundaries nested (RG → VNet → Subnet), los íconos correctos por tipo de recurso y las conexiones de red entre ellos (peerings de VNet, NIC↔subnet, asociaciones NSG, Private Endpoints, VNet integration de App Service).

Mantenés el control: los nodos importados van a una capa **Reality** separada, así seguís dibujando ideas nuevas en la capa **Design** y mostrás/ocultás la vista importada cuando querés.

## Cómo usarlo

1. **Conectá un tenant** a tu workspace (Settings → Connect Tenant). Consentimiento de admin, una sola vez.
2. Abrí cualquier diagrama en AzDraw.
3. Click en **Importar** en la barra superior.
4. Seleccioná uno o más Resource Groups en el árbol (sub → RG).
5. Confirmá. El canvas se popula en ~2-10 segundos según el tamaño del RG.

¿Querés mantenerlo sincronizado? Click en **Refresh** en cualquier momento — Hub28 vuelve a consultar el tenant y te muestra un diff (nuevo / cambiado / borrado) para que decidas qué aplicar. Las posiciones que hayas movido se respetan.

## Permisos necesarios

El Service Principal del Tenant Connector necesita **Reader** a nivel de subscription o management group. Nada más — no Contributor, no Owner.

- Para Phase 1-3 (import + refresh + network edges + Private Endpoints): `Reader` alcanza.
- Para Phase 4 (capa Identity con role assignments): se sumarán scopes de lectura tipo `Role Based Access Control Administrator` cuando esa capa salga.

Connect Tenant usa el **admin consent** estándar de Microsoft Entra para un App Registration multi-tenant. La pantalla de consent lista exactamente qué pedimos — revisalo antes de hacer Accept.

## Qué se importa

Phase 1 + 2 cubren ~30 tipos de recurso que representan el 80% de las arquitecturas que vemos:

- **Boundaries**: Resource Group, Virtual Network, Subnet
- **Networking**: NIC, Public IP, Load Balancer, Application Gateway, Firewall, NSG, Route Table, Private Endpoint, Private DNS, Bastion, VPN Gateway, Front Door, WAF
- **Compute**: VM, VMSS, Disk
- **Storage / data**: Storage Account, SQL Server, SQL DB, PostgreSQL Flex, MySQL Flex, Cosmos DB, Redis, Synapse
- **Web**: App Service Plan, App Service, Static Web App, Function App
- **Identity / Security**: Key Vault, Managed Identity, Defender, Sentinel
- **Containers**: AKS, ACR, Container Instance, Container App
- **Integration**: Service Bus, Event Hub, Event Grid, API Management, Logic App, Data Factory
- **Observability**: Log Analytics, Application Insights
- **AI**: Cognitive Services, ML workspace

Cualquier cosa fuera de esta lista se importa igual — con un ícono Azure genérico y el ARM type completo como label, así no perdés información.

## Capas on-demand (Identity + Data plane)

Más allá de los edges de red (que vienen automáticamente con el import), hay dos capas extra disponibles a pedido desde el panel derecho:

### Capa Identity (Phase 4)
- **Qué muestra**: edges Managed Identity → recurso, con label del nombre del rol (Reader, Storage Blob Data Reader, Contributor, etc.).
- **Fuente**: cruza las MIs System+User assigned de tus recursos importados con role assignments de Azure a nivel sub/RG/recurso.
- **Cuándo usarlo**: review de Zero-Trust, detectar scopes demasiado permisivos, auditoría de "quién puede leer qué".
- **Permiso**: lectura de `Microsoft.Authorization/roleAssignments` y `Microsoft.Authorization/roleDefinitions` — incluido en `Reader` a nivel subscription.
- **UI**: click en **Mostrar identidad** en el panel derecho. Click en **Ocultar** para quitar los edges del canvas.

### Capa Data plane (Phase 5)
- **Qué muestra**: edges App Service → SQL / Storage / Cosmos / Key Vault / Service Bus / Event Hub / Redis, inferidos a partir de app settings + connection strings.
- **Fuente**: pattern matching de `*.database.windows.net`, `*.blob.core.windows.net`, `@Microsoft.KeyVault(VaultName=...)`, etc.
- **Cuándo usarlo**: descubrir dependencias ocultas entre una App Service y sus backing services sin leer YAML de despliegue.
- **Permiso**: `Microsoft.Web/sites/config/list/action` en el RG del App Service — incluido en `Reader`.
- **Privacidad**: los valores reales de los settings (que pueden contener credenciales) se leen de forma transitoria y se descartan. Nunca los persistimos. Sólo los target ARM IDs resueltos se vuelven edges.
- **UI**: click en **Mostrar dependencias** en el panel derecho.

## Cómo funcionan las capas

| Capa | Qué va ahí | Cuándo se crea | ¿Refrescable? |
|---|---|---|---|
| **Design** | Lo que dibujás vos manualmente | Default, siempre presente | No |
| **Reality** | Todo lo importado del tenant | Después del primer import | Sí, vía Refresh |

Las podés mostrar/ocultar independientemente desde el panel derecho. Mezclá libremente: imaginá dibujar una arquitectura futura "to-be" en Design encima de tu "as-is" actual en Reality.

## Soft cap y tenants grandes

Te avisamos a partir de **200 recursos importados por diagrama**. Más allá de eso el canvas sigue funcionando, pero los layouts se vuelven difíciles de leer. La movida correcta para landing zones es partir el import: un diagrama por hub-spoke pair, uno por workload RG, etc. Podés tener tantos diagramas como permita tu plan.

## Qué nunca leemos

- Nunca leemos valores de secretos, passwords de connection strings ni contenido de secretos de Key Vault.
- Nunca escribimos contra tu tenant. Read-only es el contrato.
- Para la capa de data plane futura (Phase 5), opt-in por feature — y aún así sólo resolvemos resource IDs, no valores de secretos.

## Pricing

| Tier | Tenant Import |
|---|---|
| Free, PRO | No incluido |
| **Team** | 1 tenant conectado, imports ilimitados, refresh, 30 días de snapshots |
| **Enterprise** | Multi-tenant, refresh agendado, drift alerts, 90 días de snapshots |

## Troubleshooting

**Error "Tenant not connected"** — tu workspace no tiene un tenant binding activo. Settings → Connect Tenant.

**"Insufficient permissions" / lista de recursos vacía** — el Service Principal no tiene Reader en el scope correcto. Pedile al admin que asigne Reader a nivel de subscription o management group.

**El import tarda >30s** — el tenant es muy grande o Resource Graph está throttled. Probá filtrar a menos Resource Groups.

**Algunos recursos muestran íconos genéricos** — esos tipos no están en el top-30 actual del mapper. La data se importa correctamente igual; sólo no tenemos ícono custom todavía. El label muestra el ARM type completo así sabés qué es.
