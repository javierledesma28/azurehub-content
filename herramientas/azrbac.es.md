---
title: AzRBAC — RBAC Role Designer
description: Roles RBAC custom con least-privilege scoring y comparativa contra built-in.
category: herramientas
slug: azrbac
lang: es
order: 110
readMinutes: 4
tags: [Security, PRO]
lastUpdated: 2026-05-23
sources:
  - https://learn.microsoft.com/azure/role-based-access-control/overview
  - https://learn.microsoft.com/azure/role-based-access-control/best-practices
---

> 🔒 Requiere PRO — least-privilege scoring contra tu tenant.

## AzRBAC

AzRBAC es el designer de roles custom RBAC. Te muestra el least-privilege score de cada rol que diseñás, lo compara contra los built-in equivalents, y detecta assignments sobre-privilegiados en tu tenant.

## Para qué sirve

- Diseñar roles custom RBAC con scoring de least-privilege
- Comparar tu rol custom contra built-in roles equivalentes
- Detectar assignments con Owner / Contributor donde alcanzaría algo menor
- Exportar a ARM/Bicep listo para deploy

## Cómo empezar

1. **Paso 1**: Abrí AzRBAC y elegí "New custom role" o partí de un built-in.
2. **Paso 2**: Definí actions, notActions, dataActions y scope.
3. **Paso 3**: Revisá el least-privilege score y exportá ARM/Bicep.

## Casos de uso comunes

- **Operador de FinOps**: necesita ver costos pero no tocar recursos — built-in no encaja, custom sí
- **Audit cleanup**: detectar a quién le diste Owner cuando alcanzaba Reader + Cost Management
- **Pre-prod cleanup**: validar que nadie tiene Contributor en producción

## Tips de uso

- Antes de armar un custom, mirá si un built-in alcanza — son menos costo operativo
- notActions es tu amigo para "Contributor minus X"
- Cuanto más amplio el scope, más alto el riesgo — preferí RG scope sobre subscription

## FAQ

**¿Detecta Owner en producción automáticamente?**
Sí, los assignments overprivileged aparecen en el scan del tenant.

**¿Soporta PIM?**
Hoy no integramos con PIM directamente, pero el output es compatible.

## Recursos relacionados

- [AzPolicy — Policy Builder](/AzPolicy)
- [AzZeroTrust — Zero Trust Assessment](/AzZeroTrust)
- [Azure RBAC overview](https://learn.microsoft.com/azure/role-based-access-control/overview)
- [RBAC best practices](https://learn.microsoft.com/azure/role-based-access-control/best-practices)
