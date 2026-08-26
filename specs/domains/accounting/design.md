# Diseño — Contabilidad (ACC)

## Paquetes (objetivo ADR-001)
- `d3.accounting.voucher` ← `com.accounting.voucher`
- `d3.accounting.plan` ← `com.accounting.plan`
- `d3.accounting.api` ← `com.accounting.api`

## Componentes
- `VoucherRest` (`acc/voucher`): orquesta comprobantes manuales y generados.
- `PlanAccountingRest` (`acc/plan`): catálogos, cuentas y saldos.
- `AccountApiRest` (`api_account`): fachada externa; valida `x-api-key`, genera token
  admin y delega a `ApiAccountVoucherService` / `StackAccountProccessService`.

## DTOs clave
- `Voucher`, `VoucherDTO`, `VoucherPrepareRequest`, `VoucherRangeRequest`,
  `AccountDTO`, `CatalogDTO`, `ResultMapDTO`.

## Flujo CU-ACC-005
Documento → `VoucherPrepareRequest` → `VoucherRest.generateVoucher` →
servicio contable → `SharedIdResponse` (id del comprobante).
