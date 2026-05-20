---
title: AzNamer · Deep dive
description: Convención CAF de la A a la Z, audit avanzado, migración de naming existente, integración con AzDraw + AzPolicy.
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

## Cuándo leer esto

Esta es la versión larga de [AzNamer](/learn/herramientas/aznamer). La concisa son 6 minutos. Esta son 18.

Leela si:
- Vas a **migrar naming de un tenant existente** (no greenfield)
- Te toca **defender la convención** contra "pero siempre lo nombramos así"
- Querés **automatizar audits** scheduled (Enterprise tier o Q3 manual)
- Necesitás **integrar AzNamer con AzPolicy** (deny names que no cumplen)
- Sos arquitecto principal y vas a **enseñarle al equipo** la convención

Si solo querés generar un nombre, [andá al article corto](/learn/herramientas/aznamer).

## Anatomía completa de un nombre CAF

El patrón canónico:

```bash
{abbr}-{workload}-{env}-{region}-{instance}
   │       │        │      │        │
   │       │        │      │        └─ instancia o secuencia: 001, 002, blue, green
   │       │        │      └─ región abreviada: eus, weu, neu, brs, sea
   │       │        └─ entorno: dev, test, stg, prod, sbx
   │       └─ workload o app: payments, identity, shared, finops
   └─ tipo de recurso (CAF abbreviation): vnet, rg, st, kv, aks, app
```

Ejemplos reales del patrón completo:

| Recurso                       | Nombre generado              | Notas                                |
|-------------------------------|-------------------------------|--------------------------------------|
| Virtual Network productivo    | `vnet-payments-prod-eus-001`  | Estándar                             |
| Storage Account productivo    | `stpaymentsprodeusxxx`        | Sin guiones (limit servicio)         |
| Key Vault staging             | `kv-payments-stg-eus-001`     | 24 chars max — ojo workloads largos  |
| AKS cluster dev               | `aks-payments-dev-eus-001`    | Lowercase only                       |
| Resource Group productivo     | `rg-payments-prod-eus`        | Sin instance (RG es singleton)       |
| App Service dev               | `app-payments-dev-eus-001`    | DNS-unique global (sufijo random)    |

### Por qué cada segmento existe

**`{abbr}`** — el tipo de recurso al inicio. **No al final.**

```bash
# CAF
vnet-payments-prod-eus-001    ✅
# Anti-pattern: tipo al final
payments-prod-eus-001-vnet    ❌ — al ordenar alfabéticamente todo se mezcla
```

Razón práctica: en el portal de Azure, cuando listás recursos ordenados por nombre, los del mismo tipo aparecen juntos. Buscar todos los VNets es scroll, no filtro.

**`{workload}`** — la app o iniciativa. **Una palabra, sin guiones internos.**

```bash
# CAF
payments, identity, shared, finops, infra

# Anti-patterns
customer-portal      ❌ — guion interno rompe el parsing del patrón
CustomerPortal       ❌ — camelCase no es CAF
portal_v2            ❌ — underscore + version
```

Si tu workload realmente necesita 2 palabras (raro), elegí una. "Customer Portal" → `cportal` o `portal`.

**`{env}`** — siempre uno de estos:

| Code  | Significado     | Cuándo usar                                          |
|-------|------------------|-------------------------------------------------------|
| `dev`   | Development     | Tu rama main / develop. Cambios constantes.          |
| `test`  | Test            | QA, integration tests. Tests automatizados corren acá. |
| `stg`   | Staging         | Mirror de prod. Validación pre-release.              |
| `prod`  | Production      | Productivo.                                          |
| `sbx`   | Sandbox         | PoCs, experimentos. Lifecycle <30 días recomendado.  |

Resistí la tentación de inventar `uat`, `pre`, `qa1`. Si tu pipeline tiene más etapas, mapéalas a estas 5. Más entropía = menos automatización posible.

**`{region}`** — abreviación de 3-4 letras de la región Azure.

