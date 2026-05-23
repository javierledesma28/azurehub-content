---
title: AzCatalog — Catálogo completo de recursos Azure
description: Búsqueda instantánea de cualquier recurso Azure con abreviación oficial, AVM y links a docs.
category: herramientas
slug: azcatalog
lang: es
order: 50
readMinutes: 3
tags: [Reference, Free forever]
lastUpdated: 2026-05-23
sources:
  - https://learn.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/resource-abbreviations
  - https://azure.github.io/Azure-Verified-Modules/
---

## AzCatalog

AzCatalog es el catálogo searchable de recursos Azure: 500+ tipos con su abreviación oficial CAF, el módulo AVM verificado (si existe) y links directos a la documentación oficial. Pensado para responder "¿cómo se abrevia esto?" en 3 segundos en vez de 3 pestañas.

## Para qué sirve

- Encontrar la abreviación oficial CAF de cualquier resource type
- Confirmar si existe un Azure Verified Module (AVM) para ese recurso
- Saltar al doc oficial sin perder el contexto
- Copiar el resource provider namespace para ARM/Bicep

## Cómo empezar

1. **Paso 1**: Abrí AzCatalog desde el Hub.
2. **Paso 2**: Tipeá el nombre del recurso (en inglés: "key vault", "front door", "kubernetes").
3. **Paso 3**: Click en la fila para ver detalle: abreviación, namespace, AVM, link a docs.

## Casos de uso comunes

- **Naming**: estás armando un nombre y necesitás la abreviación oficial (`kv`, `afd`, `aks`)
- **Compras tooling**: validar si un recurso ya tiene AVM antes de escribir Bicep custom
- **Onboarding**: el ingeniero nuevo aprende los namespaces sin tener que abrir 12 pestañas

## Tips de uso

- La búsqueda es fuzzy — "vault" te trae Key Vault, Recovery Services Vault y Backup Vault
- Si el recurso tiene AVM, copialo desde AzModules en vez de escribirlo a mano

## FAQ

**¿De dónde sale la lista?**
Microsoft Cloud Adoption Framework + el index público de AVM. Lo refrescamos cada release.

**¿Puedo exportar el catálogo completo?**
Hoy no como CSV global, pero podés filtrarlo en la vista y copiar lo que necesites.

## Recursos relacionados

- [AzNamer — Naming conventions](/AzNamer)
- [AzModules — Azure Verified Modules](/AzModules)
- [CAF Resource Abbreviations](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/resource-abbreviations)
