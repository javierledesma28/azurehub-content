---
title: Seguridad de Connect Tenant
description: Qué hacemos con tu tenant. Qué no. Y cómo lo verificás vos mismo.
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

## Reflexión antes de seguir

El cliente al que vas a vender Connect Tenant **va a tener miedo**. Y bien.

Conectar un tenant a un SaaS de terceros no es trivial — la pregunta "qué hace este token cuando nadie mira" es totalmente legítima. Esta página existe para que vos puedas responderla con seguridad y para que el admin del cliente pueda auditarnos antes de hacer click.

## Para qué sirve

Te explica **exactamente** qué Azure Hub puede y no puede hacer en un tenant conectado. Nada de abstracciones marketinglish — permisos específicos, scopes, y los comandos que el admin puede correr para verificarlo en su propio tenant.

**Lo que vas a poder hacer:**

- Mostrarle al cliente los permisos exactos antes de pedir consent
- Responder objeciones de seguridad con datos concretos
- Demostrar que el binding es revocable en segundos
- Auditar la actividad del SP en los logs de su tenant

## Cómo funciona

### Qué pedimos

Una sola App Registration multi-tenant ("Azure Hub Tenant Connector") con estas **Application permissions**:

| Permiso | API | Por qué lo pedimos |
|---|---|---|
| `Directory.Read.All` | Microsoft Graph | Resolver displayName del tenant, listar usuarios para AzRBAC audit (Q4) |
| `RoleManagement.Read.All` | Microsoft Graph | Inventario de roles para AzRBAC Review (Q4) |
| `Policy.Read.All` | Microsoft Graph | AzCompliance Mapper (Q4) |
| `user_impersonation` | Azure Service Management | ARM API para listar subs y recursos (AzNamer Audit — live) |

**Todos son `.Read.*` o equivalentes.** No pedimos ningún `.ReadWrite`, ningún `Directory.AccessAsUser.All`, ninguno que permita modificar nada.

### Qué hace Azure Hub con esos permisos

| Acción | ¿Lo hacemos? |
|---|---|
| Leer lista de subscriptions | ✅ Sí — para el audit |
| Leer recursos (nombres, tipos, tags, location) | ✅ Sí — para el audit |
| Leer policy assignments | ✅ Sí — cuando AzPolicy Compare arranque |
| Crear, modificar, eliminar recursos | ❌ No — sin permisos de escritura |
| Deployar templates ARM/Bicep | ❌ No |
| Acceder a contenido de Key Vault / Storage / DB | ❌ No (esos requieren data-plane permissions) |
| Leer secretos, contraseñas, tokens | ❌ No |
| Cambiar role assignments | ❌ No |
| Vender / compartir los datos del audit con terceros | ❌ No |

### Cómo verificarlo desde el tenant del cliente

Una vez que el admin acepta el consent, en SU tenant aparece una Enterprise Application llamada **"Azure Hub Tenant Connector"**. Para auditarla:

```bash
# Ver la app, sus permisos y assignments
az ad sp show --id <APP_OBJECT_ID>

# Ver los logs de uso del SP (últimos 7 días)
az monitor activity-log list \
  --caller "<SP_OBJECT_ID>" \
  --start-time $(date -u -d '7 days ago' +%Y-%m-%dT%H:%M:%SZ)

# Revocar el binding desde Azure (no requiere venir a nuestra app)
az ad sp delete --id <APP_OBJECT_ID>
```

O por portal: **Microsoft Entra → Enterprise applications → Azure Hub Tenant Connector → Permissions / Sign-in logs / Delete**.

### Cómo funciona el token

1. El admin acepta el consent en `login.microsoftonline.com` (nunca en nuestra UI)
2. Microsoft crea el Service Principal en SU tenant
3. Nuestro backend usa `client_credentials grant` con nuestro app secret para pedir tokens
4. Cada token tiene ~60 min de vida, scope `https://management.azure.com/.default`, tenant-id del cliente
5. Lo usamos solo para el audit que disparaste; **no hay procesos en background tocando tu tenant**

## Decisión arquitectural

**Lo que diferencia a Azure Hub de otros SaaS que piden consent**:

| Modelo | Cuándo conviene | Riesgo |
|---|---|---|
| **Delegated permissions** (acceso "as user") | Auth básica, tu usuario actuando | Si el user tiene Owner, el SaaS también |
| **Application permissions read-only** ✅ Azure Hub | Audits, inventarios, reporting | Bajo — sin write, sin data-plane |
| **Application permissions full** | Provisioners, deployers, IaC managers | Alto — pueden modificar todo |

Azure Hub se sienta deliberadamente en el medio: **suficientes permisos para auditar, cero permisos para tocar nada**. Si necesitás un provisioner, Azure Hub no es la herramienta — andá a Terraform Cloud o Bicep deployments.

Referencia: [Microsoft consent and permissions framework](https://learn.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

## Hack de campo

**Antes de conectar producción, conectá un tenant de sandbox.**

Si el cliente tiene un tenant de dev/sandbox separado, conectalo primero. Corré el audit completo, miralo de punta a punta, mostrale el resultado al admin. Después conectás producción con confianza basada en evidencia, no en promesas.

Cualquier proveedor que insiste en "conectá producción directo" sin oferecer esta opción merece una pregunta más.

## Llevatelo a tu IA

```markdown
# Azure Hub Tenant Connector — security one-pager

## Permisos
- Application permissions read-only:
  Directory.Read.All, RoleManagement.Read.All,
  Policy.Read.All, ARM user_impersonation
- Cero permisos de escritura. Cero data-plane.

## Lo que hace
- Lista subscriptions y recursos via ARM API
- Audit naming, policies, RBAC, compliance (algunos roadmap)
- Export CSV/Markdown del reporte

## Lo que NO hace
- Crear / modificar / eliminar recursos
- Leer secretos de KV, datos en Storage o DBs
- Cambiar permisos o asignaciones
- Vender o compartir datos

## Cómo revocar
Microsoft Entra → Enterprise apps → Azure Hub Tenant Connector → Delete
(instantáneo, no requiere venir a Azure Hub)
```

## Fuentes oficiales

- **[Permissions and consent framework](https://learn.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)** — cómo funciona el modelo de consent de Entra
- **[client_credentials grant flow](https://learn.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)** — el flow que usamos para los tokens
- **[Azure built-in roles](https://learn.microsoft.com/azure/role-based-access-control/built-in-roles)** — Reader es lo mínimo para audits ARM
