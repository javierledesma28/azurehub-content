---
title: Glosario
description: Términos CAF, WAF y Azure que aparecen en todo el Hub — sin abstracciones.
category: start
slug: glossary
order: 20
readMinutes: 5
tags: [Reference, All plans]
lastUpdated: 2026-05-20
author: javier
sources:
  - https://learn.microsoft.com/azure/cloud-adoption-framework/
  - https://learn.microsoft.com/azure/well-architected/
---

## Reflexión antes de seguir

La mitad de los problemas de comunicación en un equipo Azure son por términos que usamos como sinónimos cuando no lo son.

"Subscription", "tenant", "directory" no son lo mismo. "Workload" y "application" tampoco. Este glosario es para volver al uso correcto, no para inventar definiciones nuevas.

## Para qué sirve

Una referencia compacta de los términos que más usamos en el Hub. Cada entrada vincula a Microsoft Learn por si querés profundizar — esto es punto de partida, no enciclopedia.

## Cómo funciona

### Identidad y tenancy

- **Tenant (Microsoft Entra)** — la "organización" en el mundo Microsoft. Un email `@empresa.onmicrosoft.com` vive en un tenant. Una empresa típica tiene 1 tenant productivo + 1-2 sandbox.
- **Directory** — sinónimo informal de tenant. Es Entra ID (antes Azure AD).
- **Subscription** — el contenedor de billing dentro de un tenant. Una empresa puede tener N subscriptions, todas bajo el mismo tenant.
- **Management Group** — agrupa subscriptions para aplicar policies/RBAC en bloque. Forma una jerarquía hasta 6 niveles.
- **Service Principal (SP)** — identidad de aplicación (no humano). Cuando otorgás consent a Azure Hub Tenant Connector, se crea un SP en tu tenant.

### Recursos

- **Resource Group (RG)** — caja para agrupar recursos con mismo ciclo de vida. Borrar el RG borra todo dentro.
- **Resource** — lo que Azure factura: VM, Storage Account, AKS cluster, etc.
- **Location / Region** — dónde físicamente vive el recurso (eus, weu, brs…).
- **ARM ID** — el path completo del recurso (`/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Network/virtualNetworks/<name>`). Es la primary key universal en Azure.

### Frameworks

- **CAF (Cloud Adoption Framework)** — guía Microsoft sobre **cómo adoptar Azure**: strategy, plan, ready, adopt, govern, manage, secure. Naming conventions, landing zones y abreviaciones vienen de acá.
- **WAF (Well-Architected Framework)** — guía Microsoft sobre **cómo diseñar bien**: reliability, security, cost, operations, performance. AzReview y AzArch usan este modelo.
- **Landing Zone** — entorno Azure pre-configurado siguiendo CAF (networking, identity, governance ya armado). Punto de aterrizaje para nuevos workloads.

### Networking

- **VNet (Virtual Network)** — la "LAN privada" en Azure. Tiene su propio CIDR.
- **Subnet** — segmento dentro de una VNet con su CIDR propio.
- **Hub-and-Spoke** — topología donde un VNet central (hub) conecta a VNets satélite (spokes). Patrón canónico para empresas.
- **Peering** — conexión directa entre dos VNets. Permite comunicación sin VPN/firewall.
- **Private Endpoint (PE)** — IP privada dentro de tu VNet que apunta a un servicio PaaS (Storage, KV, SQL). Evita exposición pública.

### Governance

- **Policy** — regla declarativa que evalúa o restringe recursos (ej. "no permitir Storage Account público").
- **Initiative** — colección de policies aplicadas en bloque (ej. ISO 27001 baseline).
- **RBAC (Role-Based Access Control)** — modelo de permisos por rol. Reader / Contributor / Owner son los más comunes.
- **Tag** — clave-valor para clasificar recursos. Esencial para cost allocation, lifecycle, contact info.

### Azure Hub specific

- **Workspace** — contenedor top-level de tu trabajo (multi-cliente, multi-proyecto). [Ver Workspaces](/learn/conceptos/workspaces).
- **Tenant binding** — relación entre un workspace y un tenant Azure conectado. Habilita audits.
- **Trial PRO** — 15 días con todo desbloqueado sin tarjeta. [Ver Trial PRO](/learn/conceptos/trial-pro).

## Decisión arquitectural

**Una definición no es un consenso de equipo.**

Tener el glosario claro no garantiza que tu equipo lo use bien. Lo que sí ayuda: cuando aparece confusión, pegá el link de Microsoft Learn relevante en el thread y movés la discusión al término correcto. Reduce el costo de aclarar.

## Hack de campo

**Pregunta diagnóstica cuando alguien dice "subscription":**

"¿Te referís a la suscripción que paga, o al tenant donde viven los usuarios?"

El 30% de las veces la persona estaba mezclando los dos. Resolver eso de entrada te ahorra 20 minutos de zoom después.

## Llevatelo a tu IA

```markdown
# Azure tenancy hierarchy — quick ref

Tenant (Entra ID, "la organización")
  └── Management Group (jerarquia hasta 6 niveles)
        └── Subscription (unidad de billing)
              └── Resource Group (ciclo de vida común)
                    └── Resource (VM, Storage, AKS, etc.)

Cada nivel hereda policies y RBAC al inferior.
```

## Fuentes oficiales

- **[CAF — Cloud Adoption Framework](https://learn.microsoft.com/azure/cloud-adoption-framework/)** — la guía de "cómo adoptar Azure"
- **[WAF — Well-Architected Framework](https://learn.microsoft.com/azure/well-architected/)** — la guía de "cómo diseñar bien"
- **[Azure resource hierarchy](https://learn.microsoft.com/azure/azure-resource-manager/management/overview)** — tenants, MGs, subs, RGs
