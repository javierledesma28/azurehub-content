---
title: AzDraw · Deep dive
description: Anatomy of a decent Azure diagram, the boundary system in depth, cloud sync mechanics, canonical patterns, and the 12 most common pitfalls.
category: herramientas
slug: azdraw-deep-dive
order: 11
readMinutes: 18
tags: [Design, Deep dive]
lastUpdated: 2026-05-20
author: javier
sources:
  - https://learn.microsoft.com/azure/architecture/icons/
  - https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/
  - https://learn.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
---

## When to read this

This is the long version of [AzDraw](/learn/herramientas/azdraw). The concise one is 5 minutes. This is 18.

Read it if:
- You're going to **standardize diagram format** across your team
- It frustrates you that your diagrams don't survive past the sprint
- You need a **reusable template** (Hub-Spoke, Landing Zone, AKS)
- You'll **import/export** between AzDraw and other tools
- You're principal architect and want your team to **draw well**, not just draw

If you only want to drag a VNet to the canvas, [go to the short article](/learn/herramientas/azdraw).

## Anatomy of a decent Azure diagram

There are three levels of "diagram":

```
Level 1 — Trash:   boxes with text, no icons, no structure
Level 2 — OK:      correct icons, readable layout
Level 3 — Good:    boundary semantics + clear flow + CAF-aligned naming
```

Most teams live at Level 1-2 without knowing. Moving up to Level 3 isn't talent — it's following 5 rules:

### The 5 non-negotiable rules

1. **Explicit boundaries**. A VM inside a Resource Group inside a Subscription must visually appear enclosed. Not "assumed".
2. **Official Azure icons**, not generic ones. A "blue square that says Storage" is not a Storage Account.
3. **CAF-aligned naming in labels**. If your real name is going to be `vnet-payments-prod-eus-001`, put it in the diagram. Not `MyVnet`.
4. **Directional flow**. Arrows have direction and mean something (HTTPS, gRPC, AMQP, replication).
5. **Region/Zone visible**. If your workload is multi-region, the two regions must be visually separate, not fused into a flat canvas.

AzDraw supports all 5 natively. Rule 1 is the one that generates the most "aha" moments when people get it.

### Example: the same workload at 3 levels

**Level 1 (trash):**

```
[Web App] ────→ [Database]
```

Works for a whiteboard. Not for a review.

**Level 2 (OK):**

```
🌐 App Service ────HTTPS────→ 🗄 SQL Database
   "WebPortal"                "Db_Customers"
```

Correct icons, meaningful labels. Missing context.

**Level 3 (Good) — with AzDraw:**

```
┌─ Subscription: sub-payments-prod ─────────────────────────┐
│ ┌─ Resource Group: rg-payments-prod-eus ────────────────┐ │
│ │ ┌─ VNet: vnet-payments-prod-eus-001 ──────────────┐  │ │
│ │ │ ┌─ Subnet: snet-app-prod-eus-001 ─┐             │  │ │
│ │ │ │ 🌐 app-payments-prod-eus-001    │             │  │ │
│ │ │ └──────────────┬──────────────────┘             │  │ │
│ │ │ ┌─ Subnet: snet-db-prod-eus-001 ──┐  HTTPS      │  │ │
│ │ │ │ 🗄 sql-payments-prod-eus-001    │◀────────────┘  │ │
│ │ │ └────────────────────────────────┬┘                │ │
│ │ └─────────────────────────────────┬┴─────────────────┘ │
│ └────────────────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────────────────┘
```

Same thing, but **interpretable a year from now** by someone who wasn't in the design.

## The boundary system in depth

AzDraw models three boundaries with Azure-aware semantics:

| Boundary       | Type tag      | Default size       | Contains                                                |
|----------------|---------------|---------------------|---------------------------------------------------------|
| Subscription   | `boundary-sub` | 1100 × 720         | RGs, MGs, Policy Assignments                            |
| Resource Group | `boundary-rg`  | 900 × 600          | Most resources (VM, AKS, Storage, KV…)                  |
| VNet           | `boundary-vnet`| 800 × 500          | Subnets, NSGs, Route Tables, Peerings                   |

