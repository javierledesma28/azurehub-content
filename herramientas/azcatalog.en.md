---
title: AzCatalog — Complete Azure resource catalog
description: Instant search for any Azure resource with official abbreviation, AVM link, and docs.
category: herramientas
slug: azcatalog
lang: en
order: 50
readMinutes: 3
tags: [Reference, Free forever]
lastUpdated: 2026-05-23
sources:
  - https://learn.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/resource-abbreviations
  - https://azure.github.io/Azure-Verified-Modules/
---

## AzCatalog

AzCatalog is the searchable Azure resource catalog: 500+ types with their official CAF abbreviation, the verified AVM module (when one exists), and direct links to the official docs. Built to answer "how is this abbreviated?" in 3 seconds instead of 3 tabs.

## What it's for

- Find the official CAF abbreviation for any resource type
- Confirm whether an Azure Verified Module (AVM) exists for that resource
- Jump to the official doc without losing context
- Copy the resource provider namespace for ARM/Bicep

## Getting started

1. **Step 1**: Open AzCatalog from the Hub.
2. **Step 2**: Type the resource name ("key vault", "front door", "kubernetes").
3. **Step 3**: Click the row to see detail: abbreviation, namespace, AVM, docs link.

## Common use cases

- **Naming**: you're building a name and need the official abbreviation (`kv`, `afd`, `aks`)
- **Tooling decisions**: validate whether a resource already has an AVM before writing custom Bicep
- **Onboarding**: new engineer learns namespaces without opening 12 tabs

## Tips

- Search is fuzzy — "vault" returns Key Vault, Recovery Services Vault, and Backup Vault
- If the resource has an AVM, grab it from AzModules instead of writing it by hand

## FAQ

**Where does the list come from?**
Microsoft Cloud Adoption Framework + the public AVM index. We refresh it each release.

**Can I export the full catalog?**
Not as a global CSV today, but you can filter the view and copy what you need.

## Related

- [AzNamer — Naming conventions](/AzNamer)
- [AzModules — Azure Verified Modules](/AzModules)
- [CAF Resource Abbreviations](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/resource-abbreviations)
