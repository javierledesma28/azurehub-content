---
title: AzTopo — Visualizá tu red Azure end-to-end
description: Topología visual de VNets, subnets, peerings, NSGs y ExpressRoute con auto-import del tenant.
category: herramientas
slug: aztopo
lang: es
order: 80
readMinutes: 4
tags: [Network, Soon]
lastUpdated: 2026-05-23
sources:
  - https://learn.microsoft.com/azure/virtual-network/network-overview
---

> ⚠️ Próximamente — todavía no disponible. Estamos terminando la integración con el import del tenant.

## AzTopo

AzTopo es el visualizador end-to-end de la red Azure: VNets, subnets, peerings, NSGs y ExpressRoute en un solo canvas. Se nutre del import live del tenant conectado y resalta overlaps o misconfigurations antes de que sean problema en producción.

## Para qué sirve

- Ver la topología real del tenant sin armar diagramas a mano
- Detectar overlaps de CIDR entre VNets que comparten peering
- Visualizar el flujo NSG entre subnets para entender qué pasa cuando algo deja de comunicarse
- Identificar VNets huérfanas o peerings unilaterales

## Cómo empezar

1. **Paso 1**: Conectá el tenant desde el menú Tenant Import (necesitás permisos Reader sobre las subscriptions).
2. **Paso 2**: Abrí AzTopo desde el Hub. Elegí el scope (MG, subscription o resource group).
3. **Paso 3**: AzTopo carga la topología y te muestra VNets como nodos, peerings como edges, NSGs aplicados como badges.

## Casos de uso comunes

- **Auditoría rápida**: revisar la red de un cliente nuevo en 5 minutos sin pedir un diagrama desactualizado
- **Detección de overlap**: validar que el nuevo /16 que vas a deployar no choque con un peering existente
- **Documentación viva**: snapshot exportable como respaldo de cómo está la red hoy

## Tips de uso

- Si trabajás con hub-and-spoke, agrupá el view por hub para entender la jerarquía de un vistazo
- Los peerings unilaterales (uno a uno conectado pero el otro no) suelen ser bugs de provisioning — AzTopo los marca con un warning

## FAQ

**¿Por qué dice "Próximamente"?**
La feature está en beta interna. El import del tenant ya funciona en AzDraw; estamos puliendo la vista network-only para que cargue rápido en tenants grandes.

**¿Va a soportar ExpressRoute y VPN Gateway?**
Sí, ambos en el primer release público.

## Recursos relacionados

- [AzNet — Subnet & IP Address Planner](/AzNet)
- [AzDraw — Architecture canvas](/AzDraw)
- [Azure Virtual Network overview](https://learn.microsoft.com/azure/virtual-network/network-overview)
