---
title: AzModules — Azure Verified Modules Explorer
description: Explorer of official AVMs with live Bicep / Terraform preview and documented parameters.
category: herramientas
slug: azmodules
lang: en
order: 65
readMinutes: 3
tags: [IaC, Free forever]
lastUpdated: 2026-05-23
sources:
  - https://azure.github.io/Azure-Verified-Modules/
---

## AzModules

AzModules is the Azure Verified Modules (AVM) explorer — the official Microsoft IaC modules. Find a module, see the Bicep or Terraform code live, the documented parameters, and copy it ready for your pipeline.

## What it's for

- Find the official AVM for any Azure resource
- See Bicep or Terraform code without cloning the repo
- Understand parameters before instantiating
- Copy-paste without opening 5 tabs

## Getting started

1. **Step 1**: Open AzModules and search for the resource (e.g. "storage account", "key vault").
2. **Step 2**: Pick Bicep or Terraform.
3. **Step 3**: Review parameters and copy the snippet.

## Common use cases

- **New pipeline**: starting a Bicep module and want AVM instead of writing it by hand
- **Version check**: confirm which AVM version you're using vs the latest published
- **Compare**: see Microsoft's default options vs what you have today

## Tips

- Combine with AzCatalog to first see which resources have an AVM available
- AVMs follow semver — pin the version in production

## FAQ

**Are the modules up to date?**
We sync against the official AVM index. New publishes show up at next refresh.

**Why is there no module for some resources?**
Not every resource has an AVM yet. When missing, AzCatalog flags that you need to write it custom.

## Related

- [AzCatalog — Resource catalog](/AzCatalog)
- [AzLanding — Landing Zone Studio](/AzLanding)
- [Azure Verified Modules](https://azure.github.io/Azure-Verified-Modules/)
