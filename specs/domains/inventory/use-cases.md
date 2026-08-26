# Casos de uso — Inventario (INV)

Dominio: `inventory`. Paquete `d3.inventory`. Módulo de servicios (sin controlador REST
propio; consumido por `documents`/expedientes).

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-INV-001 | Gestionar productos | Sistema | ✅ |
| CU-INV-002 | Gestión de inventario de producto | Sistema | ✅ |
| CU-INV-003 | Trazabilidad de producto en inventario | Sistema | ✅ |
| CU-INV-004 | Deducciones de producto | Sistema | ✅ |

---

## CU-INV-001/002/003/004
`ProductoSvc`, `ProductoInventarioSvc`, `TrazabilidadProductoInventarioSvc`,
`DeduccionProductoSvc`, `ProductoInventarioDescuentoSvc` gestionan el ciclo de inventario.
