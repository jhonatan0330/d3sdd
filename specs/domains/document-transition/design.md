# Diseño — Transición de Documentos (DT)

## Paquetes (objetivo ADR-001)
- `d3.document.transition` ← `com.softure.document_transition`
- `d3.document.execution` ← `com.softure.document_execution` (APIController `/rest`)

## Componentes
- `APIController` (`/rest`): `changeState` (estado del documento).
- `TemplateController` (`/template`): `getTrace`, `getTraceFields` (trazabilidad).

## DTOs
- `PedidoVentaAjusteDTO`, `DocumentoRelacionGestorFilterDTO`,
  `DocumentoRelacionGestorDTO`, `PedidoVentaCaracteristicaDTO`.
