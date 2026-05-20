---
title: AzNamer
description: Nombrá tus resources Azure como si tu sucesor te lo agradeciera.
category: herramientas
slug: aznamer
order: 30
readMinutes: 6
tags: [Govern, Free forever]
lastUpdated: 2026-05-20
sources:
  - https://learn.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/resource-naming
  - https://learn.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/resource-abbreviations
  - https://learn.microsoft.com/azure/azure-resource-manager/management/resource-name-rules
---

## Reflexión antes de seguir

¿Tu naming convention sobrevive a una rotación de equipo? Si el ingeniero que la definió no está hoy, ¿el siguiente sabe leer `wl01-prd-eu-aks-001` sin preguntarle a nadie?

Si la respuesta es "más o menos", esta guía es para vos.

## Para qué sirve

Nombrar recursos Azure es la convención que más equipos rompen y la que menos quieren documentar. Cada PoC se llena de `test-2`, `vm-borrar`, `prod-real-2-final`. Cuando llega la auditoría, los nombres son lo primero que se rompe.

AzNamer aplica la convención del **Cloud Adoption Framework** sobre tus inputs (workload, env, region, type, instance) y devuelve nombres válidos contra las *resource name rules* de Azure — incluyendo restricciones particulares (storage account 24 caracteres, lowercase, sin guiones).

**Problemas que probablemente tenés:**

- "Convención de pasillo" sin documentación
- Storage account max-length rompió tu patrón
- Multi-región sin abreviaciones alineadas
- Governance pidió uniformizar este trimestre

**Lo que vas a poder hacer:**

- Definir el patrón una sola vez
- Generar nombres válidos en segundos
- Detectar violations por tipo de recurso
- Exportar Bicep, Terraform o CSV

## Cómo funciona

Definís el patrón una sola vez en el workspace. AzNamer compone, valida y exporta:

```bash
# patrón base
{workload}-{env}-{region}-{type}-{instance}

# ejemplos generados
payments-prd-eus-aks-001
shared-dev-weu-vnet-001
finops-prd-neu-st-001       # storage · guiones removidos
```

## Decisión arquitectural

**AzNamer asume Azure. Vos no necesariamente tenés que asumirlo.**

| Camino | Cuándo conviene |
|---|---|
| **Cloud Azure** | Workloads con picos, autoscale, multi-región global, PaaS de alto valor (AKS, Cosmos, Synapse) |
| **Híbrido** | Workloads core onpremises + bursting o DR en cloud. Azure Local, Stack HCI, Arc |
| **Onpremises** | Tráfico predecible, latencia muy baja, data residency cerrada. ~1/5 del costo cloud |

La convención de naming aplica en los tres. La decisión arquitectural sigue siendo tuya.
Referencia: [CAF Hybrid Cloud Strategy](https://learn.microsoft.com/azure/cloud-adoption-framework/strategy/hybrid-cloud-strategy)

## Hack de campo

**No apliques la convención completa a recursos efímeros.**

POCs, sandboxes y experimentos cargan costo cognitivo si los nombrás como producción. Prefijo `tmp-` + timestamp UTC, más un Azure Policy con TTL que limpia recursos con esa tag después de N días. La convención larga es para lo que va a vivir.

Patrón sugerido: `tmp-{user}-{yyyymmdd}-{purpose}`

## Llevatelo a tu IA

Markdown agnóstico de cloud — pegalo en ChatGPT, Claude o Copilot. El Hub es punto de partida. La conversación sigue donde vos quieras.

```markdown
# Azure CAF Naming — quick ref

## Pattern
{workload}-{env}-{region}-{type}-{instance}

## Constraints (extracto)
- Storage account: max 24, lowercase, no hyphens
- Key Vault: 3-24 chars, alphanum + hyphens
- Resource Group: max 90

## Abbreviations
| Resource | abbr |
|---|---|
| Resource Group | rg |
| Virtual Network | vnet |
| Subnet | snet |
| AKS cluster | aks |
| Storage account | st |
| Key Vault | kv |
| App Service | app |

src: learn.microsoft.com/azure/cloud-adoption-framework/...
```

## Fuentes oficiales

Todo lo de esta página tiene respaldo en Microsoft Learn:

- **[CAF — Resource Naming Best Practices](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/resource-naming)** — la convención de referencia
- **[CAF — Resource Abbreviations](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/resource-abbreviations)** — abreviaciones por tipo de recurso
- **[Resource Naming Rules](https://learn.microsoft.com/azure/azure-resource-manager/management/resource-name-rules)** — reglas por servicio (caracteres permitidos, longitud)
