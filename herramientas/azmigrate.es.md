---
title: AzMigrate — Migration Assessment & Planner
description: Migration plan que sale en el primer intento — 6 Rs, wave planning, cost projection.
category: herramientas
slug: azmigrate
lang: es
order: 130
readMinutes: 4
tags: [Migration, PRO]
lastUpdated: 2026-05-23
sources:
  - https://learn.microsoft.com/azure/cloud-adoption-framework/migrate/
  - https://learn.microsoft.com/azure/migrate/
---

> 🔒 Requiere PRO — el assessment de migración y wave planning son PRO.

## AzMigrate

AzMigrate es el assessment de migración a Azure: clasificás cada workload bajo el framework de 6 Rs (Rehost, Replatform, Refactor, Rebuild, Repurchase, Retire), armás el wave plan, mapeás dependencias y proyectás costos.

## Para qué sirve

- Clasificar workloads bajo las 6 Rs
- Wave planning (qué se migra primero, qué depende de qué)
- Dependency mapping entre workloads
- Cost projection del estado target en Azure

## Cómo empezar

1. **Paso 1**: Abrí AzMigrate y cargá el inventario (manual, CSV o import desde Azure Migrate).
2. **Paso 2**: Clasificá cada workload bajo las 6 Rs.
3. **Paso 3**: Armá waves, mapeá dependencias, generá la proyección de costos.

## Casos de uso comunes

- **Migration kickoff**: armás el plan que se presenta al sponsor ejecutivo
- **Re-plan**: la migración se trabó y necesitás re-priorizar
- **Cost justification**: cuánto cuesta la migración + cuánto costará operar en Azure

## Tips de uso

- "Rehost" es la opción más rápida pero la que menos valor cloud entrega — balanceá
- El wave planning depende del mapping de dependencias — invertí ahí
- Combiná con AzCost para validar la proyección target
- "Retire" suele ser la R más rentable y la más olvidada

## FAQ

**¿Reemplaza a Azure Migrate?**
No — Azure Migrate es la herramienta de discovery + replication de Microsoft. AzMigrate es el planner que se sienta arriba.

**¿Cubre on-prem → Azure?**
Sí, ese es el caso principal. También cubre cloud-to-cloud y datacenter consolidation.

## Recursos relacionados

- [AzLanding — Landing Zone Studio](/AzLanding)
- [AzCost — Pricing calculator](/AzCost)
- [CAF Migrate](https://learn.microsoft.com/azure/cloud-adoption-framework/migrate/)
- [Azure Migrate (oficial)](https://learn.microsoft.com/azure/migrate/)
