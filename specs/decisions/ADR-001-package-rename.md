# ADR-001 — Convención de paquetes Java (com.softure.* → d3.*)

- **Estado:** Aceptado (parcial, pendiente de ejecución en Fase C)
- **Fecha:** 2026-08-26
- **Dominios afectados:** todos (`d3brain`)

## Contexto

El backend usa raíces de paquete inconsistentes: `com.softure.*`, `com.shared.*`,
`com.task.*`, `com.accounting.*`, `com.configuration.*`. No hay un layering de paquete
uniforme y la clase principal vive en `package com` (`Sw42WebApplication`).

Se decidió (ver conversación SDD) **documentar primero y renombrar después**, pero fijar ya
la convención target para que los `design.md` de los dominios la usen y no haya rework.

## Decisión

1. **Raíz única `d3`** (reemplaza `com.softure`, `com.shared`, `com.task`,
   `com.accounting`, `com.configuration`).
2. **Layering por módulo:**
   ```
   d3.<modulo>.domain          # DTO, entidades, @Alias de MyBatis
   d3.<modulo>.application     # Svc (reglas de negocio)
   d3.<modulo>.infrastructure  # Mapper (interfaz + XML) y Controller/Rest
   ```
3. **Mapeo old → new** (propuesto, ajustable en ejecución):

   | Origen | Destino |
   |--------|---------|
   | `com.softure.api` | `d3.api` |
   | `com.softure.authentication` | `d3.auth` |
   | `com.softure.authorization` | `d3.authorization` |
   | `com.task.task` | `d3.task` |
   | `com.accounting.voucher` / `com.accounting.plan` / `com.accounting.api` | `d3.accounting.voucher` / `d3.accounting.plan` / `d3.accounting.api` |
   | `com.configuration.homologate` | `d3.config.homologate` |
   | `com.shared` | `d3.shared` |
   | `com.softure.logisticpymes` | `d3.core` |
   | `com.softure.fe` | `d3.fe` |
   | `com.softure.massiveload` | `d3.massive` |
   | `com.softure.document_transition` / `com.softure.document_execution` | `d3.document.transition` / `d3.document.execution` |
   | `com.softure.process_form` | `d3.form` |
   | `com.softure.property` | `d3.property` |
   | `com.Sw42WebApplication` | `d3.Sw42WebApplication` |

4. La clase principal se mueve a `package d3` y se fija
   `@SpringBootApplication(scanBasePackages = "d3")`; `build.gradle` actualiza
   `mainClass = 'd3.Sw42WebApplication'`.

## Consecuencias

- **Positivas:** estructura uniforme y autoexplicativa; alinea front (`d3_front`) y back
  (`d3brain`) bajo la marca `d3`; facilita escaneo y navegación.
- **Riesgos (Fase C):**
  - 72 mappers XML cuyo `namespace` debe coincidir con el FQN del mapper → reescribir en el
    mismo paso.
  - Actualizar `mainClass` en `build.gradle` y `scanBasePackages`.
  - Posibles referencias FQN en `application.properties` o configuración.
  - Verificación obligatoria: `./gradlew.bat build -x test`.
- **No afecta** el contrato HTTP (`/api/*`, `/main/*`, `/rest/*`, etc.) ni el frontend.

## Estados

- [x] Decisión registrada (este ADR).
- [ ] ADR reflejado en `design.md` de cada dominio (usando nombres target).
- [ ] Ejecución del renombrado (Fase C, ver `specs/backlog.md` tarea técnica).
- [ ] Actualización de `d3brain/AGENTS.md` tras el renombrado.
