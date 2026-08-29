# Specs — Contabilidad (ACC)

## Requisitos

### Funcionales
- R-ACC-001: Listar, consultar, crear y eliminar comprobantes (vouchers) por catálogo contable.
- R-ACC-002: Generar/recrear comprobantes a partir de documentos del módulo de documentos.
- R-ACC-003: Gestionar rangos de numeración de comprobantes.
- R-ACC-004: Exponer el plan contable: catálogos, cuentas y saldos (`/balance/{catalog}`).
- R-ACC-005: API externa `api_account` para generar comprobantes desde sistemas externos
  autenticados con `x-api-key` (crea un token administrativo interno).

### No funcionales
- NF-ACC-001: Toda operación usa el header `Authorization` (token de sesión); la API externa
  usa `x-api-key` + `securityToken`.
- NF-ACC-002: Multi-tenant: el tenant se deriva de la sesión, no del request.
- NF-ACC-003: Respuestas de error en formato `SharedApiErrorResponse`.

## Diseño

### Paquetes (realizado según ARCH-001)
- `d3.accounting_voucher` (vouchers)
- `d3.accounting_plan` (plan contable)
- `d3.accounting_api` (API externa de comprobantes)

### Componentes
- `VoucherController` (`d3.accounting_voucher`, `/acc/voucher`): comprobantes manuales y generados.
- `PlanAccountingController` (`d3.accounting_plan`, `/acc/plan`): catálogos, cuentas y saldos.
- `AccountApiController` (`d3.accounting_api`, `api_account`): fachada externa; valida `x-api-key`,
  genera token admin y delega a `ApiAccountVoucherService` / `StackAccountProccessService`.

### DTOs clave
- `Voucher`, `VoucherDTO`, `VoucherPrepareRequest`, `VoucherRangeRequest`,
  `AccountDTO`, `CatalogDTO`, `ResultMapDTO`.

### Flujo CU-ACC-005
Documento → `VoucherPrepareRequest` → `VoucherController.generateVoucher` →
servicio contable → `SharedIdResponse` (id del comprobante).