### How auto-parenting works

When you drag a resource INSIDE an existing boundary, the `CanvasEditor` detects the drop point and assigns `parentId` to the new node. This means:

- Moving the boundary moves its children
- Resizing the boundary doesn't move children
- Selecting the boundary doesn't select children (individual selection)
- Copying the boundary (future) copies it with all children

### What auto-parenting does NOT do

- **It doesn't work retroactively**. If you draw 10 VMs first and then drag an RG over them, the VMs aren't "adopted" automatically. You'd have to drag them inside manually.
- **It doesn't validate invalid combinations**. AzDraw lets you put a VNet inside an RG (correct) or an RG inside a VNet (incorrect). Today it's the user's responsibility. Validation lands in Q4 2026.
- **It doesn't persist logical relationships** — only visual. If you export to PNG, "RG contains VM" is visible. But we don't export a structured JSON that says `vm.resourceGroup = rg-xxx` (yet).

### Resizing boundaries

Boundaries are resizable. When you drag a boundary near the canvas edge or another node, **there's no automatic snap**. It's freedom + responsibility. People coming from Lucid miss smart-snap; people from draw.io appreciate the freedom.

### Typical boundary chains

Combinations you see every day:

```text
Sub → RG → VNet → Subnet → Resource     (web app pattern)
Sub → RG → VNet → Subnet → AKS          (kubernetes pattern)
Sub → RG → Storage (no VNet)            (PaaS without private network)
Sub → RG (no VNet) → KV → Apps          (shared services)
Sub → RG → Resource (no more nesting)   (PoCs and sandboxes)
```

If your workload doesn't map to one of these, **you're probably over-engineering or under-modeling**.

## The two palettes — T28 vs AZ

AzDraw has two palettes, switchable without losing content:

### T28 (generic)

