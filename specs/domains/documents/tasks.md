# Tasks — Documentos / Expedientes (DOC)

Desglose de implementación. Estado: `[ ]` pendiente, `[x]` hecho, `[~]` en curso.

## Backend (d3brain)

- [x] **T-DOC-001** `POST /rest/guardarDocumento` (save/update + `non-duplicate`).
- [x] **T-DOC-002** `POST /rest/consultarDocumento` y `POST /document/getDocument`.
- [x] **T-DOC-003** `POST /rest/listarDocumentos` y `POST /document/getDocuments`.
- [x] **T-DOC-004** `POST /rest/changeState` (transición de estado).
- [x] **T-DOC-005** `POST /rest/validateBeforeNew`.
- [x] **T-DOC-006** `POST /rest/upload` y `POST /document/upload` (MultipartFile).
- [x] **T-DOC-007** `GET /template/getTemplates/{profile}` (ADMIN/READER/usuario).
- [x] **T-DOC-008** `POST /rest/obtenerCampos` y `GET /template/getFields`.
- [x] **T-DOC-009** `POST /rest/consultarDatosBase` y `POST /template/getFieldData`.
- [x] **T-DOC-010** `GET /document/getInventory/{id}`.
- [x] **T-DOC-011** `POST /template/getTrace` y `GET /template/getTraceFields/{documentId}/{transaction}`.
- [ ] **T-DOC-012** (backlog DOC-NEW-003) Migrar `/document/getDocument` y `/document/saveDocument`
  para recibir token en header en vez de body.
- [ ] **T-DOC-013** (backlog DOC-NEW-002) Auditoría/historial de cambios de estado
  (`changeState`).

## Frontend (d3_front)

- [x] **T-DOC-101** `ApiService` cubre: `listarDocumentos`, `consultarDocumento`,
  `validateBeforeNew`, `guardarDocumento`, `saveByMassive`, `consultarDatosBase`, `ajustarEstado`,
  `uploadFile`, `obtenerCampos`, `relacionesPropiedad`, `validarTipoProcesoCarga`,
  `consultarInventario`, `getMessageInFiledProccess`. (ver `api.service.ts`)
- [x] **T-DOC-102** `template.service.ts` consume `getTemplates`, `getFields`.
- [ ] **T-DOC-103** Unificar consumo de `/document/getDocument`/`saveDocument` al estándar de
  header `Authorization` (cuando T-DOC-012 exista).
- [ ] **T-DOC-104** Pantalla/estado de trazabilidad usando `getTrace`/`getTraceFields`.
- [ ] **T-DOC-105** Indicador de idempotencia: enviar `non-duplicate` en guardar (ya lo hace
  `guardarDocumento` en `api.service.ts`; verificar en todas las rutas de guardado).

## Verificación

- Back: `./gradlew.bat build -x test`
- Front: `npm run build` y `npx tsc -p tsconfig.app.json --noEmit`
