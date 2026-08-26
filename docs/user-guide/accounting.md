# Contabilidad

Módulo contable: comprobantes (vouchers) y plan contable.

## Comprobantes
- Listar por catálogo: `GET acc/voucher/{catalog}`
- Ver uno: `GET acc/voucher/one/{voucherId}`
- Crear manual: `POST acc/voucher/manual`
- Eliminar manual: `DELETE acc/voucher/manual/{voucherId}`
- Generar desde documento: `POST acc/voucher/generate-voucher`
- Rangos: `POST acc/voucher/range-clear-voucher` / `range-create-voucher`

## Plan contable
- Catálogos: `GET acc/plan/catalog`, `GET acc/plan/catalog/{id}`
- Cuentas: `GET acc/plan/account/{catalog}?filter=`, `GET acc/plan/account/{catalog}/{id}`
- Saldos: `GET acc/plan/balance/{catalog}`

> Toda llamada usa el header `Authorization` (token de sesión).
