# Casos de uso — Subida de archivos (UPLOAD)

Dominio: `upload`. Paquete `d3.upload`. Contrato: §17.

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-UP-001 | Servir/descargar archivo por ruta | Usuario autenticado | ✅ |
| CU-UP-002 | Cargar archivo | Sistema | 🔧 |

---

## CU-UP-001 — Descargar
`GET /files/{visibility}/{type}/{year}/{month}/{day}/{filename}` → archivo almacenado.

## CU-UP-002 — Cargar
`CargaArchivoSvc` / `UploadSvc` gestionan la carga (consumido internamente por
`/rest/upload`, `/document/upload`).
