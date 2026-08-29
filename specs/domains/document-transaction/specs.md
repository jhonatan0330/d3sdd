# Specs — Log de transacciones (DOC-TX)

## Requisitos

### Funcionales
- R-DTX-001: Auditoría de transacciones por documento (`DocumentoTransaccionDTO`).
- R-DTX-002: Registro de errores de transacción (`TransaccionErrorDTO`).
- R-DTX-003: Log de traza (`TransaccionLogDTO`).

### No funcionales
- NF-DTX-001: Multi-tenant; consumido por `documents` / `document-transition`.

## Diseño

### Paquetes (ARCH-001, realizado)
- `d3.document_transaction`

### Componentes
- `DocumentoTransaccionSvc`, `TransaccionLogSvc`, `TransaccionErrorSvc`.
- Mappers: `DocumentoTransaccionMapper`, `TransaccionLogMapper`, `TransaccionErrorMapper`.

### DTOs
- `DocumentoTransaccionDTO`, `TransaccionLogDTO`, `TransaccionErrorDTO` y filtros.

### Notas
Complementa `document-transition` (auditoría de `changeState`). Ver backlog DOC-NEW-002.
