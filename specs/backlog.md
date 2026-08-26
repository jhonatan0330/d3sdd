# Backlog — Nuevos casos de uso (SDD)

Propuestas de nuevas funcionalidades por dominio, pendientes de priorizar y pasar a su
carpeta de dominio siguiendo el proceso de `README.md`. Cada ítem, al activarse, genera su
`use-cases.md` / `requirements.md` / `design.md` / `tasks.md` y (si toca API) actualiza
`contract/`.

> Estado de priorización: 🔴 alta, 🟡 media, 🟢 baja.

## Authentication

- [ ] **AUTH-NEW-001** 🔴 Logout explícito remoto (`POST /main/cerrarSesion`) — cierra la
  sesión en `usuariosesion_ussp`. (Relacionado T-AUTH-008.)
- [ ] **AUTH-NEW-002** 🔴 Refresh token / renovación de JWT antes de expirar.
- [ ] **AUTH-NEW-003** 🟡 "Ingresar con Google" en la SPA (botón + flujo). (Relacionado T-AUTH-016.)
- [ ] **AUTH-NEW-004** 🟡 MFA / segundo factor para usuarios admin.
- [ ] **AUTH-NEW-005** 🟢 Bloqueo por intentos fallidos de login.

## Documents (pedido/venta, expedientes)

- [ ] **DOC-NEW-001** 🔴 Endpoint de consulta paginada de documentos estandarizado para la SPA
  (`/document/getDocuments` ya existe — documentar contrato y casos de uso).
- [ ] **DOC-NEW-002** 🟡 Historial de cambios de estado (auditoría de `changeState`).
- [ ] **DOC-NEW-003** 🟡 Subida de archivos con validación de tipo/tamaño en backend
  (`/rest/upload` exists — spec formal).
- [ ] **DOC-NEW-004** 🟢 Plantillas de documento dinámicas (motor `neuron`) — documentar el
  contrato de `obtenerCampos`.

## Tasks

- [ ] **TASK-NEW-001** 🟡 Crear/asignar tareas desde la SPA (`TaskRest` existe — spec de casos de uso).
- [ ] **TASK-NEW-002** 🟢 Notificaciones de vencimiento de tarea.

## Accounting (voucher / plan contable)

- [ ] **ACC-NEW-001** 🟡 Preparar/emitir comprobantes (`VoucherController`, `d3.accounting_voucher`) — casos de uso y contrato.
- [ ] **ACC-NEW-002** 🟢 Plan contable y consulta de cuentas (`PlanAccountingController`, `d3.accounting_plan`).

## Massive load

- [ ] **MAS-NEW-001** 🟡 Carga masiva de documentos con validación y reporte de errores
  (`MassiveRest` — spec).
- [ ] **MAS-NEW-002** 🔴 Ajustar carga masiva en el back y crear la interfaz en el front.
  Falta documentarlo y crear los casos de uso primero (ver `domains/massive/`).

## Notifications

- [ ] **NOT-NEW-001** 🟡 Centro de notificaciones en SPA (`notification-center.service.ts`
  existe — definir contrato de suscripción/lectura).

## Config-forms

- [ ] **CFG-NEW-001** 🟢 Gestión de organizaciones, procesos y plantillas (varios `*Service`
  en `configuration-forms`) — spec de casos de uso por subdominio.
- [ ] **CFG-NEW-002** 🔴 Terminar la migración de la configuración del sistema "flex" a Angular
  (pasar todo a Angular).

## Consumo (Units)

- [ ] **CONS-NEW-001** 🟡 Crear funcionalidad para calcular "Units" de consumo (nueva
  funcionalidad, pendiente de especificar casos de uso y contrato).

## Infraestructura / Tenants

- [ ] **INF-NEW-001** 🟡 Probar la creación de tenants (multi-tenancy por catálogo JDBC).

## Notas

- Antes de implementar cualquiera, revisar `contract/api-contract.md` §16 (pendientes de contrato)
  y completar los esquemas OpenAPI faltantes (`UsuarioAutenticacionDTO`, `OrganizacionDTO`,
  `FieldResponse`, etc.).
- Todos los dominios documentados (`authentication`, `documents`, `tasks`, `accounting`,
  `massive`, `notifications`, `config-forms`, `persons`, `authorization`, `document-transition`)
  ya tienen carpeta en `specs/domains/`. Los nuevos ítems de este backlog generan/extienden
  sus `use-cases/requirements/design/tasks` al activarse.
