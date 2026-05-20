---
title: Planes
description: Free, PRO, Team, Consulting, Enterprise — qué pagás, qué desbloquea cada uno, sin marketing.
category: cuenta
slug: planes
order: 10
readMinutes: 4
tags: [Pricing, Account]
lastUpdated: 2026-05-20
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

| Plan       | Precio   | Tools          | Cloud sync | Workspaces | Connect Tenant | Ideal para                            |
|------------|----------|----------------|------------|------------|----------------|---------------------------------------|
| Free       | $0       | 13 (de 21)     | 1 diagrama | 0          | ❌             | Probar el Hub sin firmar nada         |
| PRO        | $12/mes  | 21             | Ilimitado  | Ilimitados | ❌             | Arquitecto individual full-time       |
| Team       | $29/seat | 21             | Ilimitado  | Compartidos| ❌             | Equipos internos con SSO              |
| Consulting | $49/mes  | 21 + audit     | Ilimitado  | Ilimitados | ✅ 1 tenant    | Freelance multi-cliente               |
| Enterprise     | $149/mes | 21 + audit + scheduled | Ilimitado | Ilimitados | ✅ Ilimitados | Consultoras / MSPs                   |

Yearly: ~30% de descuento (ej. PRO $99 vs $144).

### Qué desbloquea cada salto

**Free → PRO ($12/mes)** — para cuando ya estás usando el Hub y querés:
- Las 7 tools PRO (AzPolicy, AzRBAC, AzCompliance, AzZeroTrust, AzResiliency, AzFinOps, AzMigrate, AzAI)
- Diagramas ilimitados con cloud sync
- Workspaces ilimitados para separar contextos

**PRO → Consulting ($49/mes)** — para cuando trabajás con clientes y querés:
- **Connect Tenant**: bindeás un tenant Azure real (read-only) al workspace
- **AzNamer Audit en vivo**: cruzar tu convención CAF contra recursos reales del cliente
- **AzPolicy Compare** (Q3 2026): policies diseñadas vs assignments deployados
- **Audit history**: cada audit corrido queda guardado en el workspace

**Consulting → Enterprise ($149/mes)** — para cuando manejás múltiples clientes y querés:
- Tenants Azure ilimitados (no solo 1)
- **Scheduled audits**: corren solos diario o semanal
- **Delta detection**: te avisa qué cambió entre audits
- 90 días de retención de history
- Slack dedicado para soporte

### Trial PRO incluido en signup

Cualquier usuario nuevo arranca con 15 días de **trial PRO sin tarjeta**. Tenés todo el feature set de PRO disponible. No es Consulting/Enterprise (Connect Tenant queda visible pero sin scheduled audits).

Si te interesa probar Connect Tenant durante el trial → podés conectar tu sandbox Azure. Es la mejor forma de evaluar si Consulting es para vos.

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

**Si dudás entre PRO y Consulting → empezá por PRO mensual.**

PRO mensual + Connect Tenant en el trial → te da una semana para evaluar Consulting **mientras ya estás pago de PRO**. Si el audit te aporta, subís a Consulting al mes siguiente. Si no, te quedás en PRO sin compromiso de $49.

No hay penalidad por moverte entre planes — prorratéo automático.

## Llevatelo a tu IA

```markdown
# Azure Hub plans — decision matrix

## Decision tree
¿Usás el Hub >2x por semana?
  NO → Free
  SÍ → ¿Trabajás con clientes externos?
    NO → ¿Estás solo o en equipo?
      Solo → PRO ($12)
      Equipo → Team ($29/seat, Q3 2026)
    SÍ → ¿Cuántos clientes simultáneos?
      1-2 → Consulting ($49)
      3+ con audits regulares → Enterprise ($149)

## Trial
Free 15 días, sin tarjeta, todo PRO desbloqueado.
Connect Tenant disponible para sandbox tests.

## Pagos
Paddle (merchant of record) — Q3 2026
Hasta entonces: launching soon notice.
```

## Fuentes oficiales

- **[/pricing](/pricing)** — la página de pricing con UI completa y toggle anual/mensual
- **[Trial PRO](/learn/conceptos/trial-pro)** — mecánica completa del trial
- **[Connect Tenant Security](/learn/connect-tenant/security-faq)** — permisos exactos si vas a Consulting/Enterprise
