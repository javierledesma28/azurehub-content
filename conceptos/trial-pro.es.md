---
title: Trial PRO · 15 días
description: Qué desbloquea el trial, qué pasa al día 16, y cómo cancelarlo si no te sirve.
category: conceptos
slug: trial-pro
order: 20
readMinutes: 3
tags: [Trial, All plans]
lastUpdated: 2026-05-20
author: javier
---

## Reflexión antes de seguir

La mayoría de trials SaaS son una pelea por tu tarjeta. Esta no.

Acá no pedimos tarjeta para arrancar. Si el Hub no te sirve, no te pasa nada al día 16 — solo volvés a Free. Sin sustos. Sin "atrapaste el plan automático sin querer".

## Para qué sirve

Explica exactamente qué obtenés con el trial, qué pasa cuando se vence, y cómo cancelar voluntariamente si decidiste antes que no es para vos.

## Cómo funciona

### Al hacer signup

Tu cuenta arranca en `status='trialing'`, `plan='pro'`, con `trial_end = NOW() + 15 días`. **15 días, no 14, no 30. Quince calendario, no días hábiles.**

### Lo que tenés desbloqueado durante esos 15 días

- **21 tools** completas — los 7 PRO (AzPolicy, AzRBAC, AzCompliance, AzZeroTrust, AzResiliency, AzFinOps, AzMigrate, AzAI) están abiertas
- **Cloud sync ilimitado** — diagramas, reviews, subnet plans, naming configs persisten en tu cuenta
- **1 workspace** para empezar a separar contextos
- **Connect Tenant disponible** si querés probar audits reales (necesitás un tenant Azure tuyo o de cliente con consent)

### En el día 15

A las 23:59 UTC del día 15 corre un cron (vía pg-boss schedule) que:

1. Te downgradea automáticamente a `plan='free'`, `status='active'`
2. Envía un email con CTA al `/pricing?offer=trial-extension` ofreciendo "1 mes gratis si te suscribís 3 meses"
3. Tus diagramas y configs **no se borran** — pasan a read-only en tools PRO. Pueden re-abrirse en cualquier momento al upgradear.

### Si querés cancelar antes de los 15 días

Hoy podés simplemente esperar a que se venza solo — no te cuesta nada y no requiere tarjeta. Cuando Paddle esté live (Q3 2026), el menú de usuario va a tener "Cancelar trial" con un click.

### Banner de aviso

Cuando faltan ≤3 días aparece el TrialBanner en la top-bar con la cuenta regresiva. No es ad — es información. Lo podés ignorar.

## Decisión arquitectural

**¿Por qué 15 días y no 7 ó 30?**

| Duración | Trade-off |
|---|---|
| **7 días** | Trial corto, decisión rápida, mayor presión. Pero no alcanza para integrar a un equipo |
| **15 días** ✅ | Suficiente para probar el Hub en 1-2 sprints reales. Decisión informada sin que se haga eterno |
| **30 días** | Muy largo, mucho ruido en el funnel, "ya lo decido mañana" eterno |

15 días alinea con un sprint medio. Si en un sprint no le encontraste el valor, probablemente no es para vos.

## Hack de campo

**Usá el trial para probar Connect Tenant.**

Es el feature más diferenciado y el que más miedo da. Pero **en el trial podés conectar el tenant azurehub28 (sandbox tuyo) sin compromiso**. Corré un audit, exportá CSV, mostraselo a tu sponsor interno. Esa es la conversación que vende, no las screenshots.

## Llevatelo a tu IA

```markdown
# Hub28 Trial PRO — quick facts

Duración: 15 días calendario
Tarjeta: NO requerida
Activación: automática en signup
Cancelación: automática al día 16 (downgrade a Free)
Datos: persisten en read-only, recuperables en upgrade

Incluye:
- 21 tools (todas las PRO desbloqueadas)
- Cloud sync ilimitado
- 1 workspace
- Connect Tenant (audit AzNamer Live)

Oferta post-trial: "1 mes gratis con plan trimestral"
(via email automatico al expirar)
```

## Fuentes oficiales

Este es un mecanismo interno de Hub28, no de Azure / Microsoft. Si querés ver el código, está en `api/src/jobs/trialCheck.ts` (pg-boss schedule corriendo cron `17 * * * *`).
