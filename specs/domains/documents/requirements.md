# Requisitos — Documentos / Expedientes (DOC)

Formato EARS. Referencia: `use-cases.md` y `contract/api-contract.md` §6.

## Requisitos funcionales

- **RF-DOC-001** El sistema *debe* permitir crear y editar un documento (`PedidoVentaDTO`)
  vía `POST /rest/guardarDocumento` (o `/document/saveDocument`).
- **RF-DOC-002** El sistema *debe* devolver el documento completo en `POST /rest/consultarDocumento`.
- **RF-DOC-003** El sistema *debe* listar documentos filtrados vía `POST /rest/listarDocumentos`
  y `POST /document/getDocuments`.
- **RF-DOC-004** El sistema *debe* permitir cambiar el estado del documento vía
  `POST /rest/changeState` (`PedidoVentaAjusteDTO`).
- **RF-DOC-005** El sistema *debe* validar reglas previas a la creación vía
  `POST /rest/validateBeforeNew`.
- **RF-DOC-006** El sistema *debe* aceptar la subida de archivos (`MultipartFile`) en
  `POST /rest/upload` y devolver la URL.
- **RF-DOC-007** El sistema *debe* listar plantillas por perfil (`ADMIN`/`READER`/usuario) en
  `GET /template/getTemplates/{profile}`.
- **RF-DOC-008** El sistema *debe* resolver datos base/dependentes de un campo en
  `POST /rest/consultarDatosBase`.
- **RF-DOC-009** El sistema *debe* consultar inventario por producto en
  `GET /document/getInventory/{id}`.
- **RF-DOC-010** El sistema *debe* exponer trazabilidad del documento (`/template/getTrace`,
  `/template/getTraceFields/{documentId}/{transaction}`).

## Requisitos de calidad

- **RQ-DOC-001** El endpoint `guardarDocumento` *debe* aceptar el header `non-duplicate`
  (id de sesión) para evitar envíos duplicados por doble click.
- **RQ-DOC-002** Toda operación *requerirá* token válido (`Authorization` o `securityToken`);
  excepción: `/document/getDocument` y `/document/saveDocument` hoy reciben token en body
  (legacy → migrar a header).

## Reglas de negocio

- **RN-DOC-001** Multi-tenancy: el tenant se deriva de la sesión; no es parámetro del request.
- **RN-DOC-002** Estados de documento son valores lógicos (`A`/`I` y estados de transición
  definidos por la plantilla).
- **RN-DOC-003** Al guardar, si `llaveTabla` es null se crea; si no, se actualiza.

## Trazabilidad

| Requisito | Caso de uso | Contrato |
|-----------|-------------|----------|
| RF-DOC-001 | CU-DOC-001 | `/rest/guardarDocumento` |
| RF-DOC-002 | CU-DOC-002 | `/rest/consultarDocumento` |
| RF-DOC-003 | CU-DOC-003 | `/rest/listarDocumentos`, `/document/getDocuments` |
| RF-DOC-004 | CU-DOC-004 | `/rest/changeState` |
| RF-DOC-005 | CU-DOC-005 | `/rest/validateBeforeNew` |
| RF-DOC-006 | CU-DOC-006 | `/rest/upload` |
| RF-DOC-007 | CU-DOC-007 | `/template/getTemplates/{profile}` |
| RF-DOC-008 | CU-DOC-008 | `/rest/consultarDatosBase` |
| RF-DOC-009 | CU-DOC-009 | `/document/getInventory/{id}` |
| RF-DOC-010 | CU-DOC-010 | `/template/getTrace*` |
