---
title: AzPolicy — Policy & Initiative Builder
description: Diseñá Azure Policies e initiatives y comparalas con producción.
category: herramientas
slug: azpolicy
lang: es
order: 100
readMinutes: 4
tags: [Govern, PRO]
lastUpdated: 2026-05-23
sources:
  - https://learn.microsoft.com/azure/governance/policy/overview
---

> 🔒 Requiere PRO — drift detection y scheduled audits dependen del tenant conectado.

## AzPolicy

AzPolicy es el builder visual de Azure Policies e initiatives. Diseñás el set de policies de tu org, lo comparás con lo que está deployado en el tenant para detectar drift, y configurás scheduled audits con email alerts (Enterprise).

## Para qué sirve

- Diseñar policies e initiatives sin pelearte con JSON
- Comparar el design vs lo deployado en producción (drift detection)
- Detectar policies "rogue" (deployadas pero no en el design)
- Schedule audits diarios/semanales con alerts por email (Enterprise)

## Cómo empezar

1. **Paso 1**: Abrí AzPolicy y armá el design (definitions + assignments).
2. **Paso 2**: Si tenés tenant conectado, corré "Compare deployed" para ver el diff.
3. **Paso 3**: (Enterprise) configurá schedule + emails para drift alerts automáticas.

## Casos de uso comunes

- **Governance baseline**: diseñás el set CAF + custom y lo trackás contra prod
- **Audit prep**: antes de auditoría, validás que no haya policies fuera de design
- **Multi-tenant**: con Enterprise, comparás varios tenants contra el mismo design

## Tips de uso

- Empezá con un set chico (5-10 policies) — initiatives grandes son difíciles de operar
- Para enforcement duro usá `deny`; para visibilidad inicial usá `audit`
- AzPolicy Compare (Enterprise) es la herramienta que te dice "alguien deployó algo a mano"

## FAQ

**¿Reemplaza el portal de Azure Policy?**
No — el Hub te ayuda a diseñar, comparar y trackear. El apply lo hacés vía pipelines o portal.

**¿Detecta políticas creadas en el portal directamente?**
Sí, eso es exactamente "rogue policy" en el compare.

## Recursos relacionados

- [AzCAF — Cloud Adoption Framework](/AzCAF)
- [AzRBAC — RBAC Designer](/AzRBAC)
- [AzCompliance — Regulatory Mapper](/AzCompliance)
- [Azure Policy overview](https://learn.microsoft.com/azure/governance/policy/overview)
