---
title: AzFinOps — Rightsizing & Reservations Advisor
description: Encontrá el dinero que estás dejando en la mesa con análisis live del tenant.
category: herramientas
slug: azfinops
lang: es
order: 95
readMinutes: 4
tags: [FinOps, PRO]
lastUpdated: 2026-05-23
sources:
  - https://learn.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices
  - https://learn.microsoft.com/azure/advisor/advisor-cost-recommendations
---

> 🔒 Requiere PRO — AzFinOps analiza tu tenant conectado en vivo.

## AzFinOps

AzFinOps es el advisor de FinOps que mira tu tenant en vivo y te dice dónde estás dejando dinero en la mesa: VMs sobre-provisionadas, reservations no aplicadas, recursos huérfanos. No te cobra una "auditoría FinOps" — te muestra las top opportunities en minutos.

## Para qué sirve

- Detectar VMs sobre-provisionadas con datos de uso reales
- Calcular ROI de pasar PAYG → Reservations 1y / 3y
- Identificar discos huérfanos, IPs sin asociar, recursos sin tag de owner
- Priorizar las top opportunities por ahorro mensual estimado

## Cómo empezar

1. **Paso 1**: Conectá el tenant (Reader + Cost Management Reader).
2. **Paso 2**: Abrí AzFinOps y elegí el scope.
3. **Paso 3**: Revisá las recomendaciones ordenadas por ahorro estimado mensual.

## Casos de uso comunes

- **Cleanup trimestral**: corrés AzFinOps cada Q y aplicás el top 10
- **Pre-renewal**: antes de comprar reservations, validás cuáles realmente aplican
- **Client quick-win**: arrancás un engagement nuevo mostrando 3 ahorros concretos

## Tips de uso

- Las recomendaciones de Microsoft Advisor son útiles pero genéricas — AzFinOps las prioriza por impacto real
- Antes de aceptar un downsize, mirá los picos de CPU/RAM del último mes, no solo el promedio
- Reservations 3y solo si confías que el workload sobrevive 3 años

## FAQ

**¿Mira métricas reales o solo SKU?**
Métricas reales (CPU, memoria, network) de los últimos 30 días vía Azure Monitor.

**¿Puedo aplicar las recomendaciones desde el Hub?**
No, te damos el comando az/Bicep listo, pero el apply lo hacés en tu pipeline.

## Recursos relacionados

- [AzCost — Pricing calculator](/AzCost)
- [Azure Advisor Cost Recommendations](https://learn.microsoft.com/azure/advisor/advisor-cost-recommendations)
- [Microsoft Cost Management best practices](https://learn.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices)
