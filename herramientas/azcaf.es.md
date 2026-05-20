---
title: AzCAF
description: Cloud Adoption Framework aplicado de verdad — Landing Zones sin quemar 6 meses.
category: herramientas
slug: azcaf
order: 70
readMinutes: 6
tags: [Foundation, Free with signup]
lastUpdated: 2026-05-20
author: javier
sources:
  - https://learn.microsoft.com/azure/cloud-adoption-framework/
  - https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/
---

## Reflexión antes de seguir

El patrón que Microsoft vio repetirse en cada empresa enterprise: adoptan Azure rápido, 6 meses después tienen 12 subscriptions sin governance, año 2 contratan una consultora para "ordenar la casa", año 3 todavía hay deuda técnica del sprint inicial.

¿Vale la pena invertir 4-8 semanas al inicio en Landing Zone, o "deployamos y vemos"?

CAF asume que sí. Esta página te ayuda a decidirlo conscientemente.

## Para qué sirve

AzCAF es la guía Cloud Adoption Framework operacionalizada — diseñás tu jerarquía de Management Groups, naming convention, networking baseline y governance policies **antes** de que el primer workload aterrice.

**Problemas que probablemente tenés:**

- Subscriptions creadas ad-hoc por equipos distintos
- Sin Management Groups, todo cuelga del tenant root
- Networking parchado workload por workload, no hay hub central
- Cada equipo inventa su propia convención de naming
- "Después armamos governance"

**Lo que vas a poder hacer:**

- Definir la jerarquía MG → Sub → RG correcta para tu tamaño
- Generar un blueprint de Landing Zone exportable
- Mapear los componentes mínimos (networking, identity, governance, monitoring)
- Documentar la decisión antes de deployarla

## Cómo funciona

Tres elementos centrales:

```bash
1. Management Groups hierarchy  →  Platform / Workloads / Sandbox
2. Subscriptions strategy        →  por LZ (Corp, Online, etc.) o por env
3. Baseline components           →  Hub VNet, Log Analytics central,
                                    Policies de naming + tagging,
                                    Defender for Cloud
```

AzCAF te ayuda a decidir el **tamaño** de tu Landing Zone:

| Tier | Cuándo | Tiempo setup |
|---|---|---|
| **Tier 1** | Solo / <5 ppl, 1-2 subs | 2-3 días |
| **Tier 2** | Equipo mediano (10-50), 1 empresa | 2-3 semanas |
| **Tier 3** | Enterprise 50+ ppl, multi-team | 2-4 meses |
| **Tier 4** | MSP / consultora multi-cliente | varía |

## Decisión arquitectural

**¿Cuándo NO seguir CAF al pie de la letra?**

| Situación | Recomendación |
|---|---|
| Sos solo o team <5 personas | Tier 1 alcanza. CAF formal es overkill |
| Workloads ephemeral / experimentales | Sandbox sub con TTL > LZ proper |
| Multi-cloud predominante (Azure es 30%) | Convención propia agnóstica |
| Heredás governance existente que funciona | No lo rompas porque CAF es más nuevo |
| Restricciones regulatorias específicas | Sumate al framework del regulador |

CAF es **guía**, no **ley**. Lo que importa es que tu decisión esté **documentada y conscientemente tomada**, no defaulteada por desidia.

[Ver deep dive de AzCAF →](/learn/herramientas/azcaf-deep-dive)

## Hack de campo

**Empezá por la decisión de tier, no por el diagrama.**

El error más común: dibujar primero el VNet hub-spoke perfecto sin haber decidido si tu empresa necesita Tier 2 o Tier 3. Te pasás 2 semanas armando algo que después tenés que tirar.

Sentate 30 minutos. ¿Cuántas personas tocan Azure en tu empresa hoy? ¿En 12 meses? ¿Sos consultora multi-cliente o equipo interno? Esas 3 preguntas deciden el tier. El diagrama viene después.

## Llevatelo a tu IA

```markdown
# CAF Landing Zone — decision matrix

## Tier decision (en orden)
1. ¿Sos solo / team <5? -> Tier 1 (no LZ formal)
2. ¿Equipo mediano 10-50 ppl, 1 empresa? -> Tier 2
3. ¿Enterprise 50+ ppl, multi-team? -> Tier 3
4. ¿Consultora multi-cliente? -> Tier 4 (MSP)

## Componentes mínimos por tier
Tier 1: Naming + tags + 1 KV central
Tier 2: + Hub VNet básico + Log Analytics + Policies
Tier 3: + Azure Firewall + PIM + Sentinel + multi-region DR
Tier 4: + Tenant per cliente + audits scheduled

## Anti-patterns
- "Después armamos governance"
- Owner role permanente en producción
- Sandbox sin TTL
- Naming convention solo en un README (no en Policy)
- Monitoring opcional
```

## Fuentes oficiales

- **[CAF — Cloud Adoption Framework](https://learn.microsoft.com/azure/cloud-adoption-framework/)** — la fuente canónica
- **[CAF — Landing Zones](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/)** — concepto end-to-end
- **[AzCAF · Deep dive](/learn/herramientas/azcaf-deep-dive)** — la versión 18 min con sprints, anti-patterns, casos de uso