```bash
eus  = East US               weu  = West Europe
eus2 = East US 2             neu  = North Europe
wus2 = West US 2             uks  = UK South
cus  = Central US            brs  = Brazil South
sea  = Southeast Asia        aue  = Australia East
```

Ver la lista completa en [AzNamer](/AzNamer) — categoría `Region`.

**`{instance}`** — 3 dígitos por convención (`001`, `002`, `003`).

Cuándo NO usar 001:
- Para **blue/green deployments**, usá `blue`/`green` en vez de `001`/`002`
- Para **a/b testing**, usá `a`/`b`
- Para **canary releases**, usá `canary`
- Para singletons (RG, KV), omitir el segmento

## Reglas por servicio — tabla maestra

Estas son las limitaciones que más rompen los patrones:

| Servicio              | Max | Min | Charset                   | Scope        | Hyphens | Notas                          |
|----------------------|-----|-----|---------------------------|--------------|---------|--------------------------------|
| Storage Account       | 24  | 3   | a-z 0-9                   | global       | ❌      | Sufijo random recomendado      |
| Container Registry    | 50  | 5   | a-zA-Z 0-9                | global       | ❌      | Lowercase recomendado          |
| Key Vault             | 24  | 3   | a-zA-Z 0-9 -              | global       | ✅      | DNS-unique global              |
| AKS cluster           | 63  | 1   | a-z 0-9 -                 | RG           | ✅      | Lowercase only                 |
| Virtual Network       | 64  | 2   | a-zA-Z 0-9 - _ .          | RG           | ✅      | OK con caracteres especiales   |
| Subnet                | 80  | 1   | a-zA-Z 0-9 - _ .          | VNet         | ✅      | OK con caracteres especiales   |
| Resource Group        | 90  | 1   | a-zA-Z 0-9 - _ . ()       | sub          | ✅      | El más permisivo               |
| Virtual Machine       | 15  | 1   | a-zA-Z 0-9 -              | RG           | ✅      | **Windows 15 chars** (NetBIOS) |
| App Service           | 60  | 2   | a-zA-Z 0-9 -              | global       | ✅      | DNS-unique global              |
| Function App          | 60  | 2   | a-zA-Z 0-9 -              | global       | ✅      | Mismo limit que App Service    |
| Cosmos DB account     | 44  | 3   | a-z 0-9 -                 | global       | ✅      | Lowercase only                 |
| SQL Server            | 63  | 1   | a-z 0-9 -                 | global       | ✅      | Lowercase only                 |
| Log Analytics Wkspc   | 63  | 4   | a-zA-Z 0-9 -              | RG           | ✅      | Standard                       |
| Application Insights  | 260 | 1   | varios                    | RG           | ✅      | El más permisivo después de RG |

**El que más rompe la convención: Storage Account.**

24 chars + sin guiones obliga a comprimir. Si tu workload es "payments" (8) y el env es "prod" (4) y la región es "eus" (3), ya gastaste 15. Sumás el abbr `st` (2) y el instance `001` (3) = 20. Te quedan 4 chars de margen — perfecto. Pero si tu workload es "customer-onboarding" (19 sin guion), ya superás el límite.

**Hack:** para Storage, mantené un "código corto" por workload mapeado en una tabla aparte:

```yaml
# .azure-hub/workload-codes.yml
payments:           pay
customer-onboarding: cob
identity-platform:  idp
shared-services:    sh
finops:             fo
```

## Cómo funciona el audit en detalle

### Lo que el engine evalúa

