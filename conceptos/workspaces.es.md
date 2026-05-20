---
title: Workspaces
description: El contenedor top-level que separa clientes, proyectos y experimentos sin mezclar gobierno.
category: conceptos
slug: workspaces
order: 10
readMinutes: 5
tags: [Foundation, All plans]
lastUpdated: 2026-05-20
sources:
  - https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/
  - https://learn.microsoft.com/azure/governance/management-groups/overview
---

## Reflexión antes de seguir

¿Estás diseñando para un cliente o para tres? ¿Tus diagramas de "ProyectoX" y "ProyectoY" comparten storage accounts en tu cabeza pero en realidad son tenants distintos?

Si trabajás multi-cliente o multi-proyecto, mezclar el contexto es el primer pecado.

## Para qué sirve

Un **Workspace** en Azure Hub es el contenedor top-level del trabajo. Cada workspace tiene su propio set de diagramas (AzDraw), reviews (AzReview), subnet plans (AzNet) y — cuando lo conectes — su binding a un tenant Azure real.

**Problemas que probablemente tenés:**

- Cliente A y Cliente B mezclados en la misma vista
- No sabés a qué tenant pertenece el diagrama que estás viendo
- Convención de naming de un proyecto se filtra al otro
- Compartir contenido con el equipo del cliente sin exponer otros

**Lo que vas a poder hacer:**

- Aislar contenido por cliente / proyecto / iniciativa
- Vincular cada workspace a un tenant Azure (PRO Consulting+)
- Mantener naming conventions independientes
- Switchear contexto con un dropdown en la TopBar

## Cómo funciona

Todo recurso (diagrama, review, subnet plan) pertenece a exactamente **un** workspace. El switcher en la TopBar es el contexto activo:

```bash
# estructura mental
Workspace "Default"
  ├── Diagramas
  ├── Reviews
  ├── Subnet plans
  └── (opcional) Tenant binding

Workspace "Cliente Acme"
  ├── Diagramas
  ├── Reviews
  ├── Subnet plans
  └── Tenant binding: acme.onmicrosoft.com
```

## Decisión arquitectural

**El workspace es a Azure Hub lo que un Management Group es a Azure.**

| Camino | Cuándo conviene |
|---|---|
| **Un solo workspace** | Freelance trabajando un solo cliente / una sola iniciativa interna |
| **Workspace por cliente** | Consultoras, MSPs, arquitectos multi-cuenta |
| **Workspace por entorno** | Equipos internos con prd/dev/staging muy separados |
| **Workspace por iniciativa** | Programas grandes con muchos proyectos hermanos |

No hay límite "técnico" óptimo. La pregunta correcta es: **¿qué cosas deberían compartir convención de naming y tenant binding?** Eso va junto. El resto se separa.

Referencia: [CAF Landing Zones](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/) — la lógica de separación es la misma a otra escala.

## Hack de campo

**Nombrá el workspace como vas a buscar la factura.**

Si el cliente te paga como "Acme S.A.", el workspace es "Acme". Si la iniciativa interna se llama "FinOps 2026", el workspace es "FinOps 2026". Suena obvio — hasta que tenés 12 workspaces llamados "Cliente1", "Cliente2", "Proyecto-nuevo".

Trial: 1 workspace. PRO: ilimitados. Te recomiendo no crear más de los que vas a usar la misma semana.

## Llevatelo a tu IA

```markdown
# Workspace strategy — quick ref

## When to split
- Different billing entity (client)
- Different Azure tenant
- Different naming convention
- Different governance baseline

## When NOT to split
- Different environment of same app (use tags)
- Different team of same client (use roles)
- Temporary experiment (use tmp- prefix in same workspace)

## Rule of thumb
"What should share the same naming convention
 and tenant binding?" → same workspace.
```

## Fuentes oficiales

- **[CAF — Landing Zones](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/)** — separación lógica a escala de Azure
- **[Management Groups overview](https://learn.microsoft.com/azure/governance/management-groups/overview)** — el equivalente Azure de "contenedor top-level"
