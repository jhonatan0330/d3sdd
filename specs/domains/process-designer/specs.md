# Specs — Diseñador de procesos (PD)

## Requisitos

### Funcionales
- R-PD-001: Definir procesos, estados y transiciones (manuales y automáticas).
- R-PD-002: Copiar definiciones de proceso.

### No funcionales
- NF-PD-001: Relacionado con `documents` (transición de estado) y `config-forms`.

## Diseño

### Paquetes (ARCH-001, realizado)
- `d3.process_designer`

### Componentes
- `ProcessDesignerController` (`/process_designer`): `copy`.
- `ProcesoSvc`, `ProcesoEstadoSvc`, `ProcesoTransicionSvc`, `ProcesoTransicionAutomaticaSvc`, `ProcessCopy`.

### DTOs
- `ProcesoDTO`, `ProcesoEstadoDTO`, `ProcesoTransicionDTO`, `ProcesoTransicionAutomaticaDTO` y filtros.
