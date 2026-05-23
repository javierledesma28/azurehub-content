---
title: AzArch — Architecture Reference Center
description: Architecture patterns oficiales de Microsoft, filtrables y listos para importar al canvas.
category: herramientas
slug: azarch
lang: es
order: 40
readMinutes: 4
tags: [Design, Free forever]
lastUpdated: 2026-05-23
sources:
  - https://learn.microsoft.com/azure/architecture/
---

## AzArch

AzArch es el centro de referencia de architecture patterns oficiales del Microsoft Architecture Center. Filtrá por workload, busca por tecnología, y si te gusta el patrón, lo importás al canvas de AzDraw con un click — sin pasar 20 minutos copiando un PDF.

## Para qué sirve

- Encontrar el pattern de referencia para un caso (3-tier web, AKS multi-region, event-driven, etc.)
- Ver ~200 patterns indexados sin saltar entre páginas de docs
- Filtrar por industria, tecnología o pilar WAF
- Importar el pattern como punto de partida del próximo diagrama

## Cómo empezar

1. **Paso 1**: Abrí AzArch desde el Hub.
2. **Paso 2**: Usá la barra de búsqueda o los filtros (industria, servicios incluidos, complejidad).
3. **Paso 3**: Click en el pattern para ver el diagrama, descripción y componentes. Click "Import to AzDraw" para arrancar a editarlo.

## Casos de uso comunes

- **Kickoff de proyecto**: necesitás el baseline reference de "AKS production-grade" y no querés inventarlo desde cero
- **Comparar opciones**: ¿API Management + Functions vs Front Door + App Service? Vés los dos patterns lado a lado
- **Enseñar al equipo**: el junior ve el pattern oficial antes de proponer uno

## Tips de uso

- Filtrá por "Well-Architected" si querés patterns que ya pasaron review formal
- Después de importar al canvas, validá con AzReview — el pattern es punto de partida, no respuesta final

## FAQ

**¿Los patterns están actualizados?**
Sincronizamos contra Microsoft Architecture Center; si Microsoft actualiza un pattern, lo refrescamos en el próximo release.

**¿Puedo proponer un pattern propio?**
Hoy no. AzRecipes cubre arquitecturas curadas por el Hub.

## Recursos relacionados

- [AzRecipes — Recipes catalog](/AzRecipes)
- [AzDraw — Architecture canvas](/AzDraw)
- [Microsoft Azure Architecture Center](https://learn.microsoft.com/azure/architecture/)
