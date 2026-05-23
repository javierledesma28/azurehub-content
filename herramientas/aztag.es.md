---
title: AzTag — Tagging Strategy Builder
description: Builder visual de tags con generación de Azure Policy para forzar compliance.
category: herramientas
slug: aztag
lang: es
order: 60
readMinutes: 4
tags: [Govern, Free forever]
lastUpdated: 2026-05-23
sources:
  - https://learn.microsoft.com/azure/azure-resource-manager/management/tag-resources
  - https://learn.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/resource-tagging
---

## AzTag

AzTag es el builder visual para diseñar tu estrategia de tagging. Definís tags mandatory vs optional, allowed values, y AzTag te genera el Azure Policy que fuerza compliance. Export a Bicep, Terraform o CSV — la convención deja de vivir solo en un README.

## Para qué sirve

- Diseñar una estrategia de tagging coherente alineada a CAF
- Distinguir tags obligatorios (CostCenter, Owner, Env) de opcionales (Project, Phase)
- Generar la Azure Policy "require tag" automáticamente
- Exportar la convención lista para deploy

## Cómo empezar

1. **Paso 1**: Abrí AzTag y elegí el preset CAF o partí de cero.
2. **Paso 2**: Definí cada tag: nombre, mandatory/optional, allowed values, scope (subscription/RG).
3. **Paso 3**: Exportá Bicep/Terraform/CSV o copiá la policy directo a Cloud Shell.

## Casos de uso comunes

- **Greenfield**: arrancás un tenant nuevo y querés tagging desde el día 1
- **Audit cleanup**: detectás recursos sin owner ni cost center y necesitás cerrar el gap
- **FinOps**: necesitás tags consistentes para que Cost Management agrupe bien

## Tips de uso

- Limitá los mandatory a 4-5 tags como mucho — más se rompe en la práctica
- "Inheritance from RG" es tu amigo: definí en RG y los hijos lo heredan
- Combiná con AzPolicy para enforcement real (deny si falta el tag)

## FAQ

**¿Las policies generadas son deny o audit?**
Por default audit. Podés cambiar a deny en el export si querés enforcement duro.

**¿Soporta tags con valores dinámicos?**
Sí, vía Azure Policy con `modify` effect.

## Recursos relacionados

- [AzNamer — Naming conventions](/AzNamer)
- [AzPolicy — Policy Builder](/AzPolicy)
- [CAF Resource Tagging](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/resource-tagging)
