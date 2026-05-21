---
title: AzDraw · Deep dive
description: Anatomía de un diagrama Azure decente, el sistema de boundaries en profundidad, cloud sync, patrones canónicos y los 12 errores más comunes.
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

## Cuándo leer esto

Esta es la versión larga de [AzDraw](/learn/herramientas/azdraw). La concisa son 5 minutos. Esta son 18.

Leela si:
- Vas a **estandarizar el formato de diagramas** en tu equipo
- Te frustra que tus diagramas no sobreviven al sprint
- Necesitás un **template reutilizable** (Hub-Spoke, Landing Zone, AKS)
- Vas a **importar/exportar** entre AzDraw y otras tools
- Sos arquitecto principal y querés que tu equipo **dibuje bien**, no solo dibuje

Si solo querés arrastrar un VNet al canvas, [andá al article corto](/learn/herramientas/azdraw).

## Anatomía de un diagrama Azure decente

Hay tres niveles de "diagrama":

```
Nivel 1 — Trash:   cajas con texto, sin iconos, sin estructura
Nivel 2 — OK:      iconos correctos, layout legible
Nivel 3 — Bueno:   semántica de boundaries + flujo claro + naming alineado a CAF
```

La mayoría de equipos viven en Nivel 1-2 sin saberlo. Subir a Nivel 3 no es talento — es seguir 5 reglas:

### Las 5 reglas no-negociables

1. **Boundaries explícitos**. Una VM dentro de un Resource Group dentro de una Subscription debe verse encerrada visualmente. No "asumido".
2. **Iconos Azure oficiales**, no genéricos. Un "cuadrado azul que dice Storage" no es un Storage Account.
3. **Naming alineado a CAF en los labels**. Si tu nombre real va a ser `vnet-payments-prod-eus-001`, ponelo en el diagrama. No `MyVnet`.
4. **Flujo direccional**. Las flechas tienen dirección y significan algo (HTTPS, gRPC, AMQP, replicación).
5. **Region/Zone visible**. Si tu workload es multi-región, las dos regiones deben verse separadas, no fusionadas en un canvas plano.

AzDraw soporta los 5 nativamente. La regla 1 es la que más genera "ajá" cuando la gente la entiende.

### Ejemplo: el mismo workload en 3 niveles

**Nivel 1 (trash):**

```
[Web App] ────→ [Database]
```

Sirve para una pizarra. No para una review.

**Nivel 2 (OK):**

```
🌐 App Service ────HTTPS────→ 🗄 SQL Database
   "WebPortal"                "Db_Customers"
```

Iconos correctos, labels con sentido. Falta contexto.

**Nivel 3 (Bueno) — con AzDraw:**

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

Lo mismo, pero **interpretable a un año vista** por alguien que no participó del diseño.

## El sistema de boundaries en profundidad

AzDraw modela tres boundaries con semántica Azure-aware:

| Boundary       | Tag tipo     | Default size       | Contiene                                                |
|----------------|--------------|---------------------|---------------------------------------------------------|
| Subscription   | `boundary-sub` | 1100 × 720         | RGs, MGs, Policy Assignments                            |
| Resource Group | `boundary-rg`  | 900 × 600          | La mayoría de recursos (VM, AKS, Storage, KV…)         |
| VNet           | `boundary-vnet`| 800 × 500          | Subnets, NSGs, Route Tables, Peerings                   |

### Cómo funciona el auto-parenting

Cuando arrastrás un recurso DENTRO de un boundary existente, el `CanvasEditor` detecta el drop point y le asigna `parentId` al nodo nuevo. Esto significa:

- Si movés el boundary, los hijos se mueven con él
- Si redimensionás el boundary, los hijos quedan donde estaban
- Si seleccionás el boundary, los hijos NO se seleccionan (selección individual)
- Si copiás el boundary (futuro), copia con todos los hijos

### Lo que NO hace el auto-parenting

