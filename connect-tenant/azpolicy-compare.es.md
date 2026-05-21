---
title: AzPolicy Compare — designed vs deployed
description: Auditá tus policy assignments diseñados contra lo realmente desplegado en el tenant. Mostramos drift, missing y rogue en una vista lado a lado.
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

## Qué hace

Compara los **policy assignments que diseñaste** en AzPolicy → Assignments contra los **realmente desplegados** en el tenant Azure conectado al workspace. Para cada par te decimos:

- **✓ In sync** — el deployment matchea lo diseñado, sin drift.
- **⚠ Drifting** — existe en ambos lados pero al menos un campo cambió (scope, parameters, enforcement, etc).
- **✗ Missing** — diseñado pero no deployado todavía.
- **🆕 Rogue** — deployado pero no diseñado (alguien más lo creó, o lo aplicó management group de arriba).

Click en cualquier fila para abrir el detalle JSON lado a lado con los campos drift resaltados en naranja.

## Para qué sirve

El uso típico: estás auditando el tenant de un cliente y querés demostrar **"esto es lo acordado en el diseño, esto es lo real, esto es la brecha"** sin abrir 50 pestañas del portal.

Casos concretos:

| Situación | Lo que ves |
|---|---|
| Cliente dice "ya implementamos las policies" | Missing/drifting count > 0 → no es exactamente lo acordado |
| Otra consultora tocó el tenant | Rogue count > 0 → assignments que no salieron del diseño formal |
| Cambio aprobado pero no documentado | Drifting con detalle del campo cambiado |
| Cierre de auditoría compliance | Snapshot del estado actual con run history |

## Cómo usarlo

1. **Diseñá los assignments**: AzPolicy → tab **Assignments** → crear los assignments que el cliente debería tener. Persisten en el workspace.
2. **Conectá el tenant** del cliente (Settings → Connect Tenant). Sin esto, Compare no tiene contra qué comparar.
3. Click **COMPARE** desde el tab Assignments (o navegá a `/azpolicy-compare`).
4. Elegí las suscripciones a auditar (por default vienen todas pre-seleccionadas).
5. **Run compare** → ves los counts arriba + las filas con detalle abajo.
6. Click los counts para filtrar (ej. solo drifting).
7. En filas rogue: **Importar al diseño** adopta ese assignment al workspace.

## Permisos necesarios

El Service Principal del Tenant Connector necesita **Reader** a nivel subscription (o management group si auditás MGs). Es el mismo SP que usás para Tenant Import + AzNamer Audit — si esos funcionan, este también.

**No** necesita Owner, Contributor ni Policy Contributor. Compare es 100% read-only.

## Cómo se hace el match

El identificador de match es el `name` del assignment (el id ARM-estable, no el display name), comparado **case-insensitive**. Esto es lo más robusto:

- `displayName` puede tener duplicados y se renombra sin consecuencias.
- `policyDefinitionId + scope` rompe si el assignment usa `notScopes` o vive en MG con herencia.
- `name` lo elige el deployer y es la primary key real de Azure.

## Drift detection — qué campos se chequean

| Campo | Cómo se compara |
|---|---|
| `scope` | Exact match case-insensitive |
| `policyDefinitionId` | Exact match case-insensitive |
| `enforcementMode` | Exact match (`Default` vs `DoNotEnforce`) |
| `notScopes` | Set equality (orden no importa, case-insensitive) |
| `parameters` | Deep equal con paths dotted. Te decimos exactamente qué key cambió (ej. `parameters.tagName.value`) |

`displayName` y `description` **no** se chequean por drift — son metadata libre.

## Pricing

| Tier | AzPolicy Compare |
|---|---|
| Free, PRO | No incluido |
| Team | No incluido (Team tiene Tenant Import + AzNamer Audit) |
| **Enterprise** | ✅ Audits ilimitados, history extendido, scheduled runs (próximo) |

Compare es el diferenciador Enterprise. La razón: scheduled runs + multi-tenant + alertas de drift son features que solo justifican el salto de Team→Enterprise cuando manejás múltiples clientes con compliance continuo.

## Troubleshooting

**"Tenant not connected" (412)** — el workspace no tiene un tenant binding activo. Settings → Connect Tenant.

**"Compare requires Enterprise"** — estás en PRO o Team. El feature está visible pero gated. Si necesitás probarlo antes de upgradear, pedinos un trial.

**"Compare failed" / 502** — Azure REST devolvió error. Suele ser:
- Token vencido (esperá un minuto, vuelve a intentar)
- SP perdió el rol Reader (verificá en el portal)
- Subscription deshabilitada

**Resultado vacío + 0 designed** — todavía no creaste assignments en AzPolicy → Assignments. El feature compara contra lo que vos diseñaste, no contra una baseline genérica.

**Veo assignments del management group como rogue** — Azure los retorna por herencia. Si querés tratarlos como parte del diseño, click **Importar al diseño** en cada uno.

## Fuentes oficiales

- [Microsoft — Policy assignment structure](https://learn.microsoft.com/azure/governance/policy/concepts/assignment-structure)
- [Microsoft — Policy Assignments REST API](https://learn.microsoft.com/rest/api/policy/policy-assignments)
- Backend endpoint: `POST /api/azpolicy/compare`
- Diff engine: `api/src/lib/policyDiff.ts` (22 unit tests cubren todos los edge cases)
