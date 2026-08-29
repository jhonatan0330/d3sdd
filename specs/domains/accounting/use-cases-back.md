# Casos de uso — Contabilidad (ACC) (BACKEND)

> Capa backend (d3brain). Contratos/endpoints aquí. Los pasos de UI (si los hubiera) van en use-cases-front.md.

Dominio: `accounting` (comprobantes y plan contable). Contrato:
[`contract.md`](../../contract.md) §9.

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-ACC-001 | Listar comprobantes por catálogo | Usuario autenticado | ✅ |
| CU-ACC-002 | Ver comprobante | Usuario autenticado | ✅ |
| CU-ACC-003 | Crear comprobante manual | Usuario autenticado | ✅ |
| CU-ACC-004 | Eliminar comprobante manual | Usuario autenticado | ✅ |
| CU-ACC-005 | Generar comprobante desde documento | Sistema | ✅ |
| CU-ACC-006 | Obtener id de comprobante por documento | Sistema | ✅ |
| CU-ACC-007 | Gestionar rangos de comprobante | Usuario autenticado | ✅ |
| CU-ACC-008 | Consultar plan contable (catálogos/cuentas/saldos) | Usuario autenticado | ✅ |

---

## CU-ACC-001 — Listar comprobantes por catálogo
`GET acc/voucher/{catalog}` → `List<VoucherDTO>`.

## CU-ACC-002 — Ver comprobante
`GET acc/voucher/one/{voucherId}` → `Voucher`.

## CU-ACC-003 — Crear comprobante manual
`POST acc/voucher/manual` con `Voucher` → `SharedIdResponse`.

## CU-ACC-004 — Eliminar comprobante manual
`DELETE acc/voucher/manual/{voucherId}` → `SharedIdResponse`.

## CU-ACC-005 — Generar comprobante desde documento
`POST acc/voucher/generate-voucher` con `VoucherPrepareRequest` → `SharedIdResponse`
(recrea el comprobante asociado a un documento).

## CU-ACC-006 — Obtener id de comprobante por documento
`POST acc/voucher/document` con `VoucherPrepareRequest` → `SharedIdResponse`.

## CU-ACC-007 — Rangos de comprobante
`POST acc/voucher/range-clear-voucher` y `POST acc/voucher/range-create-voucher`
con `VoucherRangeRequest` → gestionan rangos.

## CU-ACC-008 — Plan contable
`GET acc/plan/catalog` (catálogos), `GET acc/plan/catalog/{id}`,
`GET acc/plan/account/{catalog}?filter=`, `GET acc/plan/account/{catalog}/{id}`,
`GET acc/plan/balance/{catalog}` (saldos).

