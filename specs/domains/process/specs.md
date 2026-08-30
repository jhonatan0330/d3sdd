# Specs — Procesos (PRC)

## Requisitos

### Funcionales
- R-PRC-001: Definir procesos, estados y transiciones (manuales y automáticas).
- R-PRC-002: Copiar definiciones de proceso.

### No funcionales
- NF-PRC-001: Relacionado con `document` (transición de estado) y `configuration`.

## Diseño

### Paquetes (ARCH-001, realizado)
- `d3.process_designer`

### Componentes
- `ProcessDesignerController` (`/process_designer`): `copy`.
- `ProcesoSvc`, `ProcesoEstadoSvc`, `ProcesoTransicionSvc`, `ProcesoTransicionAutomaticaSvc`, `ProcessCopy`.

### DTOs
- `ProcesoDTO`, `ProcesoEstadoDTO`, `ProcesoTransicionDTO`, `ProcesoTransicionAutomaticaDTO` y filtros.
