# Casos de uso — Log de transacciones (DOC-TX) (BACKEND)

> Capa backend (d3brain). Contratos/endpoints aquí. Los pasos de UI (si los hubiera) van en use-cases-front.md.

Dominio: `document-transaction`. Paquete `d3.document_transaction`. Módulo de servicios
(sin controlador REST propio; registra traza de transacciones de documentos).

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-DTX-001 | Registrar log de transacción | Sistema | ✅ |
| CU-DTX-002 | Registrar error de transacción | Sistema | ✅ |
| CU-DTX-003 | Consultar transacciones de un documento | Sistema | ✅ |

---

## CU-DTX-001..003
`TransaccionLogSvc`, `TransaccionErrorSvc`, `DocumentoTransaccionSvc` registran y consultan
la traza de transacciones asociadas a documentos/expedientes.

