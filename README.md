<div align="center">

# Azure Architects Handbook

### The Knowledge Base for engineers who actually run Azure — not the marketing version.

[![Articles](https://img.shields.io/badge/articles-15-F5C500?style=flat-square)](https://azurehub.t28.io/learn)
[![Languages](https://img.shields.io/badge/languages-ES%20%2B%20EN-blue?style=flat-square)](https://azurehub.t28.io/learn)
[![License](https://img.shields.io/badge/license-CC%20BY--SA%204.0-green?style=flat-square)](./LICENSE)
[![Live](https://img.shields.io/badge/read%20it%20live-azurehub.t28.io%2Flearn-F5C500?style=flat-square)](https://azurehub.t28.io/learn)
[![Twitter](https://img.shields.io/badge/discuss-LinkedIn-0A66C2?style=flat-square)](https://www.linkedin.com/in/ledesmajavier/)

**Concise guides + deep dives. CAF / WAF / Landing Zones / Connect Tenant audits.**
**Honest about when each practice does NOT apply.**

[**📖 Read it rendered →**](https://azurehub.t28.io/learn) &nbsp;·&nbsp; [**🛠 Try the product →**](https://azurehub.t28.io)

<sub>Maintained by **Javier Ledesma** · Head of IT & Cloud Strategy at Fundanet · Microsoft Azure Solutions Architect Expert · 15+ years operating Azure in regulated environments (banking · health · public sector) · [LinkedIn](https://www.linkedin.com/in/ledesmajavier/)</sub>

</div>

---

## Why this exists

Most Azure documentation falls into one of two camps:

- **Microsoft Learn** — exhaustive, formal, sometimes 40 pages when 4 would do.
- **Medium articles** — fragmented, opinionated, often outdated by month 3.

This handbook is the **missing middle layer** — opinionated like a senior architect's notebook, anchored in official sources, and updated continuously because it powers a paying SaaS product ([Azure Hub](https://azurehub.t28.io)).

> Every claim links to Microsoft Learn. Every "this is how I do it" is paired with **"and here's when you shouldn't"**.

---

## What's inside

```
📂 start/         (2 articles)         Onboarding + glossary
📂 conceptos/     (2 articles)         Workspaces + Trial PRO mechanics
📂 herramientas/  (7 articles)         AzNamer · AzDraw · AzCAF · AzReview
                                         + 3 deep dives (~18 min each)
📂 connect-tenant/(1 article)          Read-only tenant audit security FAQ
📂 cuenta/        (1 article)          Plans explained (Free → Enterprise)
```

**26 articles** across **5 categories** · all bilingual ES/EN · all maintained by [@javierledesma28](https://github.com/javierledesma28).

---

## Start here

| 👋 New to this | 🏗 Picking a tool | 🛡 Selling to a client |
|---|---|---|
| [**First 5 minutes**](./start/getting-started.es.md) — what this is and when to bail | [**AzCAF — Landing Zone tier**](./herramientas/azcaf.es.md) — decide T1/T2/T3 in 30 min | [**Connect Tenant security FAQ**](./connect-tenant/security-faq.es.md) — what we touch, what we don't |
| [**Glossary**](./start/glossary.es.md) — tenant ≠ subscription ≠ directory | [**AzNamer**](./herramientas/aznamer.es.md) — CAF naming with brutal honesty | [**Plans**](./cuenta/planes.es.md) — Free, PRO, Team, Consulting, Enterprise |

---

## The deep dives (~18 min each)

The serious ones. For when you're refactoring an existing tenant or selling governance to a real client.

| 📖 Deep dive | What you'll know after reading |
|---|---|
| [**AzNamer · Deep dive**](./herramientas/aznamer-deep-dive.es.md) | Migration playbook brownfield → CAF, audit engine internals, 12 production pitfalls |
| [**AzDraw · Deep dive**](./herramientas/azdraw-deep-dive.es.md) | 5 rules for diagrams that survive past one sprint, canonical patterns, cloud sync internals |
| [**AzCAF · Deep dive**](./herramientas/azcaf-deep-dive.es.md) | Tier 1-4 decision matrix, 2-week sprint plan, 6 anti-patterns, continuous audit cadence |

---

## The format — every article follows this template

1. **A quick reflection** — challenge the reader's current assumption
2. **What it's for** — your real problems + what you'll be able to do
3. **How it works** — concrete walkthroughs, no abstractions
4. **Architectural decision** — when it fits, **when it doesn't** (honest)
5. **Field hack** — battle-tested tip from running this on real tenants
6. **Take it to your AI** — agnostic markdown block, paste into ChatGPT/Claude/Copilot
7. **Official sources** — every claim links to Microsoft Learn or vendor docs

### What "Take it to your AI" looks like

Every article ends with a portable markdown block you can paste into your favorite LLM, like this:

```markdown
# CAF Naming Convention — full pattern

## Pattern
{abbr}-{workload}-{env}-{region}-{instance}

## Service quirks
- Storage Account: 24 chars, no hyphens, lowercase
- Key Vault: 24 chars, DNS-unique global
- VM Windows: 15 chars max (NetBIOS limit)
- AKS: lowercase only, 63 chars

## Anti-patterns
- abbr at end (kills alphabetical grouping)
- workload with internal hyphens
- mid-stream convention change
```

The Hub gives you the starting point. The conversation continues wherever you want.

---

## Read it the way it's meant to be read

The markdown here is the source. The **rendered version** at [azurehub.t28.io/learn](https://azurehub.t28.io/learn) has:

- 🎨 Proper styling, syntax highlighting, scroll-spy table of contents
- 🔍 Search across all articles
- 🌐 Toggle between ES / EN with one click
- ⬇️ Download any article as `.md` (the "Take it to your AI" block, full version)
- 🔗 Deep linking + breadcrumbs

[**👉 azurehub.t28.io/learn**](https://azurehub.t28.io/learn)

---

## How this connects to the Azure Hub product

This handbook is the **open-source content arm** of [Azure Hub](https://azurehub.t28.io) — a SaaS for Azure architects with **21 tools** spanning design, governance, audit and FinOps.

The product gives you the **tooling**:
- Architecture canvas with boundary semantics (`AzDraw`)
- CAF naming generator + live tenant audit (`AzNamer`)
- Landing Zone studio (`AzCAF`)
- Well-Architected review toolkit (`AzReview`)
- ...and 17 more

This repo gives you the **thinking** behind each tool, free forever.

**Pricing of the product:**

| Plan | Price | When |
|---|---|---|
| **Free** | $0 | 13 tools, 1 cloud diagram, local storage |
| **PRO** | $12 / month | 21 tools, unlimited diagrams + workspaces, cloud sync |
| **Consulting** | $49 / month | + 1 Azure tenant connected (Reader) + live audits |
| **Enterprise** | $149 / month | + unlimited tenants + scheduled audits + 90-day history |

Every signup includes a **15-day PRO trial with no credit card**. Cancel anytime.

[**Start the trial →**](https://azurehub.t28.io/pricing)

---

## Contribute

Found a typo, a stale Microsoft Learn link, or an outdated claim?

- **Small fix** → open a PR. Usually merged in <48h.
- **New article idea** → open an issue first. We keep the bar high on:
  - **Honesty** about when each practice does NOT apply
  - **Every claim** must link to an official source
  - **Engineer tone** — no marketing speak, no "synergies"

Translations to languages beyond ES/EN are welcome too. The frontmatter `lang` field drives the language detection.

---

## Star this repo if...

- ⭐ You manage Azure infrastructure and want a sane reference you can `Ctrl+F` through
- ⭐ You're picking governance practices for a new tenant and want to see how someone else decided
- ⭐ You're prepping a client conversation and need polished, sourced material
- ⭐ You teach Azure and want curated reading material for your students

A star helps the next architect Google their way here. Costs you a click, takes me hours to write. Fair trade.

---

## About the author

**[Javier Ledesma](https://www.linkedin.com/in/ledesmajavier/)** — Head of IT & Cloud Strategy at [Fundanet (Semicrol)](https://www.fundanet.com), leading Azure infrastructure and security for a clinical research platform used by hospitals, universities and research centers across Spain and Europe.

**15+ years** designing and operating Microsoft Azure platforms in high-criticality, regulated environments:

- 🏥 **Health** — clinical research platforms under ENS / ISO 27001 / GDPR
- 🏛 **Public sector** — Spanish national security framework (ENS)
- 🏦 **Banking** — regulated workloads, segmentation, compliance

**Current focus:**

- 🛡 Hub & Spoke, Landing Zones, Azure Virtual Desktop
- 🔐 Defender for Cloud · Sentinel · Entra ID · Conditional Access · PIM
- 🤖 Agentic AI on Azure (Semantic Kernel + Azure OpenAI + custom MCP servers + Azure AI Search RAG)
- 🛠 IaC with Terraform · Azure DevOps · GitHub Actions
- 💰 FinOps + cloud cost optimization

**Credentials:**

- Microsoft **Azure Solutions Architect Expert** (AZ-305)
- AI & LLM Specialization (Anthropic ×5, NVIDIA ×1)
- VMware VCP / VSP / VTSP
- Scrum Fundamentals Certified

**Reach me:**

- 💼 **LinkedIn** → [in/ledesmajavier](https://www.linkedin.com/in/ledesmajavier/) — for consulting engagements & discussions
- 📨 **hola@t28.io** — request a deep dive on a topic, propose a collaboration
- 🌐 **[t28.io](https://t28.io)** — independent consulting practice
- ✍️ **[blog.javierledesma.com.ar](https://blog.javierledesma.com.ar)** — long-form writing
- 🛠 **The product** → [azurehub.t28.io](https://azurehub.t28.io)

---

## What this repo does NOT contain

This is deliberately the **content** repo. You won't find here:

- Product code (React app, Node API, Postgres schema)
- The Connect Tenant audit engine
- Pricing logic, billing integration
- Internal scripts (deploys, backups, smoke checks)
- Anything that lives in the [Azure Hub](https://azurehub.t28.io) product

Those live in a private repo because they're the value of the SaaS. The knowledge they enable lives here, free.

---

## License

Content licensed under **[Creative Commons BY-SA 4.0](./LICENSE)** — share + adapt freely with attribution to Azure Hub. The Azure Hub *product* is closed-source and offered under a commercial subscription.

If you quote substantial chunks in a blog or talk, [a link back](https://azurehub.t28.io) is appreciated but not legally required.

---

<div align="center">

**Made by Javier Ledesma · t28.io · Azure Hub · 2026**

*If something here saved you a meeting, [drop me a line](mailto:hola@t28.io). I want to know what works.*

</div>
