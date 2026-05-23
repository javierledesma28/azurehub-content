---
title: AzCompliance — Regulatory Compliance Mapper
description: ENS, NIS2, ISO 27001 sin sufrir — auditoría automatizada sobre tu tenant.
category: herramientas
slug: azcompliance
lang: es
order: 120
readMinutes: 5
tags: [Compliance, PRO]
lastUpdated: 2026-05-23
sources:
  - https://www.boe.es/buscar/act.php?id=BOE-A-2022-7191
  - https://learn.microsoft.com/compliance/
---

> 🔒 Requiere PRO — la auditoría automatizada corre sobre el tenant conectado.

## AzCompliance

AzCompliance es el mapper regulatorio multi-framework: ENS Medio + Alto (75 controles cada uno), NIS2 e ISO 27001 (coming soon). Corre 13+ checks automáticos sobre tu tenant, te deja completar la review manual con evidencia, y exporta un PDF presentable al auditor.

## Para qué sirve

- Auditar tu tenant Azure contra ENS Medio o ENS Alto
- Mezclar checks automáticos (auth, network, encryption, monitoring, backup) con review manual
- Subir evidencia (PDF, screenshots) por control
- Exportar reporte PDF presentable al auditor
- Schedule audits recurrentes con drift alerts (Enterprise)

## Cómo empezar

1. **Paso 1**: Conectá el tenant (Reader sobre subscriptions).
2. **Paso 2**: Abrí AzCompliance, elegí framework (ENS Medio / Alto), elegí scope.
3. **Paso 3**: Corré el audit. Revisá los controles automáticos, completá los manuales con verdict + nota + evidencia. Exportá PDF.

## Casos de uso comunes

- **Pre-auditoría ENS**: cerrás gaps un mes antes de la auditoría formal
- **Continuous compliance**: schedule semanal con alerts si el score baja
- **Comparativa entre tenants** (Enterprise): mismo framework, varios tenants, drift visible

## Tips de uso

- Los controles `partial` necesitan tu review manual — no son falla, son "no podemos saberlo solos"
- Subí evidencia siempre — el auditor la va a pedir y el PDF la incluye
- Configurá el drift threshold según el score que vos definas como aceptable (default 85%)

## FAQ

**¿Reemplaza al auditor?**
No — te prepara para el auditor. La auditoría formal sigue siendo humana.

**¿Cubre NIS2 / ISO 27001 hoy?**
La infraestructura es multi-framework desde día 1. ENS Medio + Alto están live. NIS2 e ISO 27001 vienen en próxima fase.

## Recursos relacionados

- [AzPolicy — Policy Builder](/AzPolicy)
- [AzZeroTrust — Zero Trust Assessment](/AzZeroTrust)
- [AzReview — Well-Architected Review](/AzReview)
- [ENS — RD 311/2022 (BOE)](https://www.boe.es/buscar/act.php?id=BOE-A-2022-7191)
- [Microsoft Compliance](https://learn.microsoft.com/compliance/)