Minimalist icons in "engineer's whiteboard" style. Good for:
- Pre-decision diagrams (you haven't picked AKS vs App Service yet)
- Workshops where you don't want to bias with branding
- Multi-cloud agnostic diagrams
- Initial layouts you'll refine later

### AZ (official Azure)

Microsoft's official icons, [the same as the portal](https://learn.microsoft.com/azure/architecture/icons/). Good for:
- Final diagrams for decks / docs
- ADRs and RFCs
- Anything someone outside the team will see
- Recognizable Hub-Spoke / Landing Zone templates

### When to switch

Recommended workflow:

```
Start  →  T28 (free, fast, no visual commitment)
Mid    →  T28 (solid structure, still iterating)
Final  →  AZ (ready to present)
```

The switch preserves positions and edges. Only icons change. If your team complains "the icon isn't there", it's probably on T28 — switch to AZ.

## Cloud sync — how it actually works

### Auto-save mechanism

```text
1. User edits a node (move, rename, create, delete)
2. The Zustand store emits a change
3. useDiagramSync does signature-based diff:
   sha256(nodes + edges + viewport)
4. If the signature changed:
   - Wait 2s (debounce)
   - If no more changes in those 2s → PATCH /api/diagrams/<id>
5. If the signature did NOT change (phantom drag, click without move) → no-op
```

**The reason for the 2s debounce**: it's the sweet point between "you feel it saved" and "you don't spam the backend". Lower (500ms) generates 4x more writes. Higher (5s) generates "did it save?" anxiety.

### Signature-based change detection

The hash includes:
- Position of each node (x, y)
- Node data (label, type, color)
- Connections (edges with source/target)
- Viewport (zoom, pan position)

What is **not** tracked:
- Hover states
- Selection (which node is selected)
- UI state (panel open/closed)

This avoids writes when you're just navigating.

### Multi-diagram

| Plan       | Diagrams per workspace |
|------------|------------------------|
| Free       | 1                      |
| PRO Trial  | Unlimited              |
| PRO        | Unlimited              |
| Team       | Unlimited              |
| Enterprise | Unlimited              |

The `DiagramSwitcher` dropdown in the TopBar:
- Lists the diagrams in the active workspace
- ✓ Marks the current one
- Allows inline rename (click the name)
- "+ New" — creates another (if your plan allows it)
- 🗑 Delete with confirm-on-second-click

### Workspaces + diagrams

Diagrams are scoped to the workspace. If you switch workspaces, diagrams switch. If you delete the workspace, diagrams are deleted (FK cascade).

[See Workspaces](/learn/conceptos/workspaces) for strategy on when to create how many.

## Canonical patterns — ready-to-customize templates

These are the most-used patterns. Today you have to build them by hand — Q3 2026 brings one-click templates.

### Pattern 1: Hub-and-Spoke

```text
┌─ Hub VNet (vnet-hub-prod-eus-001) ──────────────────┐
│  Azure Firewall · VPN Gateway · DNS Private Resolver │
│  Subnets: AzureFirewallSubnet, GatewaySubnet         │
└─────────┬──────────────────────┬────────────────────┘
          │ peering              │ peering
          ▼                      ▼
┌─ Spoke 1: prod app ─────┐ ┌─ Spoke 2: dev app ─────┐
│  vnet-app1-prod-eus-001 │ │  vnet-app2-dev-eus-001 │
│  Workload subnets       │ │  Workload subnets      │
└─────────────────────────┘ └────────────────────────┘
```

Official reference: [Hub-Spoke network topology](https://learn.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)

### Pattern 2: Landing Zone (CAF-aligned)

```text
Management Group: Tenant Root
  └─ Platform MG
       ├─ Identity sub      (Entra ID Connect, AD DS)
       ├─ Management sub    (Log Analytics, Sentinel)
       └─ Connectivity sub  (Hub VNet, Firewall, ExpressRoute)
  └─ Landing Zones MG
       ├─ Corp LZ           (internal workloads)
       └─ Online LZ         (internet-facing workloads)
  └─ Sandbox MG
       └─ Sandbox subs      (PoCs, experiments)
```

Each LZ is typically 1 subscription in this scheme. Decide this before you start deploying.

Official reference: [CAF Landing Zone](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/)

### Pattern 3: AKS deployment with private endpoints

```text
┌─ RG: rg-aks-prod-eus ─────────────────────────────────────┐
│ ┌─ VNet: vnet-aks-prod-eus-001 ───────────────────────┐   │
│ │ ┌─ Subnet: snet-aks-system ─┐   System node pool    │   │
│ │ ├─ Subnet: snet-aks-user ────┤  User node pool      │   │
│ │ ├─ Subnet: snet-aks-pe ──────┤  Private endpoints   │   │
│ │ └─ Subnet: snet-aks-ingress ┘   App Gateway / NGINX │   │
│ └─────────────────────────────────────────────────────┘   │
│                                                            │
│ AKS Cluster: aks-payments-prod-eus-001                    │
│ Container Registry: crpaymentsprodxxx (private endpoint)  │
│ Key Vault: kv-payments-prod-eus-001 (private endpoint)    │
└────────────────────────────────────────────────────────────┘
```

### Pattern 4: Static Web App with Function backend

```text
┌─ RG: rg-portal-prod-eus ─────────────────────┐
│ Static Web App: stapp-portal-prod-eus-001    │
│   └─ Custom domain: portal.company.com       │
│                                              │
│ Function App: func-portal-api-prod-eus-001   │
│   └─ Storage: stportalprodeusxxx (state)     │
│   └─ Linked to Cosmos: cosmos-portal-prod    │
└──────────────────────────────────────────────┘
```

Simple on top, very cheap. Good for internal portals.

## Keyboard shortcuts and productivity

| Shortcut             | Action                                  |
|----------------------|------------------------------------------|
| `Delete` / `Backspace` | Remove selection                       |
| `Ctrl + Z`           | Undo                                    |
| `Ctrl + Y`           | Redo                                    |
| `Ctrl + A`           | Select all                              |
| `Ctrl + C` / `Ctrl + V` | Copy / paste (on roadmap)            |
| `Click + drag`       | Move node                               |
| `Shift + click`      | Multi-select                            |
| `Right-click`        | Context menu                            |
| `Scroll wheel`       | Zoom in/out                             |
| `Middle click + drag`| Pan the canvas                          |
| `Drag from handle`   | Create edge between nodes               |

### Productivity tips

1. **Use Shift + click for multi-select** and apply bulk changes (move, delete)
2. **The canvas has infinite scroll** — there's no "edge". If you get lost, there's a fit-view button in the bottom-right
3. **To align nodes by hand**: drag one near another and check coordinates (eventually real snap, today no)
4. **For large diagrams**: group visually with boundaries and minimize zoom out — no native "groups" but boundaries fulfill that role

## Export — PNG, SVG, future Bicep

### Available today

| Format | When to use                              | How                          |
|--------|------------------------------------------|------------------------------|
| **PNG** | Slides, docs, screenshots, Slack         | Export button → PNG          |
| **SVG** | Edit later in Illustrator/Figma          | Export button → SVG          |

PNG comes with bg `#050d1f` (dark) by default. SVG is transparent.

### On roadmap (not yet available)

- **Bicep export** — generate ARM/Bicep template from the diagram (Q1 2027)
- **Terraform export** — same for HCL (Q1 2027)
- **draw.io export** — to open in draw.io desktop / Microsoft portal (Q3 2026)
- **Mermaid export** — to embed in README.md (Q3 2026)

We don't promise reverse import (Bicep → AzDraw) — the complexity of parsing ARM templates is 10x more than export.

## Integration with other Hub tools

### With AzNamer

Today: when you draw a VNet in AzDraw, you know it should be called `vnet-<workload>-<env>-<region>-<instance>`. **The name you write by hand**.

Q4 2026: "Generate names" button in AzDraw that takes all canvas nodes and applies the workspace's AzNamer convention automatically. Today still manual.

### With AzNet

Today: AzNet (subnet planner) and AzDraw are independent. You design subnets in AzNet, draw them manually in AzDraw.

Roadmap: import from AzNet (Q2 2027). You take the subnet plan, AzDraw creates them as subnet nodes inside the right VNet.

### With AzReview

Today: AzReview asks things like "Does your diagram include separation of concerns by subnet?". You answer yes/no. **It doesn't verify against the real diagram**.

Roadmap: AzReview reads the AzDraw structure (in the same workspace) and auto-validates some criteria — askable questions become verifiable ones (Q3 2026).

### With AzArch (WAF Scoring)

Similar to AzReview: AzArch has a scoring module against Well-Architected. Today it's heuristic against manual inputs. Roadmap: tie the AzDraw diagram as input.

## Typical use cases

### Use case 1: ADR for architecture change

**Situation**: You're migrating from VMs to AKS. You need to document the "before / after" for the ADR.

**Workflow**:
1. "Before" diagram in T28 (fast)
2. Switch to AZ (final look)
3. Duplicate diagram (rename "After")
4. Edit the duplicate with the new architecture
5. PNG export both
6. Embed in the repo's ADR markdown

### Use case 2: Client proposal

**Situation**: Pre-sales call, you want to show what the proposed architecture would look like.

**Workflow**:
1. Diagram directly in AZ (client expects official Microsoft icons)
2. Use a dedicated workspace per client (visual separation)
3. Export PNG for the deck
4. Export SVG if you want the sales team to customize in Figma later

### Use case 3: Runbook documentation

**Situation**: Onboarding new engineer. You need to explain "this is our production stack".

**Workflow**:
1. Level 3 diagram (with boundaries, CAF naming, flows)
2. Export PNG at 2x (better to read)
3. Embed in the handbook
4. **Important**: re-export quarterly. A non-updated diagram is worse than no diagram.

## Pitfalls — the 12 I see most

| #  | Pitfall                                    | Symptom                            | Fix                                    |
|----|--------------------------------------------|-------------------------------------|----------------------------------------|
| 1  | Nodes without labels                       | "What is this?"                    | Double-click → edit the label          |
| 2  | Boundaries don't enclose their children    | Children don't move with the RG    | Drag children inside the boundary      |
| 3  | T28 icons in final diagram                 | "Tech demo" look, not pro           | Switch palette to AZ                    |
| 4  | Mixing envs in one diagram                 | "Dev and Prod in the same view"   | 1 diagram per env                      |
| 5  | Edges without label                        | "Is it HTTP? HTTPS? gRPC?"        | Double-click the edge to label         |
| 6  | Too many nodes in one canvas               | Illegible at >50 nodes             | Split into connected sub-diagrams      |
| 7  | Subnet outside its VNet                    | Broken visual semantics            | Drag the subnet inside the VNet        |
| 8  | Multi-region on a single flat canvas       | Impossible to understand what's where | Explicit region boundaries           |
| 9  | Generic names ("VM1", "Database")          | 0 useful after 2 weeks             | CAF naming on all labels                |
| 10 | Never exporting                            | Diagram dies when you close tab    | Auto-save covers you, but export to version |
| 11 | Editing "live" diagram without backup      | A bad click without Ctrl+Z = lost  | Duplicate before big changes           |
| 12 | Forgetting to update after a change        | 6-month-old diagram                | Quarterly diagram review               |

## When NOT to use AzDraw

AzDraw isn't the right tool for:

- **Business process diagrams** (BPMN, UML activities) → BPMN.io or Lucid
- **Sequence / class diagrams** (technical UML) → PlantUML, Mermaid
- **Mind maps** → Miro, MindMeister
- **Synchronous remote workshops** (live multi-cursor) → Miro
- **Multi-cloud strategy diagrams** → draw.io with custom stencils
- **Wireframes / UI mockups** → Figma, Sketch

If half your diagrams are the above, AzDraw doesn't add enough. If 80% are Azure architectures, it adds a lot.

## Honest roadmap

**Q3 2026:**
- draw.io (.drawio) export
- Mermaid export
- One-click templates (Hub-Spoke, Landing Zone, AKS)
- Validation: detect invalid boundary combinations

**Q4 2026:**
- AzNamer integration (generate names automatically)
- Clipboard copy/paste
- Auto layout (dagre) to reorganize legacy diagrams

**Q1 2027:**
- Bicep / Terraform export (IaC generation from the diagram)
- Collaborative multi-cursor (Figma-style) — Team/Enterprise tiers only

**Not promised (yet):**
- Real-time Figma-style collaboration
- Diagram versioning / branching
- AI-assisted "draw this in CAF for me"

## Take it to your AI

```markdown
# AzDraw — best practices

## Recommended workflow
1. T28 palette to iterate fast
2. Boundaries first (Sub → RG → VNet)
3. Resources drag-and-drop inside boundaries
4. Labels with full CAF naming
5. Edges with direction + protocol
6. Switch to AZ palette before exporting

## Anti-patterns
- Boundaries that don't enclose children
- Mixing envs in one diagram
- T28 icons in final diagram
- Nodes without labels / generic labels
- Diagrams that don't get updated

## Canonical patterns
- Hub-Spoke (reference: learn.microsoft.com)
- Landing Zone (CAF)
- AKS with private endpoints
- Static Web App + Function backend

## Export
PNG → for slides, docs
SVG → for later edit in Figma/Illustrator
Bicep/Terraform → roadmap Q1 2027

## Multi-diagram
Free: 1 per workspace
PRO/Trial: unlimited
```

## Official sources and references

- **[Azure Architecture Icons](https://learn.microsoft.com/azure/architecture/icons/)** — the AZ palette icons come from here
- **[CAF — Landing Zones](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/)** — boundary semantics
- **[Hub-Spoke Reference Architecture](https://learn.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)** — the most-used pattern

### Related articles in this KB

- [AzDraw · concise](/learn/herramientas/azdraw) — the 5-minute version
- [Workspaces](/learn/conceptos/workspaces) — how to separate diagrams by client / project
- [AzNamer · deep dive](/learn/herramientas/aznamer-deep-dive) — the naming convention used in diagram labels