- **No funciona retroactivamente**. Si dibujás 10 VMs primero y después arrastrás un RG por encima, las VMs no se "adoptan" automáticamente. Tendrías que dragearlas dentro manualmente.
- **No valida combinaciones inválidas**. AzDraw te deja meter un VNet dentro de un RG (correcto) o un RG dentro de un VNet (incorrecto). Hoy es responsabilidad del usuario. Validación llega en Q4 2026.
- **No persiste relación lógica** — solo visual. Si exportás a PNG, el "RG contiene VM" se ve. Pero no exportamos un JSON estructurado que diga `vm.resourceGroup = rg-xxx` (todavía).

### Resize de boundaries

Los boundaries son resizables. Cuando arrastrás un boundary cerca del borde del canvas o de otro nodo, **no hay snap automático**. Es liberty + responsabilidad. La gente que viene de Lucid extraña el smart-snap; los que vienen de draw.io agradecen la libertad.

### Boundary chains típicas

Combinaciones que se ven todos los días:

```text
Sub → RG → VNet → Subnet → Resource     (web app pattern)
Sub → RG → VNet → Subnet → AKS          (kubernetes pattern)
Sub → RG → Storage (sin VNet)           (PaaS sin red privada)
Sub → RG (sin VNet) → KV → Apps         (services compartidos)
Sub → RG → Resource (sin más nesting)   (PoCs y sandboxes)
```

Si tu workload no se mapea a una de estas, **probablemente estés over-engineering o under-modeling**.

## Las dos paletas — T28 vs AZ

AzDraw tiene dos paletas, switcheables sin perder contenido:

### T28 (genérica)

Iconos minimalistas estilo "engineer's whiteboard". Sirven para:
- Diagramas pre-decisión (todavía no elegiste si AKS o App Service)
- Workshops donde no querés sesgar con branding
- Diagramas multi-cloud agnósticos
- Layouts iniciales que después refinás

### AZ (Azure oficial)

