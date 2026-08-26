# ADR-001 — Convención de paquetes Java (com.softure.* → d3.*)

- **Estado:** Realizado (2026-08-26, tras reorganización de paquetes en `d3brain`)
- **Fecha:** 2026-08-26
- **Dominios afectados:** todos (`d3brain`)

## Contexto

El backend usaba raíces de paquete inconsistentes (`com.softure.*`, `com.shared.*`,
`com.task.*`, `com.accounting.*`, `com.configuration.*`) sin layering uniforme y con la
clase principal en `package com` (`Sw42WebApplication`).

Se decidió (ver conversación SDD) documentar primero y renombrar después, fijando la
convención target. **La ejecución ya ocurrió**: los paquetes ahora viven bajo `d3.*`.

## Decisión

1. **Raíz única `d3`** (reemplaza `com.softure`, `com.shared`, `com.task`,
   `com.accounting`, `com.configuration`).
2. **Layering por módulo:**
   ```
   d3.<modulo>.domain          # DTO, entidades, @Alias de MyBatis
   d3.<modulo>.application     # Svc (reglas de negocio)
   d3.<modulo>.infrastructure  # Mapper (interfaz + XML) y Controller/Rest
   ```
3. **Mapeo old → new (REALIZADO):**

   | Origen | Destino real |
   |--------|--------------|
   | `com.softure.api` | `d3.api` |
   | `com.softure.authentication` | `d3.authentication` |
   | `com.softure.authorization` | `d3.authorization` |
   | `com.task.task` | `d3.task` |
   | `com.accounting.voucher` | `d3.accounting_voucher` |
   | `com.accounting.plan` | `d3.accounting_plan` |
   | `com.accounting.api` | `d3.accounting_api` |
   | `com.configuration.homologate` | `d3.homologate` |
   | `com.shared` | `d3.shared` |
   | `com.softure.logisticpymes` | `d3.logisticpymes` (usuarios/personas) |
   | `com.softure.fe` | `d3.fe` |
   | `com.softure.massiveload` | `d3.massiveload` |
   | `com.softure.document_transition` | `d3.document_transition` |
   | `com.softure.document_execution` | `d3.document_execution` |
   | `com.softure.document_transaction` | `d3.document_transaction` (log de transacciones) |
   | `com.softure.process_form` | `d3.process_form` |
   | `com.softure.property` | `d3.property` |
   | `com.softure.configuration_file` | `d3.configuration_file` |
   | `com.softure.notification` | `d3.notification` |
   | `com.softure.webservice` | `d3.webservice` |
   | `com.softure.upload` | `d3.upload` |
   | `com.softure.process_designer` | `d3.process_designer` |
   | `com.softure.report` | `d3.report` |
   | `com.softure.mail` | `d3.mail` |
   | `com.softure.money` | `d3.money` |
   | `com.softure.tariff` | `d3.tariff` |
   | `com.softure.inventory` | `d3.inventory` |
   | `com.softure.java` | `d3.java` (utilidades base) |
   | `com.softure.multitenancy` | `d3.multitenancy` |
   | `com.Sw42WebApplication` | `d3.Sw42WebApplication` (raíz `d3`) |

4. Renombres de clase relevantes (Rest → Controller):
   - `VoucherRest` → `VoucherController` (`d3.accounting_voucher`)
   - `PlanAccountingRest` → `PlanAccountingController` (`d3.accounting_plan`)
   - `AccountApiRest` → `AccountApiController` (`d3.accounting_api`)
   - `MassiveRest`, `TaskRest`, `NotificationController`, `UserController`, `PropertyController`,
     `ConfigurationController`, `TemplateController`, `ApiController`, `AuthenticationController`
     mantienen su nombre.
5. La clase principal quedó en `package d3` con
   `@SpringBootApplication(scanBasePackages = "d3")`; `build.gradle` usa
   `mainClass = 'd3.Sw42WebApplication'`.

## Inventario de módulos realizados (raíz `d3.*`)

`api`, `authentication`, `authorization`, `task`, `accounting_voucher`, `accounting_plan`,
`accounting_api`, `homologate`, `shared`, `logisticpymes`, `fe`, `massiveload`,
`document_transition`, `document_execution`, `document_transaction`, `process_form`,
`property`, `configuration_file`, `notification`, `webservice`, `upload`, `process_designer`,
`report`, `mail`, `money`, `tariff`, `inventory`, `java`, `multitenancy`.

## Módulos aún sin spec SDD (candidatos a nuevo dominio)

Detectados tras la reorganización y pendientes de documentar como dominio: `fe`
(facturación electrónica), `multitenancy` (tenants), `inventory`, `money`, `tariff`, `report`,
`mail`, `upload`, `process_designer`, `webservice`, `homologate`, `document_transaction`,
`java`/`shared` (utilidades transversales). Ver `specs/backlog.md` (INF-NEW-001 tenants).

## Consecuencias

- **Positivas:** estructura uniforme y autoexplicativa; alinea front (`d3_front`) y back
  (`d3brain`) bajo la marca `d3`; facilita escaneo y navegación.
- **Riesgos (ya mitigados en la ejecución):**
  - 72 mappers XML cuyo `namespace` se reescribió al FQN nuevo.
  - `mainClass` y `scanBasePackages` actualizados a `d3`.
  - Verificación: `./gradlew.bat build -x test` (debe pasar tras el renombrado).
- **No afectó** el contrato HTTP (`/api/*`, `/main/*`, `/rest/*`, etc.) ni el frontend.

## Estados

- [x] Decisión registrada (este ADR).
- [x] ADR reflejado en `design.md` de cada dominio con nombres reales (`d3.*`).
- [x] Ejecución del renombrado (realizada).
- [ ] `d3brain/AGENTS.md` debe actualizarse para reflejar `d3.*` (confirmar).
