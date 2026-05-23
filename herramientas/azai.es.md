---
title: AzAI — AI Workload Design Patterns
description: Patterns probados para RAG, fine-tuning, inference y multi-model orchestration.
category: herramientas
slug: azai
lang: es
order: 135
readMinutes: 4
tags: [AI, PRO]
lastUpdated: 2026-05-23
sources:
  - https://learn.microsoft.com/azure/ai-foundry/
  - https://learn.microsoft.com/azure/architecture/ai-ml/
---

> 🔒 Requiere PRO — los patterns AI y la guía de diseño viven en PRO.

## AzAI

AzAI es el catálogo de design patterns para workloads de IA en Azure: RAG (Retrieval-Augmented Generation), fine-tuning, inference at edge, multi-model orchestration. Alineado a Azure AI Foundry para que el pattern conecte con servicios reales, no abstracciones.

## Para qué sirve

- Encontrar el pattern adecuado según el caso (chat sobre docs internos, agente multi-step, etc.)
- Entender los trade-offs (RAG vs fine-tuning vs prompt engineering)
- Ver la arquitectura completa con servicios Azure concretos
- Importar el pattern al canvas para personalizarlo

## Cómo empezar

1. **Paso 1**: Abrí AzAI y filtrá por tipo (RAG, fine-tune, inference, orchestration).
2. **Paso 2**: Click en el pattern para ver diagrama, componentes y consideraciones.
3. **Paso 3**: Import to AzDraw para empezar a editar tu versión.

## Casos de uso comunes

- **PoC RAG**: necesitás el pattern de chatbot sobre Knowledge Base sin inventar la rueda
- **Producción**: pasar de notebook a arquitectura con throughput, retries y observability
- **Decisión RAG vs fine-tune**: el pattern te ayuda a decidir cuándo cada uno

## Tips de uso

- RAG suele ser la respuesta antes que fine-tuning — más simple, más auditable, menos costoso
- Embeddings + vector store son el corazón del RAG — pensalo desde el primer día
- Multi-model orchestration vale cuando tenés más de un caso, no cuando estás empezando

## FAQ

**¿Soporta Azure OpenAI y modelos open-source?**
Sí — los patterns son agnósticos al modelo. Conectan con Azure OpenAI, Azure AI Foundry hub, Mistral, etc.

**¿Tiene patterns para agentes?**
Sí, multi-step y agentic workflows están cubiertos.

## Recursos relacionados

- [AzArch — Architecture Reference Center](/AzArch)
- [AzDraw — Architecture canvas](/AzDraw)
- [Azure AI Foundry](https://learn.microsoft.com/azure/ai-foundry/)
- [Azure AI/ML architectures](https://learn.microsoft.com/azure/architecture/ai-ml/)