Cuando corrés AzNamer Audit contra un tenant conectado, el engine ([código](https://github.com/javierledesma28/AzureHub/blob/master/api/src/lib/azNamerAudit.ts)) hace:

1. **Lista subscriptions** vía ARM API (`GET /subscriptions`)
2. **Por cada subscription**, lista resource groups + resources (`GET /subscriptions/<id>/resources`)
3. **Por cada recurso**, hace lookup del tipo en `ABBREV_RULES` (~50 tipos mapeados)
4. **Si tiene regla**, evalúa el nombre contra 6 checks:

| Issue code                      | Severity | Qué detecta                                  | Falso positivo común                          |
|---------------------------------|----------|----------------------------------------------|-----------------------------------------------|
| `missing_abbr`                  | high     | El nombre no contiene el código del tipo     | Workloads que **son** ese tipo (ej. "vnet" como workload) |
| `abbr_not_prefix`               | low      | Tiene la abbreviación pero no al inicio      | Convenciones que usan el tipo al final        |
| `too_long`                      | high     | Excede el max-length del servicio            | Ninguno — esto es un hard limit Azure         |
| `whitespace`                    | high     | Contiene espacios                            | Ninguno — los espacios son inválidos          |
| `uppercase_in_lowercase_only`   | medium   | Mayúsculas en Storage/CR/AKS                 | Ninguno — esto es un hard limit Azure         |
| `placeholder_name`              | high     | Matchea patrones `test`, `temp`, `final`, `real`, `copy` | Workloads legitimos llamados "test" |

### Recursos que NO se evalúan

Si el tipo no está en `ABBREV_RULES`, se cuenta como `unmappedResources`. **No es violation.** El audit te lo reporta como métrica pero no penaliza.

Tipos comunes sin regla todavía:
- `Microsoft.Authorization/*` (policy assignments, role assignments)
- `Microsoft.Insights/dataCollectionRules`
- `Microsoft.OperationsManagement/solutions`
- Casi todo en Microsoft.PowerBI, Microsoft.Synapse extensions

Si te frustra ver muchos "unmapped" en tu audit, [abrí un issue](https://github.com/javierledesma28/AzureHub/issues) listando los tipos que querés que cubramos.

### Cómo leer el reporte

El JSON crudo del audit (`POST /api/workspaces/:id/audits/naming`) tiene esta forma:

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

Si tenés 89 evaluados y 12 violations → 86.5% compliance. Para un tenant maduro apuntá a >90%. Greenfield arrancando en CAF: >98%. Tenant brownfield viejo: 60-75% es realista.

### Performance del audit

| Recursos por tenant | Tiempo aprox | Notas                                  |
|---------------------|--------------|----------------------------------------|
| <100                | <5 segundos  | 1 sub, paginación corta                |
| 100-500             | 5-15s        | 2-3 subs típicamente                   |
| 500-2000            | 15-60s       | Empieza a pesar el ARM paging          |
| 2000-5000           | 1-3 minutos  | Considerá scheduled (Enterprise tier)      |
| >5000               | >3 minutos   | Sync timeout posible — usar pg-boss    |

Hoy todos los audits corren sync (HTTP request directo). En Q3 2026 movemos a job queue para tenants grandes — Enterprise tier es el primer caso de uso.

## Migración: cómo arreglar naming existente

**No vas a renombrar 2000 recursos en una tarde.** Hacé la migración incremental.

### Estrategia recomendada

```text
Semana 1-2  →  Audit baseline + tag de cumplimiento
Semana 3-4  →  Nuevos recursos: Policy "deny non-CAF naming"
Semana 5+   →  Recursos críticos viejos: rename via deploy nuevo + cutover
Trimestre 2 →  Recursos no-críticos: bulk rename con scripting
Trimestre 3+ →  Legacy intocable: aceptado, documentado en exception list
```

### Paso 1 — Audit baseline + tag de exception

Corré el audit, exportá CSV, identificá los recursos legacy que **no vas a renombrar**. Tag them:

```bash
# Tag de exception en bulk via az CLI
for rid in $(cat legacy-resources.txt); do
  az tag update --resource-id "$rid" \
    --operation merge \
    --tags caf:naming-exception=approved-by-arch-2026Q2
done
```

En el Hub, después podés ignorar estos al exportar el reporte (filtro frontend).

### Paso 2 — Policy "deny" para nuevos recursos

Definí un Azure Policy que rechace deploys nuevos que no cumplan el patrón. Plantilla base:

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

Aplicá esta policy con effect `audit` primero (1-2 semanas), después `deny`. Tu equipo se va a quejar la primera semana. Es esperado.

[Doc completa de Policy patterns](https://learn.microsoft.com/azure/governance/policy/concepts/definition-structure)

### Paso 3 — Renombrar recursos críticos

**Azure no permite renombrar la mayoría de recursos.** Hay que recrear:

```bash
# Pattern típico para VMs
1. Snapshot del disco actual
2. Deploy VM nueva con nombre correcto (apuntando al snapshot)
3. Cutover DNS / Load Balancer / app config
4. Decommission VM vieja (después de 7-30 días de gracia)
```

Servicios que SÍ permiten rename en vivo (sin recreación):
- Resource Group (vía `az group update`, pero las references rotas hay que arreglarlas)
- Tag values
- Description / display names (no afectan el ARM ID)

Servicios que NO permiten rename en vivo:
- VMs, Storage Accounts, Key Vaults, App Services, AKS clusters, SQL Servers, Cosmos accounts
- Prácticamente todos los recursos con DNS público
- Todo lo que tiene un ARM ID propio (la mayoría)

### Paso 4 — Reportar progreso

Corré el audit semanalmente. La compliance rate debería subir progresivamente. Si lleva 6 meses estancada en 70%, hay un problema cultural, no técnico.

## Integración con otros tools del Hub

### Con AzDraw

Cuando dibujás en AzDraw, los nombres de los nodos pueden seguir la convención AzNamer. **Hoy es manual** — no hay sync automático todavía.

Roadmap (Q4 2026): un botón "Generate names" en AzDraw que tome la lista de recursos del canvas, infiera los tipos (ya sabe que un VNet es un `vnet`), y aplique la convención del workspace.

### Con AzPolicy

[AzPolicy](/AzPolicy) (PRO tier) tiene plantillas de policies de naming. La integración:

1. Definís la convención en AzNamer (futuro: persistida en DB del workspace)
2. AzPolicy genera el JSON de la policy automáticamente
3. Lo deployás a tu tenant via Bicep / Terraform

Estado: **roadmap Q3 2026**. Hoy las policies se escriben a mano usando los patterns que ves arriba.

### Con AzCAF (Landing Zone Studio)

Cuando armás una Landing Zone con AzCAF, los recursos foundation (Mgmt Groups, hubs, identity) ya vienen con nombres CAF. AzNamer audita esos recursos correctamente sin config extra — están en `ABBREV_RULES` por default.

## Casos de uso típicos

### Caso 1: Greenfield startup

**Situación**: Arrancás un tenant nuevo, 0 recursos, tenés que definir convención antes que el equipo de devops empiece a deployar.

**Workflow**:
1. Definí workload codes en `.azure-hub/workload-codes.yml` (committeado al repo de infra)
2. Pasá la convención al equipo con el [article corto](/learn/herramientas/aznamer)
3. Aplicá Policy "deny non-CAF" desde día 1 (effect `audit` en sprint 0, `deny` en sprint 1)
4. Corré audit cada sprint, target 100% compliance siempre

**Resultado esperado**: 6 meses después, 100% compliance, 0 deuda técnica de naming.

### Caso 2: Brownfield enterprise

**Situación**: Tenant productivo con 5 años, 3000 recursos, naming "histórico" (léase: caos).

**Workflow**:
1. Audit baseline → exportá CSV → ordená por severity high
2. Identificá los recursos críticos top 50 → migra esos primero (1-2 sprints)
3. Tag de exception para los legacy intocables
4. Policy `deny` para nuevos
5. Audit mensual con reporte al sponsor

**Resultado esperado**: 12 meses después, 80-85% compliance. El restante es exception documentada.

### Caso 3: Consultor externo

**Situación**: Te contratan para una "review de governance" en un tenant del cliente. 1 semana de engagement.

**Workflow**:
1. Día 1: Connect Tenant + audit + export Markdown
2. Día 2: Identificá top 10 violations + clasificá por riesgo
3. Día 3: Documentá la convención propuesta (basado en CAF + ajustes del cliente)
4. Día 4: Workshop con el equipo del cliente — pasás la convención y el reporte
5. Día 5: Entregable: convención + roadmap de migración + audit reproducible

**Pricing tier**: Consulting $49 te alcanza para 1 engagement. Si tenés 3+ clientes con audits regulares, Enterprise $149 con scheduled audits paga el delta solo.

## Common pitfalls — las 12 que más vi

| # | Pitfall | Síntoma | Fix |
|---|---|---|---|
| 1 | Tipo al final en vez de al inicio | Ordenar alfabéticamente no agrupa por tipo | Move abbr al inicio en próximo deploy |
| 2 | Workload con guiones internos | `customer-portal-prod-eus-001` parsing roto | Comprimir a 1 palabra: `cportal` |
| 3 | Storage con guiones | Deploy falla, error críptico | Quitar guiones, lowercase |
| 4 | VM Windows con >15 chars | Deploy OK, login Windows da problemas | Truncar a 15 chars en VMs Win |
| 5 | Múltiples envs en un mismo RG | `vm-payments-{prod,dev}-eus-...` en el mismo RG | 1 RG por env, lifecycle limpio |
| 6 | Región hardcoded en nombre cuando vas multi-región | `vnet-payments-prod-eus-001` y `vnet-payments-prod-weu-001` se vuelven distintos workloads en tu cabeza | Considerá ARM templates parametrizados |
| 7 | Instance number colliding entre RGs | `vm-payments-prod-eus-001` en RG-A y RG-B | El instance es per-workload-per-region, no per-RG |
| 8 | Cambio de convención a mitad de camino | Recursos nuevos con patrón nuevo, viejos con viejo | Pickeá un único patrón, no migrar nunca |
| 9 | KV/Storage con DNS-unique global | Deploy falla "name already exists" | Sufijo random o `{tenant-code}` |
| 10 | App Service / Function en mismo nombre que otra app | DNS clash silencioso | Mismo punto 9 |
| 11 | Recursos sin tag de owner | "¿De quién es este RG-old-stuff?" | Tag policy obligatoria |
| 12 | Naming convention sin documentar | Cada nuevo arquitecto inventa la suya | Esta página + commit en repo de infra |

## Cuando NO seguir CAF

CAF es una **guía**, no una **ley**. Casos legítimos para desviarse:

1. **Compatibilidad con tooling existente** — si tu CMDB / monitoring depende de un naming antiguo, romper eso cuesta más que el beneficio CAF
2. **Multi-cloud strategy** — si tenés AWS + Azure + GCP, tal vez convenga una convención propia agnóstica
3. **Convención corporate ya estandarizada** — si tu empresa tiene una convención con 10 años de antigüedad y funciona, no la rompas porque CAF es más nuevo
4. **Constraints regulatorios específicos** — algunos sectores regulados tienen naming impuesto por norma

En estos casos, **documentá la desviación** y todavía corré el audit con un mapping custom. El audit "no falla" si scoreás 80% — falla si no podés justificar el otro 20%.

## Llevatelo a tu IA

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
- naming convention sin docs en repo
```

## Fuentes oficiales y referencias

- **[CAF — Resource Naming Best Practices](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/resource-naming)** — la fuente canónica
- **[CAF — Resource Abbreviations](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/resource-abbreviations)** — abreviaciones oficiales por tipo
- **[Resource Naming Rules per service](https://learn.microsoft.com/azure/azure-resource-manager/management/resource-name-rules)** — limits por servicio
- **[Policy Definition Structure](https://learn.microsoft.com/azure/governance/policy/concepts/definition-structure)** — para escribir las policies de deny

### Articles relacionados en esta KB

- [AzNamer · concise](/learn/herramientas/aznamer) — la versión 6 minutos
- [AzNamer Audit · Connect Tenant security](/learn/connect-tenant/security-faq) — qué permisos pedimos
- [Workspaces](/learn/conceptos/workspaces) — cómo separar convenciones por cliente / proyecto
