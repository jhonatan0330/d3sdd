# Diseño — Contabilidad (ACC)

## Paquetes (realizado según ADR-001)
- `d3.accounting_voucher` (vouchers)
- `d3.accounting_plan` (plan contable)
- `d3.accounting_api` (API externa de comprobantes)

## Componentes
- `VoucherController` (`d3.accounting_voucher`, `/acc/voucher`): comprobantes manuales y generados.
- `PlanAccountingController` (`d3.accounting_plan`, `/acc/plan`): catálogos, cuentas y saldos.
- `AccountApiController` (`d3.accounting_api`, `api_account`): fachada externa; valida `x-api-key`,
  genera token admin y delega a `ApiAccountVoucherService` / `StackAccountProccessService`.

## DTOs clave
- `Voucher`, `VoucherDTO`, `VoucherPrepareRequest`, `VoucherRangeRequest`,
  `AccountDTO`, `CatalogDTO`, `ResultMapDTO`.

## Flujo CU-ACC-005
Documento → `VoucherPrepareRequest` → `VoucherController.generateVoucher` →
servicio contable → `SharedIdResponse` (id del comprobante).
