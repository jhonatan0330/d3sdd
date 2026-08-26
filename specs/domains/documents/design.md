# Diseño — Documentos / Expedientes (DOC)

Decisiones de diseño del dominio de documentos. Referencia: `use-cases.md`, `requirements.md`,
`contract/api-contract.md` §6 y `specs/decisions/ADR-001-package-rename.md`.

## D1. Paquetes (target según ADR-001)

Aunque hoy el código usa `com.softure.document_execution.*`,
`com.softure.document_transition.*`, `com.softure.process_form.*`, el diseño target es:

| Hoy | Target (ADR-001) |
|-----|------------------|
| `com.softure.document_execution.domain` | `d3.document.execution.domain` |
| `com.softure.document_execution.application` | `d3.document.execution.application` |
| `com.softure.document_transition.application` | `d3.document.transition.application` |
| `com.softure.process_form.application` | `d3.form.application` |
| `com.softure.process_form.infrastructure` | `d3.form.infrastructure` |

Controladores (hoy): `APIController` (`/rest`), `DocumentController` (`/document`),
`TemplateController` (`/template`).

## D2. Flujo de guardado

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

## D3. Token: header vs body (legacy)

- Estándar: `Authorization: Bearer <token>`.
- Legacy (a migrar): `/document/getDocument` y `/document/saveDocument` reciben el token en el
  body (`String token`). Decisión: unificar a header en Fase C (ver backlog DOC-NEW-003).

## D4. Plantillas y campos dinámicos

- `TemplateController` expone la metadata de plantillas y la resolución de campos
  (`obtenerCampos`, `getFieldData`, `validateLoad`, `getPropertyRelations`).
- El motor de formularios dinámicos (`neuron` en el front) consume estos endpoints para
  renderizar controles (ver `d3_front` `api.service.ts`).

## D5. Transición de estado

- `changeState` delega en `PedidoVentaAjusteSvc` (dominio `document-transition`). El ajuste
  registra la transición y los mensajes asociados (`SharedIdResponse.messages`).

## D6. Multi-tenancy

- El tenant se resuelve desde la sesión (`TenantContext`), no del request. Los mappers MyBatis
  operan dentro del catálogo JDBC del tenant activo.

## Diagrama de consulta de campos (formulario)

```
SPA --PedidoVentaCaracteristicaFilterDTO--> POST /rest/consultarDatosBase
        |
        v
CampoAdaptador.consultarDatosBase -> resuelve valores/dependentes
        |
        v
PedidoVentaCaracteristicaFilterDTO (valores calculados)
```
