# Specs — Contabilidad (ACC)

## Requisitos

### Funcionales
- R-ACC-001: Listar, consultar, crear, actualizar y eliminar comprobantes (vouchers) por catálogo contable.
- R-ACC-002: Generar/recrear comprobantes a partir de documentos del módulo de documentos.
- R-ACC-003: Gestionar rangos de numeración de comprobantes.
- R-ACC-004: Exponer el plan contable: catálogos, cuentas y saldos (`/balance/{catalog}`).
- R-ACC-005: API externa `api_account` para generar comprobantes desde sistemas externos
  autenticados con `x-api-key` (crea un token administrativo interno).
- R-ACC-006: Procesar pila de comprobantes para recalcular saldos (`StackAccountProccessService`).

### No funcionales
- NF-ACC-001: Toda operación usa el header `Authorization` (token de sesión); la API externa
  usa `x-api-key` + `securityToken`.
- NF-ACC-002: Multi-tenant: el tenant se deriva de la sesión, no del request.
- NF-ACC-003: Respuestas de error en formato `SharedApiErrorResponse`.
- NF-ACC-004: Validación de balance: débito = crédito en comprobantes tipo comprobante.
- NF-ACC-005: Transaccionalidad: operaciones de creación/actualización/eliminación son transaccionales.

## Diseño

### Paquetes (realizado según ARCH-001)
- `d3.accounting_voucher` (vouchers)
- `d3.accounting_plan` (plan contable)
- `d3.accounting_api` (API externa de comprobantes)

### Componentes Backend
- `AccountingController` (`/acc`): controlador único que maneja todas las rutas del dominio:
  - **Sección vouchers** (`/acc/voucher/*`): comprobantes manuales y generados.
  - **Sección plan** (`/acc/plan/*`): catálogos, cuentas y saldos.
  - **Sección API** (`/acc/api/*`): fachada externa; valida `x-api-key`, genera token admin y delega a `ApiAccountVoucherService` / `StackAccountProccessService`.

### Componentes Frontend
- `AccountComponent` (`accounting-view/`): vista principal con drawer de catálogos, árbol de cuentas, balance y tabla de comprobantes.
- `ManualFormComponent` (`manual-form/`): diálogo modal para crear/editar comprobantes manuales con formularios reactivos.

### DTOs clave

#### Backend (`d3.accounting.domain`)
- `Voucher`: `{ header: VoucherDTO, records: VoucherLine[] }` — comprobante completo.
- `VoucherDTO`: encabezado del comprobante (key, state, catalog, code, concept, factDate, value, type, document).
- `VoucherLine`: `{ line: AccountRecordDTO, references: AccountRecordAuxiliarDTO[] }` — línea de asiento.
- `AccountRecordDTO`: registro contable (account, positive, negative, note, type, voucher).
- `AccountRecordAuxiliarDTO`: auxiliar de registro (auxiliarType, auxiliarDocumentId, auxiliarCode).
- `ManualDTO`: vista simplificada del comprobante para listados (key, state, catalog, code, concept, factDate, value).
- `VoucherPrepareRequest`: `{ serviceId, documentId }` — para generar comprobante desde documento.
- `VoucherRangeRequest`: `{ templateId, startDate, endDate }` — para gestionar rangos.
- `VoucherRequest`: solicitud de API externa (catalog, concept, factDate, document, lines, type).
- `AccountDTO`: cuenta contable (key, state, catalog, code, name, parent, type, operation, status, wbs, level).
- `CatalogDTO`: catálogo contable (key, state, name, code, initialDate, finalDate, accounts, template).
- `ResultMapDTO`: saldo por cuenta y periodo (key, state, catalog, account, timeFrame, quantity, lastBalance, nextBalance, positive, negative, value).

#### Frontend (`accounting.domain.ts`)
- `CatalogDTO`, `AccountDTO`, `ResultMapDTO`, `ManualDTO`, `Voucher`, `ManualAccountDTO`, `ManualAccountAuxiliarDTO`, `VoucherLine`, `VoucherPrepareRequest`.

### Servicios Backend (`d3.accounting.application`)
- `VoucherCreateService`: crear comprobante manual (valida catálogo, fecha, cuentas, balance, consecutivo).
- `VoucherDeleteService`: eliminar comprobante (marcar inactivo + actualizar pila).
- `VoucherGetService`: obtener comprobante por ID o por documento.
- `VoucherReCreateService`: recrear comprobante desde documento (programa ejecución vía web service).
- `VoucherRangeService`: gestionar rangos (clear: eliminar comprobantes; create: recrear comprobantes en rango).
- `VoucherCalculateService`: recalcular saldos por cuenta y periodo.
- `PlanGetCatalogService`: obtener catálogos activos.
- `PlanGetAccountService`: obtener cuentas por catálogo (con filtro).
- `PlanGetBalanceService`: obtener saldos por catálogo.
- `PlanCreateCatalogService`: crear/validar catálogo y periodo temporal.
- `PlanCreateAccountService`: crear cuentas auxiliares.
- `PrepareTypeToCatalogService`: resolver tipo de comprobante por catálogo.
- `TypeService`: gestión de tipos de comprobante (CRUD básico).
- `ConsecutivoSvc`: asignación de consecutivos a comprobantes.
- `ApiAccountVoucherService`: fachada API externa (valida API key, crea comprobante con líneas y auxiliares).
- `StackAccountProccessService`: procesar pila de comprobantes (recalcular saldos).

### Infraestructura (`d3.accounting.infrastructure`)
- Mappers: `VoucherMapper`, `AccountMapper`, `CatalogMapper`, `ResultMapMapper`, `StackVoucherMapper`, `TimeFrameMapper`, `TypeMapper`, `AccountRecordMapper`, `AccountRecordAuxiliarMapper`.
- Mappers extendidos: `VoucherExtendMapper`, `ResultMapExtendMapper`, `StackVoucherExtendMapper`.
- `CreateCatalogTablesMapper`: mapper de utilidad para DDL dinámico (creación de tablas de catálogo).

### Flujos principales
- **CU-ACC-005**: Documento → `VoucherPrepareRequest` → `VoucherReCreateService.call()` → programa ejecución → `SharedIdResponse`.
- **CU-ACC-010 (API externa)**: `VoucherRequest` + `x-api-key` → `ApiAuthorizeService` → `ApiAccountVoucherService.call()` → `VoucherCreateService.call()` → `SharedIdResponse`.
- **CU-ACC-012 (Pila)**: `StackAccountProccessService.call()` → `stackAvailable()` → marcar completados → `VoucherCalculateService.call()` → recalcular saldos.
