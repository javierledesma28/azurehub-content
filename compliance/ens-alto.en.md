---
title: ENS Alto — differences with Medio and tightened controls
description: ENS HIGH is a strict superset of MEDIUM. Same 73 codes, but ~16 controls move up in severity and demand stronger evidence. Here's exactly which ones change.
category: compliance
slug: ens-alto
order: 20
readMinutes: 5
tags: [Compliance, ENS, Enterprise, Audit]
lastUpdated: 2026-05-21
author: javier
sources:
  - https://www.boe.es/eli/es/rd/2022/05/03/311
---

## When ENS Alto applies

ENS HIGH applies to systems whose impact could cause **very serious harm**:

- Classified information (Restricted, Secret per the RD).
- Critical infrastructures or essential services (energy, water, critical financial).
- Sensitive personal data at scale (national health records, large-scale biometrics, etc).

If your system handles any of the above, **NIS2** likely applies too. AzCompliance will bundle them together in Phase 3.

## Same codes, different rigor

ENS HIGH **shares exactly the 73 control codes** of Annex II with ENS Medio. The difference is depth: where Medio accepts "MFA on admins", Alto requires "MFA on admins + physical token or non-SMS OTP". Where Medio accepts "backups", Alto requires "backups + restore tested yearly + immutable storage".

In practice, AzCompliance Audit Phase 1a reflects this by raising the **severity** of the control (from `high` to `critical`) in the codes where Alto tightens, which makes a failure in those controls **weigh 2x** in the final score.

## Controls that change severity in Alto

| ENS code | Control | Medio | Alto |
|---|---|---|---|
| `op.acc.2` | Access requirements | high | **critical** |
| `op.acc.4` | Access rights management | high | **critical** |
| `op.exp.4` | Maintenance & patches | high | **critical** |
| `op.exp.5` | Vulnerability management | high | **critical** |
| `op.exp.6` | Anti-malware | high | **critical** |
| `op.exp.10` | Log protection | high | **critical** |
| `op.cont.2` | Continuity plan | high | **critical** |
| `op.mon.1` | Intrusion detection | high | **critical** |
| `op.mon.3` | Surveillance | high | **critical** |
| `mp.com.1` | Secure perimeter | high | **critical** |
| `mp.com.2` | Confidentiality / TLS | high | **critical** |
| `mp.com.4` | Flow separation | high | **critical** |
| `mp.eq.3` | Laptops | high | **critical** |
| `mp.info.3` | Information encryption | high | **critical** |
| `mp.s.2` | Web apps / WAF | high | **critical** |
| `mp.s.4` | DDoS | high | **critical** |

## How to use it

Same flow as ENS Medio: go to `/azcompliance-audit`, pick **ENS-ALTO** as framework, pick scope, run it. Since the seed shares codes with Medio, **you can run both audits in parallel** and compare scores — Alto will always be ≤ Medio for the same tenant.

## Phase 1b — tightened checks

In Phase 1a the automatic checks are the same for Medio and Alto (same handler). Phase 1b will add "strict" variants where applicable:

- `op.acc.5` (MFA) Alto will require CA policies to use **`builtInControls: ['mfa', 'compliantDevice']`** beyond plain MFA.
- `op.exp.10` (logs) Alto will require retention ≥ 6 months + immutable storage.
- `op.cont.4` (alternative means) Alto will require active-active multi-region, not just active-passive.

Per-control descriptions in the seed will be updated to reflect the nuance when Phase 1b lands.

## See also

- [ENS Medio — automated audit](/learn/compliance/ens-medio) — general flow + automatic coverage.
- [Connect Tenant](/learn/connect-tenant/tenant-import) — pre-requisite to run any audit against your tenant.
- [AzPolicy Compare](/learn/connect-tenant/azpolicy-compare) — complementary: policy drift vs designed.