Iconos oficiales de Microsoft, [los mismos del portal](https://learn.microsoft.com/azure/architecture/icons/). Sirven para:
- Diagramas finales para deck / docs
- ADRs y RFCs
- Cualquier cosa que va a ver alguien que no es del equipo
- Templates Hub-Spoke / Landing Zone reconocibles

### Cuándo switchear

Workflow recomendado:

```
Inicio  →  T28 (libre, rápido, sin compromiso visual)
Mid     →  T28 (estructura sólida, todavía iterando)
Final   →  AZ (preparado para presentar)
```

El switch preserva las posiciones y los edges. Solo cambian los iconos. Si tu equipo se queja de "el icono no está", probablemente esté en T28 — switcheá a AZ.

## Cloud sync — cómo funciona realmente

### Auto-save mechanism

```text
1. Usuario edita un nodo (mueve, renombra, crea, borra)
2. El store Zustand emite un cambio
3. useDiagramSync hace signature-based diff:
   sha256(nodes + edges + viewport)
4. Si la signature cambió:
   - Espera 2s (debounce)
   - Si no hubo más cambios en esos 2s → PATCH /api/diagrams/<id>
5. Si la signature NO cambió (drag fantasma, click sin movimiento) → no hace nada
```

**El por qué del 2s debounce**: es el punto sweet entre "sentís que se guardó" y "no spammeás el backend". Más bajo (500ms) genera 4x más writes. Más alto (5s) genera ansiedad de "¿se guardó?".

### Signature-based change detection

El hash incluye:
- Posición de cada nodo (x, y)
- Datos del nodo (label, type, color)
- Conexiones (edges con source/target)
- Viewport (zoom, pan position)

Lo que **no** se trackea:
- Hover states
- Selección (qué nodo está seleccionado)
- Estado UI (panel abierto/cerrado)

Esto evita writes cuando solo estás navegando.

### Multi-diagrama

| Plan       | Diagramas por workspace |
|------------|-------------------------|
| Free       | 1                       |
| Trial PRO  | Ilimitados              |
| PRO        | Ilimitados              |
| Team       | Ilimitados              |
| Enterprise | Ilimitados              |

El dropdown `DiagramSwitcher` en la TopBar:
- Lista los diagramas del workspace activo
- ✓ Marca el actual
- Permite rename inline (click en el nombre)
- "+ New" — crea otro (si tu plan lo permite)
- 🗑 Delete con confirm-on-second-click

### Workspaces + diagramas

Los diagramas son scoped al workspace. Si cambiás de workspace, los diagramas cambian. Si eliminás el workspace, los diagramas se eliminan (cascada FK).

[Ver Workspaces](/learn/conceptos/workspaces) para la estrategia de cuándo crear cuántos.

## Patrones canónicos — templates ready-to-customize

Estos son los patterns más usados. Hoy tenés que armarlos a mano — en Q3 2026 vienen como templates one-click.

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

Referencia oficial: [Hub-Spoke network topology](https://learn.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)

### Pattern 2: Landing Zone (CAF-aligned)

```text
Management Group: Tenant Root
  └─ Platform MG
       ├─ Identity sub      (Entra ID Connect, AD DS)
       ├─ Management sub    (Log Analytics, Sentinel)
       └─ Connectivity sub  (Hub VNet, Firewall, ExpressRoute)
  └─ Landing Zones MG
       ├─ Corp LZ           (workloads internos)
       └─ Online LZ         (workloads expuestos a internet)
  └─ Sandbox MG
       └─ Sandbox subs      (PoCs, experiments)
```

Cada LZ es típicamente 1 subscription en este esquema. Decidí esto antes de empezar a deployar.

Referencia oficial: [CAF Landing Zone](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/)

### Pattern 3: AKS deployment con private endpoints

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

### Pattern 4: Static Web App con Function backend

```text
┌─ RG: rg-portal-prod-eus ─────────────────────┐
│ Static Web App: stapp-portal-prod-eus-001    │
│   └─ Custom domain: portal.empresa.com       │
│                                              │
│ Function App: func-portal-api-prod-eus-001   │
│   └─ Storage: stportalprodeusxxx (state)     │
│   └─ Linked to Cosmos: cosmos-portal-prod    │
└──────────────────────────────────────────────┘
```

Simple por arriba, costo bajísimo. Bueno para portales internos.

## Atajos de teclado y productividad

| Atajo                | Acción                                  |
|----------------------|------------------------------------------|
| `Delete` / `Backspace` | Eliminar selección                     |
| `Ctrl + Z`           | Undo                                    |
| `Ctrl + Y`           | Redo                                    |
| `Ctrl + A`           | Seleccionar todo                        |
| `Ctrl + C` / `Ctrl + V` | Copy / paste (en roadmap)            |
| `Click + drag`       | Mover nodo                              |
| `Shift + click`      | Multi-selección                         |
| `Click derecho`      | Menú contextual                         |
| `Scroll wheel`       | Zoom in/out                             |
| `Middle click + drag`| Pan del canvas                          |
| `Drag desde handle`  | Crear edge entre nodos                  |

### Tips de productividad

1. **Usá Shift + click para multi-select** y aplicá cambios en bulk (mover, eliminar)
2. **El canvas tiene infinite scroll** — no hay "borde". Si te perdés, hay un fit-view button en el bottom-right
3. **Para alinear nodos a mano**: arrastrá uno cerca de otro y mirá las coordenadas (eventualmente snap real, hoy no)
4. **Para diagramas grandes**: agrupá visualmente con boundaries y minimizá zoom out — no hay "groups" nativos pero las boundaries cumplen

## Export — PNG, SVG, futuro Bicep

### Hoy disponible

| Formato | Cuándo conviene                          | Cómo                         |
|---------|------------------------------------------|------------------------------|
| **PNG** | Slides, docs, screenshots, slack         | Botón Export → PNG           |
| **SVG** | Editar después en Illustrator/Figma      | Botón Export → SVG           |

El PNG sale con bg `#050d1f` (dark) por default. El SVG es transparente.

### En roadmap (no disponible aún)

- **Export Bicep** — generar template ARM/Bicep a partir del diagrama (Q1 2027)
- **Export Terraform** — idem para HCL (Q1 2027)
- **Export draw.io** — para abrir en draw.io desktop / portal Microsoft (Q3 2026)
- **Export Mermaid** — para embeber en README.md (Q3 2026)

No te prometemos export inverso (Bicep → AzDraw) — la complejidad de parser ARM templates es 10x más que el export.

## Integración con otros tools del Hub

### Con AzNamer

Hoy: cuando dibujás un VNet en AzDraw, sabés que debería llamarse `vnet-<workload>-<env>-<region>-<instance>`. **El nombre lo escribís a mano**.

Q4 2026: botón "Generate names" en AzDraw que toma todos los nodos del canvas y aplica la convención AzNamer del workspace automáticamente. Hoy todavía es manual.

### Con AzNet

Hoy: AzNet (subnet planner) y AzDraw son independientes. Diseñás tus subnets en AzNet, las dibujás manualmente en AzDraw.

Roadmap: import desde AzNet (Q2 2027). Tomás el plan de subnets, AzDraw los crea como subnet nodes dentro del VNet correcto.

### Con AzReview

Hoy: AzReview pregunta cosas como "¿Tu diagrama incluye separation of concerns por subnet?". Vos respondés sí/no. **No verifica contra el diagrama real**.

Roadmap: AzReview lee la estructura de AzDraw (en el mismo workspace) y auto-valida algunos criterios — los preguntables se vuelven verificables (Q3 2026).

### Con AzArch (WAF Scoring)

Similar a AzReview: AzArch tiene un módulo de scoring contra Well-Architected. Hoy es heurístico contra inputs manuales. Roadmap: ata el diagrama de AzDraw como input.

## Casos de uso típicos

### Caso 1: ADR para cambio de arquitectura

**Situación**: Migrás de VMs a AKS. Necesitás documentar el "antes / después" para el ADR.

**Workflow**:
1. Diagrama "antes" en T28 (rápido)
2. Switch a AZ (final look)
3. Duplicar diagrama (rename "Después")
4. Editar el duplicado con la nueva arquitectura
5. Export PNG de ambos
6. Embeber en el ADR markdown del repo

### Caso 2: Proposal para cliente

**Situación**: Pre-sales call, querés mostrar cómo se vería la arquitectura propuesta.

**Workflow**:
1. Diagrama directo en AZ (cliente espera iconos Microsoft oficiales)
2. Usá un workspace dedicado por cliente (separación visual)
3. Export PNG para el deck
4. Export SVG si querés que el sales team lo customize en Figma después

### Caso 3: Documentación de runbook

**Situación**: Onboarding de nuevo engineer. Necesitás explicar "esto es nuestro stack productivo".

**Workflow**:
1. Diagrama Nivel 3 (con boundaries, naming CAF, flujos)
2. Export PNG en 2x (mejor para read)
3. Embeber en el handbook
4. **Importante**: re-export trimestral. Un diagrama no-actualizado es worse que no tener diagrama.

## Pitfalls — las 12 que más vi

| #  | Pitfall                                    | Síntoma                            | Fix                                    |
|----|--------------------------------------------|-------------------------------------|----------------------------------------|
| 1  | Nodos sin labels                          | "¿Qué es esto?"                    | Doble-click → editá el label           |
| 2  | Boundaries no encierran a sus hijos       | Los hijos no se mueven con el RG  | Drag los hijos dentro del boundary    |
| 3  | Iconos T28 en diagrama final              | Look "tech demo", no profesional  | Switch palette a AZ                    |
| 4  | Mezclar varios envs en un diagrama        | "Dev y Prod en la misma vista"    | 1 diagrama por env                    |
| 5  | Edges sin etiqueta                         | "¿Es HTTP? HTTPS? gRPC?"          | Double-click en el edge para labelar  |
| 6  | Demasiados nodos en un canvas             | Diagrama ilegible a >50 nodos     | Split en sub-diagramas conectados     |
| 7  | Subnet fuera de su VNet                   | Semántica visual rota              | Drag la subnet dentro del VNet        |
| 8  | Multi-región en un solo canvas plano       | Imposible entender lo que es donde | Boundaries por región explícitos      |
| 9  | Nombres genéricos ("VM1", "Database")     | Útil 0 después de 2 semanas       | CAF naming en todos los labels         |
| 10 | No exportar nunca                          | El diagrama muere al cerrar tab   | Auto-save te cubre, pero export para versionar |
| 11 | Editar el diagrama "vivo" sin backup      | Un mal click sin Ctrl+Z = perder  | Duplicar antes de cambios grandes     |
| 12 | Olvidar actualizar después de un cambio   | Diagrama de hace 6 meses          | Re-review trimestral del diagrama     |

## Cuándo NO usar AzDraw

AzDraw no es la herramienta correcta para:

- **Diagramas de flujo de negocio** (BPMN, UML actividades) → BPMN.io o Lucid
- **Diagramas de secuencia / clase** (UML técnico) → PlantUML, Mermaid
- **Mind maps** → Miro, MindMeister
- **Workshops sincrónicos remotos** (multi-cursor en vivo) → Miro
- **Multi-cloud strategy diagrams** → draw.io con stencils customizados
- **Wireframes / mockups UI** → Figma, Sketch

Si la mitad de tus diagramas son los de arriba, AzDraw no te aporta lo suficiente. Si el 80% son arquitecturas Azure, te aporta mucho.

## Roadmap honesto

**Q3 2026:**
- Export draw.io (.drawio)
- Export Mermaid
- Templates one-click (Hub-Spoke, Landing Zone, AKS)
- Validation: detectar combinaciones inválidas de boundaries

**Q4 2026:**
- Integración con AzNamer (generate names automático)
- Copy/paste con clipboard
- Layout auto (dagre) para reorganizar diagramas legacy

**Q1 2027:**
- Export Bicep / Terraform (generación de IaC desde el diagrama)
- Multi-cursor collaborative (estilo Figma) — solo Team/Enterprise tiers

**No prometido (todavía):**
- Real-time collaboration tipo Figma
- Versioning / branching de diagramas
- AI-assisted "dibujá esto en CAF para mí"

## Llevatelo a tu IA

```markdown
# AzDraw — best practices

## Workflow recomendado
1. T28 palette para iterar fast
2. Boundaries primero (Sub → RG → VNet)
3. Recursos drag-and-drop dentro de boundaries
4. Labels con CAF naming completo
5. Edges con dirección + protocolo
6. Switch a AZ palette antes de exportar

## Anti-patterns
- Boundaries que no encierran a sus hijos
- Mezclar envs en un diagrama
- Iconos T28 en diagrama final
- Nodos sin label / labels genéricos
- Diagramas que no se actualizan

## Patrones canónicos
- Hub-Spoke (referencia: learn.microsoft.com)
- Landing Zone (CAF)
- AKS con private endpoints
- Static Web App + Function backend

## Export
PNG → para slides, docs
SVG → para edit después en Figma/Illustrator
Bicep/Terraform → roadmap Q1 2027

## Multi-diagrama
Free: 1 por workspace
PRO/Trial: ilimitado
```

## Fuentes oficiales y referencias

- **[Azure Architecture Icons](https://learn.microsoft.com/azure/architecture/icons/)** — los iconos AZ palette son estos
- **[CAF — Landing Zones](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/)** — semántica de boundaries
- **[Hub-Spoke Reference Architecture](https://learn.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)** — el pattern más usado

### Articles relacionados en esta KB

- [AzDraw · concise](/learn/herramientas/azdraw) — la versión 5 minutos
- [Workspaces](/learn/conceptos/workspaces) — cómo separar diagramas por cliente / proyecto
- [AzNamer · deep dive](/learn/herramientas/aznamer-deep-dive) — la convención de naming que usan los labels del diagrama
