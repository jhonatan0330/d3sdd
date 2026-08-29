# Specs — Tarifas (TARIFF)

## Requisitos

### Funcionales
- R-TAR-001: Catálogo de tarifas (`TarifaDTO`).
- R-TAR-002: Agrupación en tarifarios (`TarifarioDTO`).

### No funcionales
- NF-TAR-001: Usado por `inventory` / `documents` para cálculo de valores.

## Diseño

### Paquetes (ARCH-001, realizado)
- `d3.tariff`

### Componentes
- `TarifarioService`, `TarifaSvc`.
- Mappers: `TarifaMapper`, `TarifarioMapper`.

### DTOs
- `TarifaDTO`, `TarifarioDTO` y filtros.
