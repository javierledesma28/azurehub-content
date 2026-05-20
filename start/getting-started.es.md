---
title: Primeros 5 minutos
description: Lo mínimo para que el Hub te devuelva valor antes de cerrarlo.
category: start
slug: getting-started
order: 10
readMinutes: 4
tags: [Onboarding, All plans]
lastUpdated: 2026-05-20
author: javier
sources:
  - https://learn.microsoft.com/azure/cloud-adoption-framework/
---

## Reflexión antes de seguir

Si llegaste hasta acá probablemente esperás otro toolkit con 20 features y 0 enfoque.

Este no lo es. Tiene 21 tools pero todos arrancan en la misma pregunta: **¿esto te resuelve algo hoy o no?** Si la respuesta es no, cerrá la pestaña — no nos ofendemos.

## Para qué sirve esta guía

Vas a entrar a una constelación de tools, un trial PRO de 15 días sin tarjeta, y la sensación legítima de que no sabés por dónde arrancar. Esta guía te baja a tierra en 4 minutos.

**Lo que vas a poder hacer al terminar:**

- Saber qué tool atacar primero según tu problema
- Entender qué desbloquea el trial PRO sin pagar nada
- Tener una decisión clara: seguir explorando o no

## Cómo funciona

### Paso 1 — Identificá tu problema, no la feature

Tres caminos típicos al entrar:

| Si tu prioridad es… | Empezá por |
|---|---|
| **Dibujar una arquitectura** ya | [`AzDraw`](/AzDraw) — canvas drag-and-drop con 100+ recursos Azure |
| **Nombrar resources** consistentemente | [`AzNamer`](/AzNamer) — convención CAF + audit del tenant real (PRO) |
| **Diseñar una Landing Zone** | [`AzCAF`](/AzCAF) — guía paso a paso con plantillas |
| **Revisar arquitecturas existentes** | [`AzReview`](/AzReview) — toolkit Well-Architected |
| **Planificar IPs / subnets** | [`AzNet`](/AzNet) — calculadora visual |

Si ninguna te calza, abrí la landing y mirá la constelación. Cada tool tiene una descripción técnica en 1 línea.

### Paso 2 — Trial PRO sin tarjeta

Al hacer signup obtenés **15 días con todo desbloqueado**:

- 21 tools (incluyendo los 7 PRO: AzPolicy, AzRBAC, AzCompliance, etc.)
- Cloud sync ilimitado (tus diagramas viven en tu cuenta, no en localStorage)
- 1 workspace (para empezar a separar contextos)
- Connect Tenant disponible si querés probar audits reales

Al día 15, sin upgrade, volvés a Free. Tus datos no se borran — pasan a read-only hasta que upgradees.

### Paso 3 — Decidí en 48 horas

Si en dos días no te resolviste un problema concreto con el Hub, probablemente no es para vos. Y está bien. Te recomendamos cancelar el trial vos mismo (en el menú de usuario) para no ocupar mental space con un recordatorio.

## Decisión arquitectural

**¿Cuándo NO necesitás Azure Hub?**

- Sos arquitecto solo, un solo cliente, todo onpremises → la mayoría de tools no te aportan
- Solo dibujás diagramas y ya tenés Lucid/Miro → AzDraw es más bueno para Azure pero la curva de switcheo puede no valer
- Tu equipo ya tiene Terraform Cloud + Vantage + draw.io enterprise → ya cubrís el 80%

Honestidad importa más que conversión. Si entrás por curiosidad y te sirve, te quedás.

## Hack de campo

**No empieces por la tool más linda. Empezá por la tool que reemplaza una tarea que hacés todas las semanas.**

Si nombrás recursos cada lunes → AzNamer. Si dibujás arquitecturas cada miércoles → AzDraw. Si calculás capacity cada fin de mes → AzCost.

La probabilidad de adopción es directamente proporcional a la frecuencia del dolor que resolvés. Una tool que usás una vez al año no se vuelve hábito, no importa cuán buena sea.

## Llevatelo a tu IA

```markdown
# Azure Hub — quick onboarding

## ¿Qué es?
SaaS para arquitectos Azure. 21 tools, freemium ($0 / $12 / $49 / $149).
Trial PRO 15 días sin tarjeta.

## Diferencial real
- Connect Tenant: bindeás un tenant Azure real (read-only)
  y los tools cruzan tu diseño contra la realidad deployada.
- AzNamer Audit ya está live en tier Consulting+.
- AzPolicy/AzRBAC/AzCompliance audits arrivan Q3/Q4 2026.

## Cuándo NO vale la pena
- Un solo cliente, todo onprem
- Ya tenés Lucid/Miro + draw.io enterprise + Terraform Cloud + Vantage

## Empezá por
La tool que reemplaza una tarea semanal recurrente.
```

## Fuentes oficiales

- **[CAF — Cloud Adoption Framework](https://learn.microsoft.com/azure/cloud-adoption-framework/)** — la convención de referencia que usamos en todos los tools
