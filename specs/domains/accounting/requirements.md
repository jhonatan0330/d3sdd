# Requisitos — Contabilidad (ACC)

## Funcionales
- R-ACC-001: Listar, consultar, crear y eliminar comprobantes (vouchers) por catálogo contable.
- R-ACC-002: Generar/recrear comprobantes a partir de documentos del módulo de documentos.
- R-ACC-003: Gestionar rangos de numeración de comprobantes.
- R-ACC-004: Exponer el plan contable: catálogos, cuentas y saldos (`/balance/{catalog}`).
- R-ACC-005: API externa `api_account` para generar comprobantes desde sistemas externos
  autenticados con `x-api-key` (crea un token administrativo interno).

## No funcionales
- NF-ACC-001: Toda operación usa el header `Authorization` (token de sesión); la API externa
  usa `x-api-key` + `securityToken`.
- NF-ACC-002: Multi-tenant: el tenant se deriva de la sesión, no del request.
- NF-ACC-003: Respuestas de error en formato `SharedApiErrorResponse`.
