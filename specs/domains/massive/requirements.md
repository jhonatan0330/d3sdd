# Requisitos — Carga Masiva (MAS)

## Funcionales
- R-MAS-001: Procesar ítems de carga masiva de forma incremental (`sincronizeCargaMasivaItem`).
- R-MAS-002: Ejecutar una carga completa desde un archivo (`fileUrl`, `template`).
- R-MAS-003: Administrar ítems y maestros de carga (CRUD vía `cargaMasivaItem`/`cargaMasiva`).

## No funcionales
- NF-MAS-001: Autenticación por `Authorization` (token de sesión).
- NF-MAS-002: Procesamiento idempotente por `itemId` para reintentos de scheduler.
