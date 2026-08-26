# Diseño — Transición de Documentos (DT)

## Paquetes (realizado según ADR-001)
- `d3.document_transition` (transición de estado)
- `d3.document_execution` (APIController `/rest`)

## Componentes
- `APIController` (`/rest`): `changeState` (estado del documento).
- `TemplateController` (`/template`): `getTrace`, `getTraceFields` (trazabilidad).

## DTOs
- `PedidoVentaAjusteDTO`, `DocumentoRelacionGestorFilterDTO`,
  `DocumentoRelacionGestorDTO`, `PedidoVentaCaracteristicaDTO`.
