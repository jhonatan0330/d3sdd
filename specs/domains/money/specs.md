# Specs — Dinero / Cajas (MONEY)

## Requisitos

### Funcionales
- R-MON-001: Catálogo de cuentas (`CuentaDTO`).
- R-MON-002: Movimientos contables de caja (`MovimientoDTO`).
- R-MON-003: Turnos (`TurnoDTO`).

### No funcionales
- NF-MON-001: Multi-tenant.

## Diseño

### Paquetes (ARCH-001, realizado)
- `d3.money`

### Componentes
- `CuentaSvc`, `MovimientoSvc`, `TurnoSvc`.
- Mappers: `CuentaMapper`, `MovimientoMapper`, `TurnoMapper`.

### DTOs
- `CuentaDTO`, `MovimientoDTO`, `TurnoDTO` y filtros.
