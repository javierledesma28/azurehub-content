---
title: AzReview
description: Toolkit Well-Architected sin reuniones de 2 horas que nadie quiere tener.
category: herramientas
slug: azreview
order: 20
readMinutes: 5
tags: [Validate, Free with signup]
lastUpdated: 2026-05-20
author: javier
sources:
  - https://learn.microsoft.com/azure/well-architected/
  - https://learn.microsoft.com/assessments/azure-architecture-review/
---

## Reflexión antes de seguir

¿Cuántas Well-Architected Reviews que arrancaste terminaron en un PDF que nadie lee?

El problema no es WAF — es el formato. Workshops de 2 horas, plantillas Excel infinitas, scoring que parece subjetivo. AzReview es el intento de mantener el rigor del WAF perdiendo el ceremonial.

## Para qué sirve

Un cuestionario estructurado por los 5 pilares de Well-Architected, con scoring automático, identificación de gaps prioritarios y export a Markdown para integrar al ADR del proyecto.

**Problemas que probablemente tenés:**

- Hacés WAF reviews una vez, después se quedan colgadas
- El Excel de Microsoft Assessments es bueno pero pesa para integrar al workflow
- Tu equipo no recuerda los criterios entre revisiones
- Te falta un baseline para comparar entre proyectos

**Lo que vas a poder hacer:**

- Correr una review en ~20 minutos en vez de 2 horas
- Persistir el resultado al workspace (cloud sync)
- Ver el score por pilar + gaps priorizados
- Exportar Markdown para tu repo / Notion / Confluence

## Cómo funciona

### Los 5 pilares de WAF

```bash
Reliability        →  ¿Tu workload sigue corriendo si falla X?
Security           →  ¿Quién accede a qué, cómo se protege, qué auditás?
Cost Optimization  →  ¿Pagás por lo que usás, o por lo que olvidaste apagar?
Operational Excel. →  ¿Podés diagnosticar, deployar y rollback?
Performance        →  ¿Escala bajo carga? ¿Cuántas regiones necesitás de verdad?
```

AzReview presenta entre 8 y 15 preguntas por pilar, con respuestas estructuradas (no abiertas) que mapean a un score 0-100.

### Resultado

- **Score por pilar** (0-100)
- **Score global** ponderado
- **Top 5 gaps prioritarios** — las preguntas donde scoreaste más bajo
- **Recomendaciones** linkeadas a Microsoft Learn

### Cloud sync

- **Free**: 1 review activa por workspace
- **PRO / Trial**: ilimitadas + history
- Auto-save mientras respondés (signature-based, sin pings inútiles)

## Decisión arquitectural

**AzReview no reemplaza el WAF official de Microsoft Assessments.**

| Cuándo conviene AzReview | Cuándo conviene Microsoft Assessment |
|---|---|
| Sprint reviews, retros, internal alignment | Audit formal con sign-off ejecutivo |
| Workloads <50 recursos | Workloads grandes / enterprise |
| Tu equipo lo usa cada release | Una vez al año por compliance |
| Querés Markdown para tu repo | Necesitás PDF con branding Microsoft |

Para audits formales seguí usando [Microsoft Assessments](https://learn.microsoft.com/assessments/azure-architecture-review/). Para todo lo demás, AzReview.

## Hack de campo

**Hacé la review en pareja, no solo.**

El sesgo del solo-tirador es brutal. "Sí, esto es Resilient" responde el que diseñó, mientras el que opera ya sabe que la base de datos no tiene réplica.

Sentate con alguien que **no diseñó** el workload (preferentemente operaciones). Las primeras 2-3 preguntas van a destapar gaps que ni veías. Eso vale más que toda la review.

## Llevatelo a tu IA

```markdown
# AzReview workflow — quick ref

## When to run
- Sprint end: light review (~15 min, focus en 1 pilar)
- Quarter end: full review (~45 min, 5 pilares)
- Pre-prod: full review obligatoria

## Output formato
Markdown con secciones:
1. Workload context (nombre, owners, criticality)
2. Score por pilar + score global
3. Top 5 gaps + acciones sugeridas
4. Links Microsoft Learn relevantes

## Anti-patterns
- Reviewer = arquitecto que diseñó (sesgo)
- Score sin commitments concretos
- Resultado en PDF que nadie abre

## Best practice
Score → top 3 gaps → owners + due date → next review
```

## Fuentes oficiales

- **[WAF — Well-Architected Framework](https://learn.microsoft.com/azure/well-architected/)** — los 5 pilares en detalle
- **[Microsoft Assessment — Azure Architecture Review](https://learn.microsoft.com/assessments/azure-architecture-review/)** — la herramienta formal de Microsoft (complementaria)
