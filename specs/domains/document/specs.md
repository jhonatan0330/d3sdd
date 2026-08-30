# Specs — Documentos / Expedientes (DOC)

Dominio unificado: documentos, transición de estado y log de transacciones.

## Requisitos

Formato EARS. Referencia: `use-cases-back.md` y `contract.md` §6.

### Requisitos funcionales

#### Documentos / Expedientes
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
- **RF-DOC-008** El sistema *debe* resolver datos base/dependientes de un campo en
  `POST /rest/consultarDatosBase`.
- **RF-DOC-009** El sistema *debe* consultar inventario por producto en
  `GET /document/getInventory/{id}`.
- **RF-DOC-010** El sistema *debe* exponer trazabilidad del documento (`/template/getTrace`,
  `/template/getTraceFields/{documentId}/{transaction}`).

#### Transición de estado
- **R-DT-001** Transicionar el estado de un documento (`changeState`).
- **R-DT-002** Consultar relaciones/traza de un documento gestor.
- **R-DT-003** Consultar campos de traza por transacción.

#### Log de transacciones
- **R-DTX-001** Auditoría de transacciones por documento (`DocumentoTransaccionDTO`).
- **R-DTX-002** Registro de errores de transacción (`TransaccionErrorDTO`).
- **R-DTX-003** Log de traza (`TransaccionLogDTO`).

### Requisitos de calidad

- **RQ-DOC-001** El endpoint `guardarDocumento` *debe* aceptar el header `non-duplicate`
  (id de sesión) para evitar envíos duplicados por doble click.
- **RQ-DOC-002** Toda operación *requerirá* token válido (`Authorization` o `securityToken`);
  excepción: `/document/getDocument` y `/document/saveDocument` hoy reciben token en body
  (legacy → migrar a header).

### Reglas de negocio

- **RN-DOC-001** Multi-tenancy: el tenant se deriva de la sesión; no es parámetro del request.
- **RN-DOC-002** Estados de documento son valores lógicos (`A`/`I` y estados de transición
  definidos por la plantilla).
- **RN-DOC-003** Al guardar, si `llaveTabla` es null se crea; si no, se actualiza.

### No funcionales
- **NF-DT-001** Autenticación por `Authorization` (o `securityToken` en API externa).
- **NF-DT-002** Pertenece a `d3.document_transition` / `d3.document_execution` (ARCH-001).
- **NF-DTX-001** Multi-tenant; consumido por `documents` / `document-transition`.

### Trazabilidad

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

## Diseño

### Paquetes (realizado según ARCH-001)

El código usa la raíz `d3`:

| Módulo | Paquete |
|--------|---------|
| Ejecución de documentos | `d3.document_execution.{domain,application,infrastructure}` |
| Transición de estado | `d3.document_transition.{domain,application,infrastructure}` |
| Log de transacciones | `d3.document_transaction.{domain,application,infrastructure}` |
| Formularios dinámicos | `d3.process_form.{domain,application,infrastructure}` |

Controladores: `APIController` (`/rest`), `DocumentController` (`/document`),
`TemplateController` (`/template`), todos en `d3.document_execution` / `d3.process_form`.

### DTOs

#### Documentos
- `PedidoVentaDTO`, `PedidoVentaFilterDTO`, `PedidoVentaAjusteDTO`.

#### Transición
- `PedidoVentaAjusteDTO`, `DocumentoRelacionGestorFilterDTO`,
  `DocumentoRelacionGestorDTO`, `PedidoVentaCaracteristicaDTO`.

#### Log de transacciones
- `DocumentoTransaccionDTO`, `TransaccionLogDTO`, `TransaccionErrorDTO` y filtros.

### Componentes

#### Transición de estado
- `APIController` (`/rest`): `changeState` (estado del documento).
- `TemplateController` (`/template`): `getTrace`, `getTraceFields` (trazabilidad).

#### Log de transacciones
- `DocumentoTransaccionSvc`, `TransaccionLogSvc`, `TransaccionErrorSvc`.
- Mappers: `DocumentoTransaccionMapper`, `TransaccionLogMapper`, `TransaccionErrorMapper`.

### Flujo de guardado

```
SPA --PedidoVentaDTO--> POST /rest/guardarDocumento (Authorization, non-duplicate?)
        |
        v
CallDocumentCRUD.save (llaveTabla==null) | .update (llaveTabla!=null)
        |
        v
PedidoVentaSvc -> MyBatis -> BD (multi-tenant por TenantContext)
        |
        v
PedidoVentaDTO resumido (estado, mensajes)
```

- `non-duplicate` (session id) se usa para idempotencia (evitar doble envío).
- Al actualizar se pasa `null` como segundo arg en `update` (ver `APIController`).

### Token: header vs body (legacy)

- Estándar: `Authorization: Bearer <token>`.
- Legacy (a migrar): `/document/getDocument` y `/document/saveDocument` reciben el token en el
  body (`String token`). Decisión: unificar a header en Fase C (ver backlog DOC-NEW-003).

### Plantillas y campos dinámicos

- `TemplateController` expone la metadata de plantillas y la resolución de campos
  (`obtenerCampos`, `getFieldData`, `validateLoad`, `getPropertyRelations`).
- El motor de formularios dinámicos (`neuron` en el front) consume estos endpoints para
  renderizar controles (ver `d3_front` `api.service.ts`).

### Multi-tenancy

- El tenant se resuelve desde la sesión (`TenantContext`), no del request. Los mappers MyBatis
  operan dentro del catálogo JDBC del tenant activo.

### Diagrama de consulta de campos (formulario)

```
SPA --PedidoVentaCaracteristicaFilterDTO--> POST /rest/consultarDatosBase
        |
        v
CampoAdaptador.consultarDatosBase -> resuelve valores/dependentes
        |
        v
PedidoVentaCaracteristicaFilterDTO (valores calculados)
```

### Notas
- Complementa `document-transition` (auditoría de `changeState`). Ver backlog DOC-NEW-002.
- Sin controlador REST propio para log de transacciones; registra traza de transacciones de documentos/expedientes.
