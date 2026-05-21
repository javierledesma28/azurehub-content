---
title: ENS Alto — diferencias con Medio y endurecimiento de controles
description: ENS categoría Alta es un superset estricto de Medio. Mismos 73 códigos, pero ~16 controles suben de severidad y exigen evidencia más fuerte. Acá te listamos cuáles cambian.
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

## Cuándo aplica ENS Alto

ENS Alto aplica a sistemas cuyo impacto puede causar **perjuicio muy grave**:

- Información clasificada (Reservado, Secreto en el sentido del RD).
- Infraestructuras críticas o servicios esenciales (energía, agua, financiero crítico).
- Datos personales sensibles a gran escala (salud nacional, biométricos masivos, etc).

Si tu sistema gestiona uno de los anteriores, **NIS2** también te aplica seguramente. AzCompliance los va a juntar en Phase 3.

## Mismos códigos, distinto rigor

ENS Alto **comparte exactamente los 73 códigos de control** del Anexo II que ENS Medio. La diferencia es la profundidad: donde Medio acepta "MFA en admins", Alto pide "MFA en admins + token físico u OTP no-SMS". Donde Medio acepta "backups", Alto pide "backups + restauración probada anualmente + almacenamiento immutable".

En la práctica, AzCompliance Audit Phase 1a refleja esto subiendo la **severidad** del control (de `high` a `critical`) en los códigos donde Alto endurece, lo que hace que un fallo en esos controles **pese 2x** en el score final.

## Controles que cambian de severidad en Alto

| Código ENS | Control | Medio | Alto |
|---|---|---|---|
| `op.acc.2` | Requisitos de acceso | high | **critical** |
| `op.acc.4` | Gestión de derechos de acceso | high | **critical** |
| `op.exp.4` | Mantenimiento y actualizaciones | high | **critical** |
| `op.exp.5` | Gestión de vulnerabilidades | high | **critical** |
| `op.exp.6` | Protección anti-malware | high | **critical** |
| `op.exp.10` | Protección de registros | high | **critical** |
| `op.cont.2` | Plan de continuidad | high | **critical** |
| `op.mon.1` | Detección de intrusión | high | **critical** |
| `op.mon.3` | Vigilancia | high | **critical** |
| `mp.com.1` | Perímetro seguro | high | **critical** |
| `mp.com.2` | Confidencialidad / TLS | high | **critical** |
| `mp.com.4` | Separación de flujos | high | **critical** |
| `mp.eq.3` | Equipos portátiles | high | **critical** |
| `mp.info.3` | Cifrado de la información | high | **critical** |
| `mp.s.2` | Apps web / WAF | high | **critical** |
| `mp.s.4` | DDoS | high | **critical** |

## Cómo usarlo

Idéntico al flow de ENS Medio: andá a `/azcompliance-audit`, elegí **ENS-ALTO** como framework, elegí scope, correlo. Como el seed comparte códigos con Medio, **podés correr ambos audits en paralelo** y comparar el score — Alto siempre va a ser ≤ que Medio para el mismo tenant.

## Phase 1b — endurecimiento de checks

En Phase 1a los chequeos automáticos son los mismos para Medio y Alto (mismo handler). Phase 1b agrega variantes "estrictas" donde aplica:

- `op.acc.5` (MFA) Alto exigirá que las CA policies usen **`builtInControls: ['mfa', 'compliantDevice']`** además del MFA.
- `op.exp.10` (logs) Alto exigirá retention ≥ 6 meses + immutable storage.
- `op.cont.4` (medios alternativos) Alto exigirá multi-region activo-activo, no solo activo-pasivo.

Las descripciones de cada control en el seed se actualizarán para reflejar el matiz cuando llegue Phase 1b.

## Ver también

- [ENS Medio — audit automatizado](/learn/compliance/ens-medio) — flow general + cobertura automática.
- [Connect Tenant](/learn/connect-tenant/tenant-import) — pre-requisito para correr cualquier audit contra tu tenant.
- [AzPolicy Compare](/learn/connect-tenant/azpolicy-compare) — complementario: drift de policies vs lo diseñado.
