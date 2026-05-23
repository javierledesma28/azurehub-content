---
title: AzRecipes — Architecture Recipes Catalog
description: Arquitecturas pre-armadas listas para producción, importables al canvas.
category: herramientas
slug: azrecipes
lang: es
order: 45
readMinutes: 3
tags: [Design, Free forever]
lastUpdated: 2026-05-23
sources:
  - https://learn.microsoft.com/azure/architecture/
---

## AzRecipes

AzRecipes es el catálogo de "recipes" — arquitecturas pre-armadas, curadas por el Hub, para casos comunes: 3-tier web, hub-and-spoke, AKS production-grade, event-driven, data platform. Click, importás al canvas, ya tenés baseline para arrancar.

## Para qué sirve

- Arrancar un proyecto con baseline production-ready en vez de hoja en blanco
- Comparar recipes para el mismo caso (ej: AKS small vs AKS multi-region)
- Importar la recipe completa al canvas de AzDraw
- Tener un set curado de arquitecturas validadas

## Cómo empezar

1. **Paso 1**: Abrí AzRecipes y filtrá por categoría (web, data, AI, hybrid, etc.).
2. **Paso 2**: Click en la recipe para ver el diagrama, componentes y notas.
3. **Paso 3**: Import to AzDraw — empezás a editar tu versión.

## Casos de uso comunes

- **Kickoff**: nuevo proyecto, querés baseline AKS productivo y no inventarlo desde cero
- **Templates internos**: replicás la misma base entre proyectos de un cliente
- **Enseñar**: el junior ve la recipe completa antes de proponer una propia

## Tips de uso

- AzRecipes te da el baseline; el ajuste fino al cliente lo hacés vos
- Si tu org tiene patterns propios, AzDraw te deja guardar diagramas como template

## FAQ

**¿Diferencia con AzArch?**
AzArch indexa patterns oficiales Microsoft. AzRecipes son recipes curadas por el Hub (pueden combinar varios patterns + opiniones de diseño).

**¿Puedo aportar recipes?**
No por UI todavía. Si tenés una buena, mandanos un PR.

## Recursos relacionados

- [AzArch — Architecture Reference Center](/AzArch)
- [AzDraw — Architecture canvas](/AzDraw)
- [Microsoft Azure Architecture Center](https://learn.microsoft.com/azure/architecture/)
