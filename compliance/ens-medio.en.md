---
title: ENS Medio — automated audit from Azure Hub
description: How AzCompliance Audit covers the 73 controls of the Spanish ENS Medio (RD 311/2022) framework against your connected Azure tenant. What we check automatically, what stays manual, and how to read the report.
category: compliance
slug: ens-medio
order: 10
readMinutes: 8
tags: [Compliance, ENS, Enterprise, Audit]
lastUpdated: 2026-05-21
author: javier
sources:
  - https://www.boe.es/eli/es/rd/2022/05/03/311
  - https://www.ccn.cni.es/index.php/es/menu-ens-ccn/guias-ens
---

## What AzCompliance Audit does

It audits your connected Azure tenant against the **73 controls of Annex II of Royal Decree 311/2022, category MEDIUM**, and returns for each: status (compliant / non-compliant / partial / N/A / pending manual review), human summary, and raw evidence (the exact ARM or Graph response we evaluated).

The output is **defensible before a CCN-CERT auditor**: you get timestamp, resources checked, raw API response, and you can attach additional evidence (PDF, screenshots) on the manual controls.

> AzCompliance Audit **does not replace the auditor**. It generates the technical evidence; the final regulatory interpretation is done by a certified person.

## What ENS is and why it matters

The **Esquema Nacional de Seguridad** (Spanish National Security Framework) is mandatory for:

- **Spanish public sector** (national + regional administrations, autonomous bodies, state-owned companies).
- **Vendors processing public-sector information** (typically required by tender specifications).
- **MEDIUM and HIGH categories** depending on the impact a security incident would have.

ENS Medio applies when the impact could cause serious but bounded harm: significant loss of availability/integrity/confidentiality, but without national-critical scope.

## Running an audit

1. **Pre-requisite:** the workspace must have an Azure tenant connected (Connect Tenant), and the Service Principal must hold **Reader** at the subscription level for everything you want to audit.
2. Open `/azcompliance-audit` from the **AzCompliance → Audit** button (ENT badge in the top bar).
3. Pick the **ENS-MEDIO** framework.
4. Pick target subscriptions for the scope.
5. Click **Run audit**. Takes 30s–3min depending on tenant size.
6. When it finishes, the dashboard shows the overall score + counts and the list of controls grouped by category.

## Automatic vs manual coverage in Phase 1a

| Type | Count | How it's evaluated |
|---|---|---|
| **Auto** | ~13 controls | Direct check against ARM / Graph |
| **Partial** | ~10 controls | Heuristic + user closes the final verdict |
| **Manual** | ~50 controls | No viable automation (governance, processes, physical) |

### Automatically covered controls

| ENS code | Control | What we check |
|---|---|---|
| `op.acc.1` | Identification | Guest user ratio vs total (Graph `/users`) |
| `op.acc.4` | Least privilege | Owner/Contributor role assignments at subscription scope |
| `op.acc.5` | Authentication / MFA | Conditional Access policies cover members of privileged roles (Global Admin, Privileged Role Admin, Security Admin) |
| `op.exp.1` | Asset inventory | Resources tagged with `Owner`/`CostCenter` (≥90% to pass) |
| `op.exp.8` | Activity logging | Diagnostic Settings enabled on key resources (Storage, KV, SQL, App Service, NSG) |
| `op.exp.11` | Cryptographic keys | Key Vaults with `enableSoftDelete` + `enablePurgeProtection` |
| `op.mon.1` | Intrusion detection | Defender for Cloud plans on Standard vs Free tier |
| `mp.com.1` | Secure perimeter | NSG / Azure Firewall / App Gateway present in subscriptions with VNets |
| `mp.com.2` | Confidentiality / TLS | Storage Accounts with `minimumTlsVersion` ≥ TLS 1.2 |
| `mp.com.4` | Flow separation | Storage `supportsHttpsTrafficOnly` + App Service `httpsOnly` |
| `mp.si.3` | Encryption at-rest | SQL Databases with Transparent Data Encryption enabled |
| `mp.s.4` | Denial of service | DDoS Protection Standard on VNets |
| `mp.si.7` | Backups | Recovery Services Vaults with backup items |

## Required permissions

The multi-tenant connector's Service Principal already requests the permissions during the admin-consent flow. Specifically:

- **ARM**: `Reader` at subscription level (or management group). Without this, no ARM check runs.
- **Graph** (Application permissions): `Directory.Read.All`, `Policy.Read.All`, `RoleManagement.Read.All`. If missing, the `op.acc.*` checks return `error` with an explanatory summary — the rest of the audit still runs.

## How the score is computed

Each control has a weight based on severity:

| Severity | Weight |
|---|---|
| critical | 8 |
| high | 4 |
| medium | 2 |
| low | 1 |

Score = **((sum of weights of compliant) + (sum of weights of partial) / 2) / (sum of weights of non-N/A) × 100**.

**Not-applicable** controls are excluded from the divisor. Pending manuals, errors, and non-compliant ones add to the divisor but not the numerator — an audit with many manuals pending may score low even if nothing is technically wrong.

## Manual review

`manual` controls start in `manual-review` pending. To close them:

1. Click the control row → drawer opens on the right.
2. In the **Mark manually** panel pick the verdict (Compliant / Non-compliant / Partial / N/A).
3. (Optional but recommended) write a note explaining context.
4. (Optional) upload evidence: policy PDF, screenshots, contracts... up to 10MB per file, formats PDF/PNG/JPG/TXT/MD.
5. Save.

The system records `userAttested: true` + user id + timestamp in the evidence. The report cleanly distinguishes **auto-verified** vs **user-attested** — the auditor knows what was checked technically vs what was attested by the internal responsible person.

## Re-running the audit

Each run is independent and kept in history. Phase 2 will add schedules to run the audit daily and email drift alerts. Phase 1c will add PDF export of the full report to deliver to the auditor.

## Known Phase 1a limitations

- `Conditional Access` policies using `includeGroups` are not expanded (admin coverage by group is left out of the automatic verdict — use manual review if your org uses groups).
- `op.exp.8` samples 50 resources per subscription to stay fast — large tenants will see "sampled" in the raw evidence.
- Defender for Cloud aggregates multiple plans; the current verdict is binary on "at least one in Standard". Phase 1b will refine by mandatory category (Servers, Databases, KV, ...).

## Difference with ENS Alto

ENS Alto shares the same codes but tightens requirements on ~16 controls (typically raising severity from high to critical). See [ENS Alto — differences with Medio](/learn/compliance/ens-alto).
