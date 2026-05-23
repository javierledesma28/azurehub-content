---
title: AzModules — Azure Verified Modules Explorer
description: Explorer de AVM oficiales con preview Bicep / Terraform y parámetros documentados.
category: herramientas
slug: azmodules
lang: es
order: 65
readMinutes: 3
tags: [IaC, Free forever]
lastUpdated: 2026-05-23
sources:
  - https://azure.github.io/Azure-Verified-Modules/
---

## AzModules

AzModules es el explorer de Azure Verified Modules (AVM) — los módulos IaC oficiales de Microsoft. Buscás el módulo, ves el código Bicep o Terraform en vivo, los parámetros documentados, y lo copiás listo para tu pipeline.

## Para qué sirve

- Encontrar el módulo AVM oficial para cualquier recurso Azure
- Ver el código Bicep o Terraform sin clonar el repo
- Entender los parámetros antes de instanciarlo
- Copy-paste sin abrir 5 pestañas

## Cómo empezar

1. **Paso 1**: Abrí AzModules y buscá el recurso (ej: "storage account", "key vault").
2. **Paso 2**: Elegí Bicep o Terraform.
3. **Paso 3**: Revisá parámetros y copiá el snippet.

## Casos de uso comunes

- **Pipeline nuevo**: arrancás un módulo Bicep y querés usar AVM en vez de escribirlo a mano
- **Validar versión**: chequear qué versión del AVM estás usando vs la última publicada
- **Comparar**: ver opciones default que recomienda Microsoft vs lo que tenés hoy

## Tips de uso

- Combiná con AzCatalog para ver primero qué recursos tienen AVM disponible
- Los AVM siguen semver — pineá la versión en producción

## FAQ

**¿Los módulos están actualizados?**
Sincronizamos contra el index oficial de AVM. Si Microsoft publica una versión nueva, aparece en el siguiente refresh.

**¿Por qué a veces no hay módulo para un recurso?**
No todos los recursos tienen AVM todavía. Si falta, AzCatalog te indica que hay que escribirlo custom.

## Recursos relacionados

- [AzCatalog — Resource catalog](/AzCatalog)
- [AzLanding — Landing Zone Studio](/AzLanding)
- [Azure Verified Modules](https://azure.github.io/Azure-Verified-Modules/)
