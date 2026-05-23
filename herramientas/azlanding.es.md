---
title: AzLanding — Azure Landing Zone Studio
description: Studio para diseñar Landing Zones CAF-compliant con output Bicep / Terraform.
category: herramientas
slug: azlanding
lang: es
order: 75
readMinutes: 4
tags: [Foundation, Free with signup]
lastUpdated: 2026-05-23
sources:
  - https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/
  - https://learn.microsoft.com/azure/architecture/landing-zones/
---

## AzLanding

AzLanding es el studio para diseñar Landing Zones CAF Enterprise-Scale: management groups, subscriptions, policies, RBAC y networking baseline. La salida es Bicep o Terraform listo para deploy — no JSON suelto que después tenés que pegar a mano.

## Para qué sirve

- Diseñar la jerarquía MG → Sub → RG antes de aterrizar workloads
- Modelar el hub-and-spoke network baseline
- Asignar policies y RBAC a la jerarquía
- Exportar el blueprint como Bicep o Terraform

## Cómo empezar

1. **Paso 1**: Abrí AzLanding y elegí el tier (basic, intermediate, enterprise-scale).
2. **Paso 2**: Configurá MGs, subscriptions, hub VNet, policies y RBAC.
3. **Paso 3**: Validá pre-deploy y exportá Bicep/Terraform.

## Casos de uso comunes

- **Greenfield enterprise**: arrancás un tenant nuevo y necesitás la base CAF en semanas, no meses
- **Migración a enterprise-scale**: tu tenant creció ad-hoc y querés un blueprint para reorganizar
- **Consultora**: replicás el mismo baseline para varios clientes con variantes

## Tips de uso

- Empezá por la decisión de tier (ver AzCAF) antes de tocar AzLanding
- No metas todos los policies del catálogo CAF — empezá con el set mínimo y crecé
- Validá el output con `bicep build` antes de pegarlo en pipelines

## FAQ

**¿Es lo mismo que el Enterprise-Scale portal accelerator?**
Cubre el mismo terreno, pero acá editás visualmente y exportás el código.

**¿Genera el deploy automáticamente?**
No, eso depende de tu pipeline. Te damos el código listo.

## Recursos relacionados

- [AzCAF — Cloud Adoption Framework](/AzCAF)
- [AzNet — Subnet planner](/AzNet)
- [AzModules — Azure Verified Modules](/AzModules)
- [CAF Landing Zones](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/)
