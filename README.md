# Azure Architects Handbook

> Knowledge Base for Azure architects — concise guides and deep dives covering CAF, WAF, Connect Tenant security, naming conventions, Landing Zones and more.

This repository hosts the **content** of the [Azure Hub Knowledge Base](https://azurehub.t28.io/learn), kept open-source so anyone can read, link to, and propose improvements without needing an account.

**Status:** maintained by [@javierledesma28](https://github.com/javierledesma28) · synced from the private Azure Hub repo on every change.

---

## What's here

15 articles spanning 5 categories (all bilingual ES/EN, **30 markdown files**):

```
start/                  Onboarding (2 articles)
├── getting-started     First 5 minutes
└── glossary            CAF/WAF/Azure terms

conceptos/              Concepts (2 articles)
├── workspaces          Multi-client containers
└── trial-pro           15-day PRO trial mechanics

herramientas/           Tools (6 articles)
├── aznamer             CAF naming generator (concise)
├── aznamer-deep-dive   ↳ 18-min deep dive
├── azdraw              Architecture canvas (concise)
├── azdraw-deep-dive    ↳ 18-min deep dive
├── azcaf               Landing Zone studio (concise)
├── azcaf-deep-dive     ↳ 20-min deep dive
└── azreview            Well-Architected toolkit

connect-tenant/         (1 article)
└── security-faq        Read-only consent + what we do/don't do

cuenta/                 Account (1 article)
└── planes              Plans: Free, PRO, Team, Consulting, Enterprise
```

### Article format (every article follows this template)

1. **A quick reflection** — challenges the reader's current assumption
2. **What it's for** — problems you probably have + what you'll be able to do
3. **How it works** — concrete walkthrough with code/diagrams
4. **Architectural decision** — when it fits, when it doesn't (honest)
5. **Field hack** — battle-tested tip from a real architect
6. **Take it to your AI** — agnostic markdown block to paste into ChatGPT/Claude/Copilot
7. **Official sources** — every claim links to Microsoft Learn

### Why "take it to your AI"

Every article includes a **cloud-agnostic markdown reference block** ready to paste into your favorite LLM. The Hub gives you the starting point — the conversation continues wherever you want.

---

## Read it rendered

The same content with proper styling, navigation, search, and code highlighting lives at:

**👉 https://azurehub.t28.io/learn**

This repo is the source. The rendered site is where you actually use it.

---

## Want to contribute or fix something?

Found a typo, a stale Microsoft Learn link, or an outdated claim? Open a PR or an issue. PRs typically merge within 48h.

For new article ideas, please open an issue first to discuss scope — we keep the bar high on:

- **Honesty** about when each tool/practice does NOT apply
- **Every claim** must link to an official source (Microsoft Learn, IETF, vendor docs)
- **Engineer tone** — no marketing speak, no "leverage synergies"

---

## License

Content licensed under **Creative Commons BY-SA 4.0** — share + adapt freely with attribution to Azure Hub. See [LICENSE](./LICENSE).

The Azure Hub *product* (the SaaS at azurehub.t28.io with cloud sync, audits, multi-tenant workspaces and admin features) is closed-source and offered under a commercial subscription. This repo contains only the educational content.

---

## What you won't find here

This repo deliberately excludes:

- Product code (React app, Node API, Postgres schema)
- Connect Tenant audit engine
- Pricing logic, billing integration
- Internal scripts (deploys, backups, smoke checks)
- Anything that lives in the [Azure Hub](https://azurehub.t28.io) product

If you're looking for those, they're in a private repo because they form the value of the SaaS. The knowledge they enable lives here, free.

---

## About the author

Built and maintained by [Javier Ledesma](https://www.linkedin.com/in/ledesmajavier/) — independent architect working with Microsoft Azure since the early days.

Reach me at [hola@t28.io](mailto:hola@t28.io) for consulting engagements, content contributions or feedback.
