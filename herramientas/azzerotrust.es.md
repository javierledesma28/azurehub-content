---
title: AzZeroTrust — Zero Trust Maturity Assessment
description: Assessment del modelo Zero Trust de Microsoft con scoring por pilar y roadmap accionable.
category: herramientas
slug: azzerotrust
lang: es
order: 115
readMinutes: 4
tags: [Security, Free with signup]
lastUpdated: 2026-05-23
sources:
  - https://learn.microsoft.com/security/zero-trust/
  - https://www.microsoft.com/security/business/zero-trust
---

## AzZeroTrust

AzZeroTrust es el assessment de madurez del modelo Zero Trust de Microsoft. Cubre los 6 pilares (Identity, Endpoints, Apps, Data, Infrastructure, Network), te da un scoring por pilar y un roadmap accionable para subir de "Traditional" a "Optimal".

## Para qué sirve

- Medir madurez Zero Trust del tenant en 6 pilares
- Identificar el pilar más débil para priorizar
- Generar un roadmap "Traditional → Advanced → Optimal"
- Documentar el assessment como baseline para próximas reviews

## Cómo empezar

1. **Paso 1**: Abrí AzZeroTrust desde el Hub.
2. **Paso 2**: Respondé el cuestionario por pilar (~10 minutos por pilar).
3. **Paso 3**: Revisá scoring + roadmap. Exportá Markdown para tu repo/Confluence.

## Casos de uso comunes

- **CISO baseline**: medir dónde está la org hoy vs el target Zero Trust
- **Pre-audit**: identificar gaps antes de un audit de seguridad
- **Trimestral**: re-medir cada quarter para trackear progreso

## Tips de uso

- No hagas el assessment solo — pareja arquitecto + operaciones es ideal
- "Optimal" no es para todos: definí el target realista de tu org (Advanced suele alcanzar)
- Combiná con AzRBAC y AzCompliance para cerrar gaps específicos

## FAQ

**¿Reemplaza el Microsoft Zero Trust Assessment Tool?**
Complementa. El nuestro vive integrado con el resto del Hub (RBAC, Compliance).

**¿Se puede comparar entre tenants?**
Cada workspace tiene su propio assessment. La comparación cross-tenant es manual hoy.

## Recursos relacionados

- [AzRBAC — RBAC Designer](/AzRBAC)
- [AzCompliance — Regulatory Mapper](/AzCompliance)
- [Microsoft Zero Trust](https://learn.microsoft.com/security/zero-trust/)
