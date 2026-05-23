---
title: AzNet — Subnet & IP Address Planner
description: Subnetting IPv4/IPv6 sin Excel — con overlap detection e integración a AzDraw.
category: herramientas
slug: aznet
lang: es
order: 85
readMinutes: 4
tags: [Network, Free with signup]
lastUpdated: 2026-05-23
sources:
  - https://learn.microsoft.com/azure/virtual-network/virtual-networks-overview
  - https://datatracker.ietf.org/doc/html/rfc4632
---

## AzNet

AzNet es el planner visual de subnets. Definís VNets, dividís en subnets, calculás overlap entre rangos, todo con soporte IPv4 e IPv6. Export a Bicep y al canvas de AzDraw — el plan deja de vivir en una hoja de Excel.

## Para qué sirve

- Diseñar el address space del workload o del hub
- Detectar overlaps entre VNets/subnets antes de deployar
- Recomendar tamaños según los recursos esperados (AKS, App Gateway, Bastion, etc.)
- Exportar Bicep + diagrama en AzDraw

## Cómo empezar

1. **Paso 1**: Abrí AzNet y definí la VNet base (ej: `10.50.0.0/16`).
2. **Paso 2**: Dividí en subnets — AzNet te recomienda tamaños según el tipo (AKS, AppGW, Bastion, etc.).
3. **Paso 3**: Validá overlaps con peerings existentes y exportá Bicep o al canvas.

## Casos de uso comunes

- **Nueva landing zone**: planeás el address space antes de crear el primer VNet
- **Expansión**: agregás un spoke nuevo y necesitás validar que no choque con los existentes
- **IPv6 dual-stack**: arrancás un workload con IPv6 nativo y querés validar el diseño

## Tips de uso

- Reservá rangos para servicios que crecen: AKS necesita más espacio del que parece
- Subnets de servicios gestionados tienen tamaños mínimos (Bastion /26, AppGW /24) — AzNet los respeta
- Documentá el plan antes de deployar — refactorear subnets es doloroso

## FAQ

**¿Soporta IPv6?**
Sí, IPv4 e IPv6 — incluyendo dual-stack.

**¿Detecta overlap con peerings?**
Sí, si tenés tenant conectado o si declarás los rangos del hub manualmente.

## Recursos relacionados

- [AzTopo — Network topology](/AzTopo)
- [AzLanding — Landing Zone Studio](/AzLanding)
- [Azure Virtual Network overview](https://learn.microsoft.com/azure/virtual-network/virtual-networks-overview)
