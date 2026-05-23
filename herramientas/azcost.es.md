---
title: AzCost — Pricing & FinOps Calculator
description: Calculadora con pricing oficial Microsoft, comparable por región y SKU.
category: herramientas
slug: azcost
lang: es
order: 90
readMinutes: 4
tags: [FinOps, Free with signup]
lastUpdated: 2026-05-23
sources:
  - https://azure.microsoft.com/pricing/calculator/
  - https://learn.microsoft.com/azure/cost-management-billing/
---

## AzCost

AzCost es la calculadora de pricing Azure con datos oficiales Microsoft, comparable por región, SKU y escenario (reservation / PAYG / spot). Pensada para cotizar workloads antes de prender el recurso, no para descubrir el costo en la factura.

## Para qué sirve

- Cotizar un workload con pricing oficial actualizado
- Comparar regiones (West Europe vs East US 2 vs North Europe)
- Modelar reservations 1y / 3y vs PAYG vs Spot
- Exportar la cotización detallada para el cliente

## Cómo empezar

1. **Paso 1**: Abrí AzCost y agregá los recursos del workload (VM, AKS, Storage, etc.).
2. **Paso 2**: Configurá región, SKU, horas/mes, y el modelo (PAYG / 1y RI / 3y RI / Spot).
3. **Paso 3**: Revisá el total mensual y exportá PDF/CSV.

## Casos de uso comunes

- **Cotización a cliente**: armar el TCO de un proyecto antes de cerrarlo
- **Comparativa de región**: validar si moverte de West Europe a North Europe vale la pena
- **What-if**: cuánto ahorrarías con reservations 3y vs PAYG en el AKS de prod

## Tips de uso

- Reservations solo valen si el workload es estable — Spot no aplica para producción crítica
- Los precios cambian: re-corré la cotización si el deal está parado >30 días
- Para análisis sobre lo que YA tenés, usá AzFinOps

## FAQ

**¿Tiene Dev/Test pricing?**
Sí, lo seleccionás como modelo separado.

**¿Soporta enterprise agreements?**
La calculadora muestra precios públicos. Tu descuento EA lo aplicás encima.

## Recursos relacionados

- [AzFinOps — Rightsizing advisor](/AzFinOps)
- [Azure Pricing Calculator (oficial)](https://azure.microsoft.com/pricing/calculator/)
- [Microsoft Cost Management](https://learn.microsoft.com/azure/cost-management-billing/)
