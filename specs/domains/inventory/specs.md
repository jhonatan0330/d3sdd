# Specs — Inventario (INV)

## Requisitos

### Funcionales
- R-INV-001: Catálogo de productos (`ProductoDTO`) y filtros.
- R-INV-002: Movimientos de inventario (`ProductoInventarioDTO`).
- R-INV-003: Trazabilidad e descuentos/deducciones.

### No funcionales
- NF-INV-001: Multi-tenant (vía `d3.multitenancy`).

## Diseño

### Paquetes (ARCH-001, realizado)
- `d3.inventory`

### Componentes
- `ProductoSvc`, `ProductoInventarioSvc`, `TrazabilidadProductoInventarioSvc`,
  `DeduccionProductoSvc`, `ProductoInventarioDescuentoSvc`.
- Mappers: `ProductoMapper`, `ProductoInventarioMapper`, `TrazabilidadProductoInventarioMapper`,
  `DeduccionProductoMapper`, `ProductoInventarioDescuentoMapper`.

### DTOs
- `ProductoDTO`, `ProductoInventarioDTO`, `TrazabilidadProductoInventarioDTO`,
  `DeduccionProductoDTO`, `ProductoInventarioDescuentoDTO` y filtros.
