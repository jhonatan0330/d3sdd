# Casos de uso — Web Services (WS) (BACKEND)

> Capa backend (d3brain). Contratos/endpoints aquí. Los pasos de UI (si los hubiera) van en use-cases-front.md.

Dominio: `webservice`. Paquete `d3.webservice`. Contrato: §17.

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-WS-001 | Copiar definición de web service | Usuario autenticado | ✅ |
| CU-WS-002 | Ejecutar web service | Sistema | 🔧 |

---

## CU-WS-001 — Copiar
`POST /webservice/copy` → duplica una definición de web service.

## CU-WS-002 — Ejecutar
`WebServiceEjecucionSvc` / `WebServiceExecuteAPI` ejecutan el WS (pendiente documentar
endpoint de ejecución).

