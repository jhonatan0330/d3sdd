# Casos de uso — Documentos / Expedientes (DOC)

Dominio: `documents` (expedientes = `PedidoVentaDTO`). Contrato asociado:
[`contract/api-contract.md`](../contract/api-contract.md) §6.

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-DOC-001 | Crear / editar documento | Usuario autenticado | ✅ documentado |
| CU-DOC-002 | Consultar documento completo | Usuario autenticado | ✅ |
| CU-DOC-003 | Listar documentos con filtros | Usuario autenticado | ✅ |
| CU-DOC-004 | Cambiar estado del documento | Usuario autenticado | ✅ |
| CU-DOC-005 | Validar antes de crear | Sistema (pre-guardado) | ✅ |
| CU-DOC-006 | Subir archivo adjunto | Usuario autenticado | ✅ |
| CU-DOC-007 | Gestionar plantillas y campos | Usuario / Admin | ✅ |
| CU-DOC-008 | Consultar datos base de un campo | Sistema (formulario) | ✅ |
| CU-DOC-009 | Consultar inventario por producto | Usuario autenticado | ✅ |
| CU-DOC-010 | Trazabilidad del documento | Usuario autenticado | ✅ |

---

## CU-DOC-001 — Crear / editar documento

- **Actor:** Usuario autenticado.
- **Precondición:** Sesión válida; plantilla (`plantilla`) existente.
- **Pasos:**
  1. La SPA arma un `PedidoVentaDTO` (campos/valores) y llama `POST /rest/guardarDocumento`
     (o `POST /document/saveDocument` en flujo legacy) con header `Authorization` y opcional
     `non-duplicate` (id de sesión para evitar duplicados).
  2. Si `llaveTabla` es null → `CallDocumentCRUD.save`; si no → `update`.
  3. Devuelve `PedidoVentaDTO` resumido (nombre, plantilla, llaveTabla, estado, mensajes).
- **Postcondición:** Documento persistido (estado inicial).
- **Errores:** Token inválido → `401`; reglas de negocio → `ServerException`.

## CU-DOC-002 — Consultar documento completo

- **Pasos:** `POST /rest/consultarDocumento` o `POST /document/getDocument` con
  `PedidoVentaFilterDTO { llaveTabla }` → `PedidoVentaDTO` completo.
- **Nota:** `/document/getDocument` recibe el token en el body (`String token`), no en header
  (legacy; migrar a header).

## CU-DOC-003 — Listar documentos con filtros

- **Pasos:** `POST /rest/listarDocumentos` o `POST /document/getDocuments` con
  `PedidoVentaFilterDTO` → `List<PedidoVentaDTO>`.
- **Filtros:** plantilla, código, texto, rango de fechas, paginación.

## CU-DOC-004 — Cambiar estado del documento

- **Pasos:** `POST /rest/changeState` con `PedidoVentaAjusteDTO` (transición de estado) →
  guarda ajuste vía `PedidoVentaAjusteSvc`.
- **Relación:** es el núcleo de `document-transition`.

## CU-DOC-005 — Validar antes de crear

- **Pasos:** `POST /rest/validateBeforeNew` con `PedidoVentaFilterDTO` → valida reglas previas
  a `guardarDocumento`.

## CU-DOC-006 — Subir archivo adjunto

- **Pasos:** `POST /rest/upload` (o `/document/upload`) con `MultipartFile file` y header
  `Authorization`. Devuelve la URL del archivo (`SharedApiErrorResponse.message`).
- **Errores:** archivo vacío → `ServerException`.

## CU-DOC-007 — Gestionar plantillas y campos

- **Pasos:**
  - Listar plantillas: `GET /template/getTemplates/{profile}` (`ADMIN`/`READER`/otro).
  - Obtener campos de una plantilla: `POST /rest/obtenerCampos` o
    `GET /template/getFields?id=`.
- **Ver también:** dominio `config-forms` (creación de plantillas).

## CU-DOC-008 — Consultar datos base de un campo

- **Pasos:** `POST /rest/consultarDatosBase` o `POST /template/getFieldData` con
  `PedidoVentaCaracteristicaFilterDTO` → valores calculados/dependientes.

## CU-DOC-009 — Consultar inventario por producto

- **Pasos:** `GET /document/getInventory/{id}` → `List<ProductoInventarioDTO>`.

## CU-DOC-010 — Trazabilidad del documento

- **Pasos:** `POST /template/getTrace` (gestores) y `GET /template/getTraceFields/{documentId}/{transaction}`.
