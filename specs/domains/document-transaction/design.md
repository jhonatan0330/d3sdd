# Diseño — Log de transacciones (DOC-TX)

## Paquetes (ADR-001, realizado)
- `d3.document_transaction`

## Componentes
- `DocumentoTransaccionSvc`, `TransaccionLogSvc`, `TransaccionErrorSvc`.
- Mappers: `DocumentoTransaccionMapper`, `TransaccionLogMapper`, `TransaccionErrorMapper`.

## DTOs
- `DocumentoTransaccionDTO`, `TransaccionLogDTO`, `TransaccionErrorDTO` y filtros.

## Notas
Complementa `document-transition` (auditoría de `changeState`). Ver backlog DOC-NEW-002.
