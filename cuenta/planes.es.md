---
title: Planes
description: Free, PRO, Team, Enterprise — qué pagás, qué desbloquea cada uno, sin marketing.
category: cuenta
slug: planes
order: 10
readMinutes: 4
tags: [Pricing, Account]
lastUpdated: 2026-05-21
author: javier
sources:
  - /pricing
---

## Reflexión antes de seguir

La página de pricing está hecha para vender. Esta página está hecha para que **decidas con info, no con emoción**.

Si después de leerla sigue dándote curiosidad probar PRO, hacé el trial. Si no, no pasa nada.

## Para qué sirve

Documentar técnicamente qué desbloquea cada plan, qué límites tiene, y cuándo conviene cada uno. Sin "MOST POPULAR" parpadeando.

## Cómo funciona

### Resumen tabular

| Plan       | Precio              | Tools                       | Cloud sync | Workspaces  | Connect Tenant | Ideal para                            |
|------------|---------------------|-----------------------------|------------|-------------|----------------|---------------------------------------|
| Free       | $0                  | 14 (de 21)                  | 1 diagrama | 0           | ❌             | Probar el Hub sin firmar nada         |
| PRO        | $12/mes             | 21                          | Ilimitado  | Personal    | ❌             | Arquitecto individual full-time       |
| **Team**   | **$29/seat (min 2)**| **21 + audits**             | Ilimitado  | Compartidos | ✅ 1 tenant    | Consultora chica · equipo interno     |
| Enterprise | $149/mes + seats    | 21 + audits + AzPolicy Compare + scheduled | Ilimitado | Ilimitados  | ✅ Ilimitados  | Consultoras grandes · MSPs            |

Yearly: ~30% de descuento (ej. PRO $99 vs $144).

> **Cambio de modelo (2026-05-21):** el tier Consulting ($49) se absorbió en Team per-seat. Si estabas en Consulting, quedaste en Team con todos los audits que ya tenías.

### Qué desbloquea cada salto

**Free → PRO ($12/mes)** — para cuando ya estás usando el Hub y querés:
- Las 7 tools PRO (AzPolicy, AzRBAC, AzCompliance, AzZeroTrust, AzResiliency, AzFinOps, AzMigrate, AzAI)
- Diagramas ilimitados con cloud sync
- Export Markdown, Mermaid, Bicep, Terraform
- Sin watermark · workspace personal + versionado 30 días

**PRO → Team ($29/seat, mínimo 2)** — para cuando trabajás con clientes o equipo y querés:
- Workspaces compartidos + templates de equipo + SSO (SAML/Entra) + branding propio
- **Connect Tenant**: bindeás 1 tenant Azure real (read-only) al workspace
- **AzNamer Audit en vivo**: cruzar tu convención CAF contra recursos reales del cliente
- **Tenant Import (AzDraw Live)**: importar topología real y comparar contra el diagrama
- Audit history 30 días

**Team → Enterprise ($149/mes + seats)** — para cuando manejás múltiples clientes y querés:
- Tenants Azure conectados ilimitados (no solo 1)
- **AzPolicy Compare**: policies diseñadas vs assignments deployados
- **Scheduled audits**: corren solos diario o semanal
- **Email/Slack alerts** ante drift
- 90 días de retención de history
- API + IaC export programable (REST + Terraform/Bicep)
- SLA 99.9% · soporte prioritario

### Trial PRO incluido en signup

Cualquier usuario nuevo arranca con 15 días de **trial PRO sin tarjeta**. Tenés todo el feature set de PRO disponible. No es Team/Enterprise (Connect Tenant queda visible pero no se puede activar).

[Ver detalles del trial →](/learn/conceptos/trial-pro)

## Decisión arquitectural

**¿Cuándo NO upgradeás?**

| Situación | Recomendación |
|---|---|
| Usás el Hub <1 vez por semana | Stay Free, no perdés nada |
| Estás en un proyecto único de 3 meses | PRO mensual durante el proyecto, downgrade después |
| Te interesa Connect Tenant pero tu cliente no acepta | Quedate en PRO, levantá el feature en la próxima propuesta |
| Tu empresa ya tiene Vantage + Lucid enterprise | Free + PRO ocasional para los specifics que ellos no cubren |

**El plan correcto NO es el más caro que podés pagar.** Es el más barato que cubre lo que vas a usar **esta semana**.

## Hack de campo

**Si dudás entre PRO y Team → empezá por PRO mensual.**

Si después de un mes te encontrás necesitando auditar tenants reales, ahí saltás a Team (mínimo 2 seats = $58). Una sola propuesta ganada con el audit ya lo paga 30 veces.

No hay penalidad por moverte entre planes — prorratéo automático.

## Llevatelo a tu IA

```markdown
# Azure Hub plans — decision matrix (post 2026-05-21)

## Decision tree
¿Usás el Hub >2x por semana?
  NO → Free
  SÍ → ¿Necesitás auditar tenants Azure reales?
    NO → PRO ($12/mes)
    SÍ → ¿Cuántos tenants/clientes simultáneos?
      1 + equipo chico → Team ($29/seat, mín 2 = $58)
      Múltiples + necesidad de AzPolicy Compare → Enterprise ($149 + seats)

## Trial
Free 15 días, sin tarjeta, todo PRO desbloqueado.

## Pagos
Paddle (merchant of record) — Q3 2026
Hasta entonces: launching soon notice.
```

## Fuentes oficiales

- **[/pricing](/pricing)** — la página de pricing con UI completa y toggle anual/mensual
- **[Trial PRO](/learn/conceptos/trial-pro)** — mecánica completa del trial
- **[Connect Tenant Security](/learn/connect-tenant/security-faq)** — permisos exactos si vas a Team/Enterprise
