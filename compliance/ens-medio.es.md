---
title: ENS Medio — audit automatizado desde Azure Hub
description: Cómo AzCompliance Audit cubre los 73 controles del ENS categoría Media (RD 311/2022) contra tu tenant Azure conectado. Qué chequeamos automático, qué queda manual y cómo se interpreta el reporte.
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

## Qué hace AzCompliance Audit

Audita tu tenant Azure (el que conectaste al workspace) contra los **73 controles del Anexo II del RD 311/2022, categoría Media**, y te devuelve por cada uno: status (cumple / no cumple / parcial / N/A / pendiente revisión manual), summary humano, y evidencia raw (la respuesta ARM o Graph exacta que usamos para evaluar).

El output es **defendible ante un auditor del CCN-CERT**: tenés timestamp, recursos verificados, respuesta cruda del API, y puedes adjuntar evidencia adicional (PDF, capturas) en los controles manuales.

> AzCompliance Audit **no sustituye al auditor**. Genera la evidencia técnica; la interpretación regulatoria final la hace una persona certificada.

## Qué es ENS y por qué importa

El **Esquema Nacional de Seguridad** (ENS) es de obligado cumplimiento para:

- **Sector público español** (administraciones, organismos autónomos, sociedades estatales).
- **Proveedores que tratan información del sector público** (típicamente vía pliegos de licitación).
- **Categorías MEDIA y ALTA** según el impacto que tendría una afectación de la seguridad.

ENS Medio aplica cuando el impacto puede causar perjuicio grave pero limitado: pérdida significativa de disponibilidad/integridad/confidencialidad, pero sin alcance crítico nacional.

## Cómo se ejecuta un audit

1. **Pre-requisito:** el workspace debe tener un tenant Azure conectado (Connect Tenant) y el Service Principal con rol **Reader** a nivel de las suscripciones que quieras auditar.
2. Abrí `/azcompliance-audit` desde el tool **AzCompliance → Audit** (botón ENT en la barra superior).
3. Elegí el framework **ENS-MEDIO**.
4. Elegí las suscripciones objetivo del scope.
5. Click **Correr audit**. Tarda 30s–3min según tamaño del tenant.
6. Cuando termina, se muestra el dashboard con score global + counts y la lista de controles agrupados por categoría.

## Cobertura automática vs manual en Phase 1a

| Tipo | Cantidad | Cómo se evalúa |
|---|---|---|
| **Auto** | ~13 controles | Chequeo directo contra ARM / Graph |
| **Partial** | ~10 controles | Tiene heurística pero el verdict final lo cierra el user |
| **Manual** | ~50 controles | Sin automatización viable (gobernanza, procesos, físico) |

### Controles automáticos cubiertos

| Código ENS | Control | Qué chequeamos |
|---|---|---|
| `op.acc.1` | Identificación | Ratio usuarios Guest vs total (Graph `/users`) |
| `op.acc.4` | Privilegios mínimos | Asignaciones Owner/Contributor a nivel suscripción |
| `op.acc.5` | Autenticación / MFA | Conditional Access policies cubren a los miembros de roles privilegiados (Global Admin, Privileged Role Admin, Security Admin) |
| `op.exp.1` | Inventario de activos | Recursos con tag `Owner`/`CostCenter` (≥90% para cumplir) |
| `op.exp.8` | Registro de actividad | Diagnostic Settings habilitado en recursos clave (Storage, KV, SQL, App Service, NSG) |
| `op.exp.11` | Claves criptográficas | Key Vaults con `enableSoftDelete` + `enablePurgeProtection` |
| `op.mon.1` | Detección de intrusión | Planes Defender for Cloud en tier Standard vs Free |
| `mp.com.1` | Perímetro seguro | NSG / Azure Firewall / App Gateway presentes en suscripciones con VNets |
| `mp.com.2` | Confidencialidad / TLS | Storage Accounts con `minimumTlsVersion` ≥ TLS 1.2 |
| `mp.com.4` | Separación de flujos | Storage `supportsHttpsTrafficOnly` + App Service `httpsOnly` |
| `mp.si.3` | Cifrado at-rest | SQL Databases con Transparent Data Encryption habilitado |
| `mp.s.4` | Denegación de servicio | DDoS Protection Standard en VNets |
| `mp.si.7` | Copias de seguridad | Recovery Services Vaults con backup items asociados |

## Permisos requeridos

El Service Principal del connector multi-tenant ya pide los permisos durante el flow de admin consent. Concretamente:

- **ARM**: `Reader` a nivel suscripción (o management group). Sin esto, ningún check ARM corre.
- **Graph** (Application permissions): `Directory.Read.All`, `Policy.Read.All`, `RoleManagement.Read.All`. Si faltan, los checks `op.acc.*` devuelven `error` con summary explicativo — el resto del audit sigue corriendo.

## Cómo se calcula el score

Cada control tiene un peso según severidad:

| Severidad | Peso |
|---|---|
| critical | 8 |
| high | 4 |
| medium | 2 |
| low | 1 |

El score es **((suma de pesos de cumplen) + (suma de pesos de parciales) / 2) / (suma de pesos no-N/A) × 100**.

Los controles **no aplicables** se excluyen del divisor. Los manuales pendientes, errores, y no cumplen suman al divisor pero no al numerador — por eso un audit con muchos manuales por marcar puede tener score bajo aunque no haya nada técnicamente mal.

## Revisión manual

Los controles `manual` arrancan en estado `manual-review` pendiente. Para cerrarlos:

1. Click en la fila del control → se abre el drawer derecho.
2. En el panel **Marcar manualmente** elegí el verdict (Cumple / No cumple / Parcial / N/A).
3. (Opcional pero recomendado) escribí una nota explicando el contexto.
4. (Opcional) subí evidencia: PDF de la política, capturas, contratos... hasta 10MB por archivo, formatos PDF/PNG/JPG/TXT/MD.
5. Guardar.

El sistema registra `userAttested: true` + el user id + timestamp en la evidencia. El reporte distingue claramente **auto-verified** vs **user-attested** — el auditor sabe qué se chequeó técnicamente y qué fue atestado por el responsable interno.

## Re-correr el audit

Cada run es independiente y queda histórico. Phase 2 agregará schedules para correr el audit diariamente y enviar email con drift cuando algo se rompe. Phase 1c agregará export PDF del reporte completo para entregar al auditor.

## Limitaciones conocidas en Phase 1a

- `Conditional Access` con `includeGroups` no se expande (la cobertura de admins por grupo queda fuera del verdict automático — usá manual review si tu org usa grupos).
- `op.exp.8` muestrea 50 recursos por suscripción para no demorar — tenants grandes verán "muestreado" en el evidence raw.
- Defender for Cloud agrega múltiples planes; el verdict actual es binario sobre "hay al menos uno en Standard". Phase 1b refinará por categoría obligatoria (Servers, Databases, KV, ...).

## Diferencia con ENS Alto

ENS Alto comparte los mismos códigos pero endurece requisitos en ~16 controles (típicamente subiendo severidad de high a critical). Verlo en [ENS Alto — diferencias con Medio](/learn/compliance/ens-alto).
