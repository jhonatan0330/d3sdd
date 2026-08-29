# Specs — Transición de Documentos (DT)

## Requisitos

### Funcionales
- R-DT-001: Transicionar el estado de un documento (`changeState`).
- R-DT-002: Consultar relaciones/traza de un documento gestor.
- R-DT-003: Consultar campos de traza por transacción.

### No funcionales
- NF-DT-001: Autenticación por `Authorization` (o `securityToken` en API externa).
- NF-DT-002: Pertenece a `d3.document_transition` / `d3.document_execution` (ARCH-001).

## Diseño

### Paquetes (realizado según ARCH-001)
- `d3.document_transition` (transición de estado)
- `d3.document_execution` (APIController `/rest`)

### Componentes
- `APIController` (`/rest`): `changeState` (estado del documento).
- `TemplateController` (`/template`): `getTrace`, `getTraceFields` (trazabilidad).

### DTOs
- `PedidoVentaAjusteDTO`, `DocumentoRelacionGestorFilterDTO`,
  `DocumentoRelacionGestorDTO`, `PedidoVentaCaracteristicaDTO`.
