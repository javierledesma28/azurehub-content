---
title: AzResiliency — BCDR & Composite SLA Calculator
description: SLA compuesto + BCDR planning con detección de single points of failure.
category: herramientas
slug: azresiliency
lang: es
order: 125
readMinutes: 4
tags: [Reliability, PRO]
lastUpdated: 2026-05-23
sources:
  - https://learn.microsoft.com/azure/reliability/
  - https://learn.microsoft.com/azure/architecture/framework/resiliency/
---

> 🔒 Requiere PRO — el modelado de SLA compuesto y BCDR vive en PRO.

## AzResiliency

AzResiliency calcula el SLA compuesto de tu arquitectura (productorio de los componentes), modela BCDR con targets de RPO/RTO, y detecta single points of failure (SPOFs). Pensado para responder "¿cuál es el SLA real de este workload?" sin sacar la calculadora.

## Para qué sirve

- Calcular SLA compuesto multiplicando los SLAs de cada componente
- Detectar SPOFs (un componente sin redundancia que tira abajo todo)
- Modelar RPO/RTO targets vs lo que tu arquitectura realmente entrega
- Generar el plan BCDR documentado

## Cómo empezar

1. **Paso 1**: Abrí AzResiliency y agregá los componentes del workload (o importalos desde AzDraw).
2. **Paso 2**: Asigná SLA per-componente (Microsoft default o custom) y configuración de redundancia.
3. **Paso 3**: Revisá el SLA compuesto + lista de SPOFs + recomendaciones.

## Casos de uso comunes

- **Pre-prod review**: validás el SLA realista antes de prometérselo al cliente
- **BCDR planning**: documentás los targets RPO/RTO y validás que la arquitectura los cumple
- **SPOF cleanup**: detectás el storage account single-region en un workload supuestamente multi-region

## Tips de uso

- El SLA compuesto es PRODUCTORIO, no promedio — 99.99% × 99.95% × 99.9% = ~99.84%
- Zonas de disponibilidad suman SLA solo si el servicio las soporta nativamente
- BCDR sin testear no es BCDR — agendá el game day

## FAQ

**¿Usa los SLAs oficiales Microsoft?**
Sí, los SLAs por SKU vienen del SLA summary oficial. Podés overridarlos.

**¿Genera el runbook BCDR?**
Te genera la estructura. El detalle operativo lo completás vos.

## Recursos relacionados

- [AzReview — Well-Architected Review](/AzReview)
- [AzLanding — Landing Zone Studio](/AzLanding)
- [Azure Reliability](https://learn.microsoft.com/azure/reliability/)
- [WAF — Reliability pillar](https://learn.microsoft.com/azure/architecture/framework/resiliency/)
