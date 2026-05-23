---
title: AzAI — AI Workload Design Patterns
description: Battle-tested patterns for RAG, fine-tuning, inference, and multi-model orchestration.
category: herramientas
slug: azai
lang: en
order: 135
readMinutes: 4
tags: [AI, PRO]
lastUpdated: 2026-05-23
sources:
  - https://learn.microsoft.com/azure/ai-foundry/
  - https://learn.microsoft.com/azure/architecture/ai-ml/
---

> 🔒 Requires PRO — AI patterns and the design guidance live in PRO.

## AzAI

AzAI is the catalog of design patterns for AI workloads on Azure: RAG (Retrieval-Augmented Generation), fine-tuning, inference at edge, multi-model orchestration. Aligned with Azure AI Foundry so each pattern connects to real services, not abstractions.

## What it's for

- Find the right pattern per case (chat over internal docs, multi-step agent, etc.)
- Understand the trade-offs (RAG vs fine-tuning vs prompt engineering)
- See the full architecture with concrete Azure services
- Import the pattern to the canvas to customize it

## Getting started

1. **Step 1**: Open AzAI and filter by type (RAG, fine-tune, inference, orchestration).
2. **Step 2**: Click a pattern to see diagram, components, and considerations.
3. **Step 3**: Import to AzDraw to start editing your version.

## Common use cases

- **RAG PoC**: you need the chatbot-over-knowledge-base pattern without reinventing the wheel
- **Production**: move from notebook to architecture with throughput, retries, observability
- **RAG vs fine-tune decision**: the pattern helps you choose when each fits

## Tips

- RAG is usually the answer before fine-tuning — simpler, auditable, cheaper
- Embeddings + vector store are the heart of RAG — design them from day 1
- Multi-model orchestration is worth it when you have more than one case, not when starting

## FAQ

**Does it support Azure OpenAI and open-source models?**
Yes — the patterns are model-agnostic. They connect to Azure OpenAI, AI Foundry hub, Mistral, etc.

**Does it have agent patterns?**
Yes — multi-step and agentic workflows are covered.

## Related

- [AzArch — Architecture Reference Center](/AzArch)
- [AzDraw — Architecture canvas](/AzDraw)
- [Azure AI Foundry](https://learn.microsoft.com/azure/ai-foundry/)
- [Azure AI/ML architectures](https://learn.microsoft.com/azure/architecture/ai-ml/)
