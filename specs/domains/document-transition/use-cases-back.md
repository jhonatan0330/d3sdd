# Casos de uso — Transición de Documentos (DT) (BACKEND)

> Capa backend (d3brain). Contratos/endpoints aquí. Los pasos de UI (si los hubiera) van en use-cases-front.md.

Dominio: `document-transition` (cambio de estado + trazabilidad).
Contrato: §15. (Base de documentos en `documents`.)

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-DT-001 | Cambiar estado de documento | Usuario/Sistema | ✅ |
| CU-DT-002 | Traza de relaciones del documento | Usuario autenticado | ✅ |
| CU-DT-003 | Campos de traza por transacción | Usuario autenticado | ✅ |

---

## CU-DT-001 — Cambiar estado
`POST rest/changeState` con `PedidoVentaAjusteDTO` → `PedidoVentaAjusteDTO`.

## CU-DT-002 — Traza de relaciones
`POST template/getTrace` con `DocumentoRelacionGestorFilterDTO` →
`List<DocumentoRelacionGestorDTO>`.

## CU-DT-003 — Campos de traza
`GET template/getTraceFields/{documentId}/{transaction}` →
`List<PedidoVentaCaracteristicaDTO>`.

