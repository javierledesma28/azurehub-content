---
title: AzDraw
description: Canvas para diagramar arquitecturas Azure sin la fricción de draw.io.
category: herramientas
slug: azdraw
order: 10
readMinutes: 5
tags: [Design, Free forever]
lastUpdated: 2026-05-20
author: javier
sources:
  - https://learn.microsoft.com/azure/architecture/icons/
  - https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/
---

## Reflexión antes de seguir

¿Cuántos diagramas de arquitectura sobreviven al sprint en el que se dibujaron?

Si el promedio en tu equipo es "menos de la mitad", el problema no es la herramienta — es la fricción. Un diagrama que tarda 2 horas en hacer y 3 minutos en quedar obsoleto no se mantiene. La pregunta correcta no es "qué herramienta de diagramas usamos" sino "cuánta fricción tiene actualizar un diagrama existente".

## Para qué sirve

AzDraw es un canvas drag-and-drop con 100+ recursos Azure pre-modelados, boundaries visuales (Subscription / Resource Group / VNet) que auto-parentan a sus hijos, y cloud sync para que el diagrama no muera en tu carpeta de descargas.

**Problemas que probablemente tenés:**

- draw.io tiene los iconos Azure pero no la semántica (no sabe qué es un boundary)
- Lucid/Miro no tienen plantillas Hub-and-Spoke decentes
- Tu equipo dibuja en una pizarra y nadie lo digitaliza
- Cada PowerPoint nuevo arranca de cero porque buscar el último deck es peor

**Lo que vas a poder hacer:**

- Arrastrar recursos al canvas con los iconos oficiales Microsoft
- Crear boundaries que automáticamente contienen los hijos
- Sincronizar el diagrama a tu cuenta (1 free, ilimitados en PRO/trial)
- Switchear entre paleta T28 (genérica) y AZ (Azure oficial) sin perder el contenido

## Cómo funciona

### Boundaries visuales

Los tres tipos de boundary que ya entienden el modelo Azure:

```bash
Subscription   →  contiene Resource Groups, Policy Assignments, MGs
Resource Group →  contiene la mayoría de recursos (VMs, AKS, Storage…)
VNet           →  contiene Subnets, NSGs, Route Tables
```

Arrastrás un boundary primero, después arrastrás los recursos dentro. AzDraw los auto-parenta. Si movés el boundary, los hijos se mueven con él.

### Multi-diagrama + workspaces

Cada diagrama vive en un workspace. Switcher en la top-bar para cambiar de workspace, dropdown junto al título para cambiar de diagrama dentro del workspace.

- **Free**: 1 diagrama
- **PRO / Trial**: ilimitados
- **Consulting**: ilimitados + tenant binding para AzNamer Audit

### Auto-save inteligente

Edit, esperá 2 segundos, ya está guardado en la nube. Sin botón "Save". La signature-based change detection evita pings inútiles si no cambió nada real.

## Decisión arquitectural

**AzDraw asume que querés diagramar Azure. Si no, hay opciones mejores:**

| Camino | Cuándo conviene |
|---|---|
| **AzDraw** | Diagramás Azure / Híbrido. Querés boundaries que se comportan. Sync sin pensar |
| **draw.io** | Multi-cloud agnóstico, control absoluto sobre estilos, ya está adoptado en la empresa |
| **Lucid / Miro** | Workshops colaborativos en vivo, brainstorming, no-Azure |
| **Pen + paper** | El 80% de los diagramas iniciales no necesitan más que esto. Honestidad. |

No tratamos de ganarte en flexibilidad pura. Tratamos de ganarte en "específico de Azure + bajo costo de mantenimiento".

## Hack de campo

**Empezá con el boundary, no con el recurso.**

Si dibujás primero las VMs y después intentás encerrarlas en un Resource Group, el auto-parent no funciona retroactivamente. El flow que sí funciona:

1. Subscription boundary → 2. Resource Groups dentro → 3. VNet boundary (si aplica) → 4. Recursos hijo

Te tarda 30 segundos extra al principio y te ahorra reorganizar 30 nodos después.

## Llevatelo a tu IA

```markdown
# AzDraw — quick reference

## Flow recomendado
1. Crear workspace (opcional, "Default" alcanza)
2. Crear diagrama nuevo (auto-save activado)
3. Empezar por boundaries (Sub → RG → VNet)
4. Drag recursos dentro
5. Conectar con edges (drag desde el handle)
6. Switchear paleta T28 ↔ AZ si necesitás

## Atajos importantes
- Delete / Backspace → eliminar selección
- Ctrl+Z / Ctrl+Y → undo / redo
- Ctrl+A → seleccionar todo
- Click derecho → menú contextual

## Lo que NO hace (todavía)
- Generar Bicep / Terraform desde el diagrama
- Validar diseño contra WAF (eso lo hace AzArch)
- Importar desde draw.io (.drawio) o Visio
```

## Fuentes oficiales

- **[Azure Architecture Icons](https://learn.microsoft.com/azure/architecture/icons/)** — los iconos oficiales que usamos
- **[CAF — Landing Zones](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/)** — la semántica de boundaries está alineada a CAF
