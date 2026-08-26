# Casos de uso — Web Services (WS)

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
