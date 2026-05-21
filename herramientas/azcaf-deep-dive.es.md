---
title: AzCAF · Deep dive
description: El Cloud Adoption Framework aplicado de verdad — Landing Zones, Management Groups, los 7 pilares, anti-patterns de adopción, y cómo no quemar 6 meses.
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

## Cuándo leer esto

Esta es la guía larga de AzCAF. Si querés un overview rápido de qué es CAF, dejá esto y andá a [CAF en Microsoft Learn](https://learn.microsoft.com/azure/cloud-adoption-framework/). Volvé acá cuando ya tengas el contexto base.

Leela si:
- Vas a **diseñar una Landing Zone desde cero** (greenfield enterprise)
- Te toca **refactorizar una adopción Azure ya en curso** que se desvió
- Sos **consultor** y necesitás un marco común con el cliente
- Tu CTO/CFO pregunta "¿por qué tardamos tanto en deployar nada?"
- Vas a **defender el costo** de una Landing Zone proper vs "deployamos y vemos"

Si solo querés ver los pasos high-level de CAF, [andá a la versión corta](/learn/herramientas/aznamer) (placeholder — el article corto de AzCAF está en el roadmap).

## Por qué CAF existe

CAF nace de un patrón que Microsoft vio repetirse en cada cliente enterprise:

1. **Adoptan Azure rápido** — un equipo de devs empieza con un par de PoCs
2. **3-6 meses después** ya hay 12 subscriptions, 0 governance, naming caótico
3. **Año 1** el equipo de security se entera, hay panic mode
4. **Año 2** se contrata una consultora para "ordenar la casa"
5. **Año 3** todavía hay deuda técnica de la fase 1

CAF intenta evitar todo esto **antes** de empezar. La premisa: invertí 4-8 semanas al inicio en governance, y ahorrá los 12 meses de remediation después.

¿Funciona? Sí, cuando se sigue. La mayoría de los equipos lo skipean y después se arrepienten.

## Los 7 pilares de CAF (no son los WAF)

**No confundir con Well-Architected Framework.** WAF son 5 pilares de diseño técnico (reliability, security, cost, ops, performance). CAF son 7 pilares de **proceso de adopción**.

```text
Strategy    →  ¿Por qué cloud? Metas de negocio
Plan        →  Skills, change management, timeline
Ready       →  Landing Zones, naming, governance baseline
Migrate     →  Lift-and-shift, rehost, refactor
Innovate    →  Cloud-native, AI/ML, modernización
Govern      →  Policies, RBAC, compliance continuo
Manage      →  Operations, monitoring, lifecycle
Secure      →  Zero trust, threat protection
```

AzCAF (la tool) se concentra en **Ready** + **Govern** porque ahí está el 80% del trabajo evitable. Los otros pilares son procesos organizacionales, no toolable directamente.

## El concepto central: Landing Zone

Una **Landing Zone** es un entorno Azure pre-configurado al que tus workloads pueden "aterrizar" con todo el plumbing ya hecho. Networking, identity, governance, monitoring — todo armado antes de que un dev escriba el primer `terraform apply`.

### Antes vs después de tener Landing Zone

| Sin Landing Zone | Con Landing Zone |
|---|---|
| Dev crea subscription nueva por su cuenta | Subscription provisioned desde IaC |
| Naming improvisado por workload | Convención CAF aplicada por policy |
| Networking parchado workload por workload | Hub central + spokes consistentes |
| Audits trimestrales que detectan cosas tarde | Policies que previenen el desvío en deploy time |
| Cada workload re-inventa el monitoring | Log Analytics centralizado |

### Anatomía típica

```text
Management Group: Tenant Root
  │
  ├─ Platform MG ──────────────────── Servicios compartidos
  │   ├─ Identity sub                 Entra Connect, AD DS, PIM
  │   ├─ Management sub               Log Analytics, Sentinel, Azure Monitor
  │   └─ Connectivity sub             Hub VNet, Firewall, ExpressRoute
  │
  ├─ Landing Zones MG ─────────────── Workloads productivos
  │   ├─ Corp LZ                      Workloads internos
  │   │   ├─ Sub: corp-prod
  │   │   ├─ Sub: corp-dev
  │   │   └─ Sub: corp-shared
  │   └─ Online LZ                    Workloads expuestos a internet
  │       ├─ Sub: online-prod
  │       ├─ Sub: online-dev
  │       └─ Sub: online-shared
  │
  ├─ Sandbox MG ──────────────────── Experimentación
  │   └─ Sandbox subs                 1 por team, TTL 30 días
  │
  └─ Decommissioned MG ─────────────── Workloads en sunset
      └─ Subs marcadas para borrado
```

Esto es **enterprise-scale landing zone** (la versión "completa" de CAF). Existen versiones más simples para empresas chicas — ver "Adoption sizing" más abajo.

### Componentes mínimos de una LZ

```yaml
Networking:
  - Hub VNet (con Firewall + DNS Resolver)
  - Spoke VNets por workload con peering
  - VPN / ExpressRoute para conexión onprem
  - Network Security Groups por subnet

Identity:
  - Entra ID Connect (sync con AD onprem si existe)
  - PIM (Privileged Identity Management) para roles privilegiados
  - Conditional Access policies baseline
  - Application registrations governance

Governance:
  - Naming convention (CAF) deployada via Policy
  - Tagging policy obligatoria (CostCenter, Owner, Environment, DataClassification)
  - Allowed locations policy (limitar a tus regiones aprobadas)
  - Allowed VM SKUs policy (evitar VMs gigantes por error)

Monitoring:
  - Log Analytics workspace centralizado
  - Azure Monitor + Application Insights baseline
  - Sentinel (SIEM) si vas a hacer SecOps
  - Diagnostic settings policy (forzar logs a Log Analytics)

Security:
  - Microsoft Defender for Cloud habilitado
  - Key Vault central para secrets compartidos
  - Just-in-Time VM access policy
  - Network watcher en todas las regiones
```

Una LZ proper tiene 50-80 recursos antes que un solo workload aterrice. Si parece mucho, **lo es**. Pero después cada workload nuevo es ~10 recursos en vez de 60.

## Adoption sizing — ¿cuán completa una LZ necesitás?

CAF tiene "enterprise-scale" como el ideal. La realidad es que no todas las empresas necesitan eso.

### Tier 1 — Solo o team chico (<5 personas)

**No necesitás Landing Zone formal.** Tenés:
- 1 tenant Entra
- 1-2 subscriptions (dev + prod)
- Resource groups por workload
- Naming convention en un README

Lo que **sí** vale invertir:
- Naming convention (AzNamer)
- Tagging obligatorio (CostCenter, Owner)
- 1 Key Vault central

Tiempo total de setup: **2-3 días**.

### Tier 2 — Equipo mediano (10-50 personas, 1 empresa)

**LZ simplificada, 3-tier:**

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

Componentes:
- Hub VNet básico (sin Firewall, NSGs alcanza)
- Log Analytics centralizado
- Policies de naming + tagging obligatorias
- Defender for Cloud (Free tier)

Tiempo total de setup: **2-3 semanas**.

### Tier 3 — Enterprise (50+ personas, multi-team)

**Enterprise-scale landing zone completa** como el diagrama de arriba.

Componentes:
- Hub VNet con Azure Firewall + DDoS Protection
- VPN / ExpressRoute para onprem
- Entra ID Connect + PIM + Conditional Access avanzado
- Sentinel para SecOps continuo
- Defender Standard + custom policies
- Multi-región (DR site)

Tiempo total de setup: **2-4 meses** con equipo dedicado.

### Tier 4 — Multi-empresa / consultora MSP

**Tenant per cliente + tu propio tenant management.**

Cada cliente tiene su LZ Tier 2 o 3 propia. Vos como MSP:
- Workspace en Azure Hub por cliente
- Connect Tenant a cada uno (tier Enterprise $149)
- Audits scheduled diarios/semanales con delta detection
- Reportes consolidados

Tiempo: depende del cliente. Tu setup interno es 1 semana, después cada onboarding de cliente nuevo es 1-2 semanas.

## Implementación step-by-step (Tier 2 como ejemplo)

Esto es lo que armás en ~2 semanas para una empresa mediana. Lo descomponemos en sprints.

### Sprint 1 — Foundation (semana 1)

**Día 1: Decisiones de arquitectura**
- Tenant naming: `<empresa>.onmicrosoft.com` ya existe, ok
- Subscription strategy: 4 subs (mgmt, prod, dev, shared)
- Regiones aprobadas: `eus` (primary) + `weu` (DR)
- Naming convention: CAF estricto (ver [AzNamer deep dive](/learn/herramientas/aznamer-deep-dive))

**Día 2-3: Management Groups**
```bash
az account management-group create --name "Platform"     --parent <root-id>
az account management-group create --name "Workloads"    --parent <root-id>
az account management-group create --name "Sandbox"      --parent <root-id>
```

**Día 4-5: Subscriptions**
- Mover las subs existentes a sus MGs correspondientes
- Si arrancás greenfield, crear via EA portal o Enterprise Agreement

### Sprint 2 — Networking (semana 1-2)

**Día 6-7: Hub VNet en Platform MG**
- `vnet-hub-prod-eus-001` con CIDR `10.0.0.0/16`
- Subnets: `GatewaySubnet`, `AzureFirewallSubnet`, `snet-shared-services`
- Sin Firewall todavía si tu budget es chico — NSGs alcanzan en Tier 2

**Día 8-9: Spoke VNets**
- Por cada workload sub: 1 spoke VNet con peering al hub
- CIDR no-overlapping (usá AzNet planner)

**Día 10: DNS**
- Private DNS Resolver en el hub (no Azure Firewall DNS si no tenés Firewall)
- Forwarder a tu onprem DNS si aplica

### Sprint 3 — Governance + Monitoring (semana 2)

**Día 11-12: Policies**
- Naming convention policy (audit mode primero, deny en 2 semanas)
- Tagging obligatorio: `CostCenter`, `Owner`, `Environment`, `Project`
- Allowed locations: solo `eus` y `weu`
- Allowed VM SKUs: bloquear `Standard_M*` y otros monstruos

**Día 13: Log Analytics + diagnostic settings**
- 1 workspace en management sub: `log-mgmt-prod-eus-001`
- Diagnostic settings policy: todo recurso debe enviar logs a este workspace
- Retention: 90 días (más es PRO)

**Día 14: Defender for Cloud**
- Habilitar en todas las subs (Free tier para empezar)
- Standard tier solo en prod si budget alcanza

**Día 15: Documentación**
- Diagrama de la LZ en AzDraw
- Naming convention en README del repo de IaC
- Runbook de onboarding workload nuevo

## Cómo NO arruinar la LZ después de tenerla

Tener la LZ es 30% del trabajo. Mantenerla es 70%. Los anti-patterns más comunes:

### Anti-pattern 1: "Vamos a deployar acá nomás, después lo movemos"

**Síntoma**: Un workload nuevo se deploya en la sandbox sub o en una sub vieja, "para ir avanzando".

**Por qué pasa**: Falta de onboarding process formal para workloads nuevos.

**Fix**: Documentá un runbook claro:
1. Decidí qué LZ corresponde (Corp vs Online)
2. Pedí provisioning de sub nueva o usá existente
3. Aplicá baseline IaC (network peering, tags, RBAC inicial)
4. Recién entonces deployás el workload

### Anti-pattern 2: "El Firewall es muy caro, lo skipeamos"

**Síntoma**: El hub no tiene Azure Firewall. Cada spoke tiene NSGs propios pero el tráfico east-west está abierto.

**Cuándo está OK**: Tier 1-2 sin compliance estricta.

**Cuándo no**: Cualquier compliance regulada (PCI, HIPAA, SOC2). Firewall ($1000+/mes) sale más barato que un breach.

### Anti-pattern 3: "RBAC granular después"

**Síntoma**: Owner role en producción para 20 personas. "Después lo limitamos a Contributor".

**Por qué pasa**: PIM (Privileged Identity Management) parece complejo al inicio.

**Fix**: Desde día 1:
- Production = NADIE tiene Owner permanente. PIM elevation para tareas críticas.
- Contributor solo en dev/sandbox
- Reader es el default para todos

### Anti-pattern 4: "Sandbox sin TTL"

**Síntoma**: Tenés 47 sandbox subscriptions, 15 con recursos sin tocar hace 8 meses, ninguna sabés de quién es.

**Fix**: Policy que enforce tag `caf:ttl-days=30` en todo recurso de sandbox. Automation Account que borra recursos que pasan ese TTL.

### Anti-pattern 5: "Naming convention que es solo un README"

**Síntoma**: Tenés la convención documentada pero los devs siguen creando `vm-test-2-final`.

**Fix**: Policy con effect `deny` que match el regex de tu convención. Audit primero (1-2 semanas, alertas), después deny. El equipo se queja la primera semana. Es esperado y necesario.

### Anti-pattern 6: "Monitoring opcional"

**Síntoma**: Log Analytics está pero los workloads no envían logs porque "es caro".

**Fix**: Policy obligatoria de diagnostic settings → Log Analytics. Si el costo asusta, ajustá retention a 30 días en dev y 90 en prod. Pero los logs **deben** estar.

## Audit continuo — cómo saber si tu LZ se mantiene

Una LZ que se arma y nunca se audita **se descompone en 3-6 meses**. Anti-pattern recurrente.

### Métricas a trackear

| Métrica | Target | Cómo medir |
|---|---|---|
| **Compliance rate** (naming) | >90% | [AzNamer Audit](/learn/herramientas/aznamer-deep-dive) |
| **Tag coverage** | 100% en tags obligatorios | Azure Policy compliance dashboard |
| **Subs sin owner tag** | 0 | Custom Policy + dashboard |
| **Recursos en regiones no-aprobadas** | 0 | Allowed locations policy |
| **VMs con Public IP directa** | 0 (deberían ir por Load Balancer / Firewall) | Custom Policy |
| **Recursos sin diagnostic settings** | 0 | Diagnostic settings policy |
| **Sandbox subs >30 días sin tag refresh** | 0 | Custom Automation |

### Cadencia recomendada

- **Diario**: Defender for Cloud alerts (automático)
- **Semanal**: Policy compliance dashboard review (10 min)
- **Mensual**: AzNamer Audit + AzReview update (1 hora)
- **Trimestral**: LZ diagram review + roadmap update (medio día)

### Cómo AzCAF integra con audits

AzCAF (la tool) hoy te ayuda a:
1. Generar la convención de naming → exportar para AzNamer + AzPolicy
2. Diseñar la jerarquía de Management Groups
3. Definir los policies baseline
4. Generar diagramas de la LZ propuesta (handoff a AzDraw)

En roadmap (Q3 2026):
- Generar Bicep templates de la LZ completa
- Audit en vivo: connect tenant + comparar contra el blueprint que diseñaste
- Detección de drift entre lo diseñado y lo deployado

## Casos de uso

### Caso 1: Startup que crece a enterprise

**Situación**: Startup 18 meses old, pasaron de 5 a 50 empleados, Azure se les hizo caos.

**Workflow**:
1. Mes 1: Inventario actual + decisión Tier 2 vs Tier 3
2. Mes 2: Sprint 1-2 (Foundation + Networking) en paralelo con prod actual
3. Mes 3: Sprint 3 (Governance) + plan de migración workload por workload
4. Mes 4-6: Migración workloads (1-2 por sprint) hacia LZ proper
5. Mes 7+: Solo workloads nuevos en LZ, legacy en sunset path

**Resultado esperado**: 12 meses después, 90% de workloads en LZ proper, 10% legacy con plan de migración.

### Caso 2: Multinacional con M&A

**Situación**: Comprás una empresa, herés su Azure tenant. Tenés que integrarlo al tuyo.

**Workflow**:
1. **No mezcles tenants**. Mantené el de la adquirida un tiempo.
2. Audit completo del tenant adquirido (AzNamer, AzPolicy, AzRBAC reviews)
3. Mapeá su LZ (o falta de ella) contra la tuya
4. Plan de integración por workload, no por sub
5. Migración gradual a tu LZ + decommission progresivo

**Tiempo**: 6-18 meses dependiendo del tamaño.

### Caso 3: Consultor armando una LZ para cliente

**Situación**: Cliente nuevo te pide "armanos governance en Azure". Engagement de 6 semanas.

**Workflow**:
1. Semana 1: Discovery + decisión de tier (Tier 2 alcanza para el 90%)
2. Semana 2-3: Sprint 1-2 (Foundation + Networking)
3. Semana 4: Sprint 3 (Governance + Monitoring)
4. Semana 5: Workshop con su equipo, transfer de knowledge
5. Semana 6: Audit baseline + roadmap de 12 meses

**Pricing tier**: Team $29/seat (mín 2 = $58/mes) te alcanza si es un cliente. Enterprise $149/mes si manejás 3+.

## Cuándo NO usar CAF

CAF no es una bala de plata. Casos legítimos para no seguirlo:

1. **Sos solo o team <5** — Tier 1 es suficiente. CAF formal es overkill.
2. **Workloads ephemeral / experimentales** — Sandbox sub con TTL es mejor que LZ proper.
3. **Multi-cloud predominante** — Si Azure es el 30% de tu footprint, una convención multi-cloud propia tiene más sentido.
4. **Heredás una LZ existente diferente** — Si llegás a una empresa con governance propio que funciona, no rompas eso porque CAF dice otra cosa.
5. **Restricciones regulatorias específicas** — Algunos reguladores tienen su propio framework. Sumate al de ellos, no al de Microsoft.

CAF es lo que recomendaría a un cliente Tier 2-3 nuevo. Para los otros casos hay variantes.

## Roadmap honesto (de AzCAF la tool)

**Q3 2026:**
- Templates Bicep generables del Tier 2 completo (LZ ready-to-deploy)
- Integración con AzPolicy (export policies de naming + tagging)
- Diagrama exportable a AzDraw

**Q4 2026:**
- Connect Tenant + audit de drift entre LZ diseñada y deployada
- Tier 3 templates (enterprise-scale completo)

**Q1 2027:**
- Bicep + Terraform output paralelo
- Multi-tenant LZ design para MSPs (Enterprise tier)

**No prometido (todavía):**
- LZ design via natural language ("ayudame a armar una LZ para fintech regulada")
- Migration tooling automático workload-by-workload

## Llevatelo a tu IA

```markdown
# CAF Landing Zone — adoption playbook

## Decision: ¿qué tier?
Solo / <5 ppl:        Tier 1 (no LZ formal, naming + tags)
Equipo mediano:       Tier 2 (LZ simplificada 3-MG)
Enterprise 50+ ppl:   Tier 3 (enterprise-scale completa)
MSP / multi-cliente:  Tier 4 (tenant per cliente)

## Sprint plan Tier 2 (2 semanas)
1. Foundation (MGs + Subs strategy)
2. Networking (Hub-Spoke + DNS)
3. Governance (Policies + Monitoring + Defender)

## Anti-patterns
- "Después lo movemos" para workloads nuevos
- Firewall opcional en prod regulada
- Owner role permanente en prod
- Sandbox sin TTL
- Naming convention solo en README (no en Policy)
- Monitoring opcional

## Audit cadence
Diario:     Defender alerts (auto)
Semanal:    Policy compliance dashboard
Mensual:    AzNamer + AzReview update
Trimestral: LZ diagram + roadmap update

## Componentes mínimos por capa
Networking:  Hub VNet, peering, NSGs
Identity:    Entra Connect, PIM, Conditional Access
Governance:  Naming Policy, Tag Policy, Allowed Locations
Monitoring:  Log Analytics central, Diagnostic Settings
Security:    Defender, Key Vault central
```

## Fuentes oficiales y referencias

- **[CAF — Cloud Adoption Framework](https://learn.microsoft.com/azure/cloud-adoption-framework/)** — la fuente canónica
- **[CAF — Landing Zones](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/)** — concepto de LZ end-to-end
- **[Enterprise-scale Landing Zone](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/enterprise-scale/)** — la versión Tier 3 completa
- **[Management Groups](https://learn.microsoft.com/azure/governance/management-groups/overview)** — la jerarquía MG en detalle

### Articles relacionados en esta KB

- [AzNamer · deep dive](/learn/herramientas/aznamer-deep-dive) — la convención de naming que aplica en toda la LZ
- [AzDraw · deep dive](/learn/herramientas/azdraw-deep-dive) — para diagramar la LZ visualmente
- [Workspaces](/learn/conceptos/workspaces) — cómo separar diseños de LZ por cliente / proyecto
- [Connect Tenant security](/learn/connect-tenant/security-faq) — qué permisos pide el audit de la LZ deployada
