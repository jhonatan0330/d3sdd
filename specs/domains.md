# Dominios de negocio — inventario y avance SDD

Este archivo es el **índice maestro** de dominios del ecosistema D3 (front `d3_front` +
back `d3brain`). Refleja el avance de documentación SDD y del manual de usuario.

Leyenda: ✅ completo · 🔧 en curso · ⏳ pendiente · — no aplica

| Dominio | Paquete | Descripción | use-cases back | use-cases front | contract | User-guide | Estado global |
|---------|---------|-------------|:--------------:|:---------------:|:--------:|:----------:|:-------------:|
| [**authentication**](#authentication) | `d3.authentication` | Login, Google, checkToken, cambio/recuperación clave, logout | ✅ | — | ✅ | ✅ (login) | 🔧 |
| [**documents**](#documents) | `d3.document_execution`, `d3.document_transition`, `d3.document_transaction` | Expedientes/pedidos: guardar, consultar, listar, changeState, upload, plantillas | ✅ | — | ✅ | ✅ | 🔧 |
| [**tasks**](#tasks) | `d3.task` | Crear/asignar tareas (`TaskRest`) | ✅ | — | ✅ | ✅ | 🔧 |
| [**accounting**](#accounting) | `d3.accounting_voucher`, `d3.accounting_plan`, `d3.accounting_api` | Comprobantes y plan contable (`VoucherController`, `PlanAccountingController`) | ✅ | — | ✅ | ✅ | ✅ |
| [**massive**](#massive) | `d3.massiveload` | Carga masiva de documentos (`MassiveRest`) | ✅ | — | ✅ | ✅ | ✅ |
| [**notifications**](#notifications) | `d3.notification` | Centro de notificaciones (`NotificationController`) | ✅ | — | ✅ | ✅ | 🔧 |
| [**config-forms**](#config-forms) | `d3.property`, `d3.configuration_file`, `d3.process_form` | Org, procesos, plantillas, servidores (`PropertyController`,`ConfigurationController`) | ✅ | — | ✅ | ✅ | 🔧 |
| [**document-transition**](#document-transition) | `d3.document_transition`, `d3.document_execution` | Cambios de estado de documentos (`APIController`,`TemplateController`) | ✅ | — | ✅ | ✅ | ✅ |
| [**persons**](#persons) | `d3.logisticpymes` | Módulo de personas/usuarios (`UserController`) | ✅ | — | ✅ | ✅ | ✅ |
| [**authorization**](#authorization) | `d3.authorization`, `d3.authentication` | Perfil/autorización/roles/2FA (`UserController` roles) | ✅ | — | ✅ | ✅ | ✅ |
| [**api-external**](#api-external) | `d3.api` | API pública `/api/*` (login/get/send/report) | — (transversal) | — | ✅ | — | ✅ |
| [**fe**](#fe) | `d3.fe` | Facturación electrónica DIAN (`FEController`) | ✅ | — | ✅ | ⏳ | ✅ |
| [**upload**](#upload) | `d3.upload` | Subida/servido de archivos (`FileController`) | ✅ | — | ✅ | ⏳ | ✅ |
| [**webservice**](#webservice) | `d3.webservice` | Web services externos (`WebServiceController`) | ✅ | — | ✅ | ⏳ | ✅ |
| [**process-designer**](#process-designer) | `d3.process_designer` | Diseñador de procesos (`ProcessDesignerController`) | ✅ | — | ✅ | ⏳ | ✅ |
| [**multitenancy**](#multitenancy) | `d3.multitenancy` | Tenants / multi-tenant (`d3.multitenancy`) | ✅ | — | 🔧 | ⏳ | ✅ |
| [**inventory**](#inventory) | `d3.inventory` | Inventario de productos (`d3.inventory`) | ✅ | — | 🔧 | ⏳ | ✅ |
| [**money**](#money) | `d3.money` | Cuentas/movimientos/turnos (`d3.money`) | ✅ | — | 🔧 | ⏳ | ✅ |
| [**tariff**](#tariff) | `d3.tariff` | Tarifas/tarifarios (`d3.tariff`) | ✅ | — | 🔧 | ⏳ | ✅ |
| [**report**](#report) | `d3.report` | Reportes Jasper (`d3.report`) | ✅ | — | 🔧 | ⏳ | ✅ |
| [**mail**](#mail) | `d3.mail` | Correo/plantillas (`d3.mail`) | ✅ | — | 🔧 | ⏳ | ✅ |
| [**homologate**](#homologate) | `d3.homologate` | Homologación (`d3.homologate`) | ✅ | — | 🔧 | ⏳ | ✅ |
| [**document-transaction**](#document-transaction) | `d3.document_transaction` | Log de transacciones (`d3.document_transaction`) | ✅ | — | 🔧 | ⏳ | ✅ |
| [**assistant**](#assistant) | — (front-only) | Asistente de comandos de la SPA (`@doc`, `/módulo`, F9) — **front-only** | — | ✅ | — (reusa documents) | ✅ | 🔧 |
| [**consumption-units**](#consumption-units) | `d3.consumption_units` | Saldo de unidades de consumo, incremento diario, compra MB/GB, descuento por cargas | ⏳ | — | ⏳ | ⏳ | ⏳ |

## Detalle por dominio

### authentication 🔧
- Specs: `specs/domains/authentication/` (use-cases, specs).
- Contract: `/main/*` y JWT/Google documentados en `contract.md`.
- User-guide: `docs/user-guide/login.md`.
- Pendiente: logout remoto, refresh token, Google en SPA (ver backlog).

### documents 🔧
- Specs: `specs/domains/documents/` (use-cases, specs).
- Contract: endpoints `/rest/*`, `/document/*`, `/template/*` documentados en `contract.md`.
- User-guide: `docs/user-guide/documents.md`.
- Pendiente: migrar token en body de `/document/getDocument`/`saveDocument` a header
  (T-DOC-012); auditoría de cambios de estado (DOC-NEW-002).

### tasks 🔧
- Specs: `specs/domains/tasks/` (use-cases, specs).
- Contract: endpoints `/task/*` documentados en `contract.md`.
- User-guide: `docs/user-guide/tasks.md`.

### accounting ✅
- Specs: `specs/domains/accounting/` (use-cases, specs).
- Contract: endpoints agregados en `contract.md`.
- User-guide: `docs/user-guide/accounting.md`.

### massive ✅
- Specs: `specs/domains/massive/` (use-cases, specs).
- Contract: endpoints agregados en `contract.md`.
- User-guide: `docs/user-guide/massive-notifications.md`.

### notifications 🔧
- Specs: `specs/domains/notifications/` (use-cases, specs).
- Contract: endpoints agregados en `contract.md`.
- User-guide: `docs/user-guide/massive-notifications.md`.

### config-forms 🔧
- Specs: `specs/domains/config-forms/` (use-cases, specs).
- Contract: endpoints agregados en `contract.md`.
- User-guide: `docs/user-guide/config-persons.md`.

### document-transition ✅
- Specs: `specs/domains/document-transition/` (use-cases, specs).
- Contract: endpoints agregados en `contract.md`.
- User-guide: `docs/user-guide/auth-transition.md`.

### persons ✅
- Specs: `specs/domains/persons/` (use-cases, specs).
- Contract: endpoints agregados en `contract.md`.
- User-guide: `docs/user-guide/config-persons.md`.

### authorization ✅
- Specs: `specs/domains/authorization/` (use-cases, specs).
- Contract: endpoints agregados en `contract.md`.
- User-guide: `docs/user-guide/auth-transition.md`.

### api-external ✅
- Contract: API pública documentada en `contract.md` y `openapi.yaml`.
- No tiene dominio propio; es transversal.

### fe ✅
- Specs: `specs/domains/fe/` (use-cases, specs).
- Contract: endpoints en `contract.md`.
- Pendiente: user-guide.

### upload ✅
- Specs: `specs/domains/upload/` (use-cases, specs).
- Contract: endpoints en `contract.md`.
- Pendiente: user-guide.

### webservice ✅
- Specs: `specs/domains/webservice/` (use-cases, specs).
- Contract: endpoints en `contract.md`.
- Pendiente: user-guide.

### process-designer ✅
- Specs: `specs/domains/process-designer/` (use-cases, specs).
- Contract: endpoints en `contract.md`.
- Pendiente: user-guide.

### multitenancy ✅
- Specs: `specs/domains/multitenancy/` (use-cases, specs).
- Contract: infraestructura transversal (sin REST propio).
- Pendiente: user-guide, creación de tenants (INF-NEW-001).

### inventory ✅
- Specs: `specs/domains/inventory/` (use-cases, specs).
- Contract: servicio interno (consumido por `documents`, `accounting`).
- Pendiente: user-guide.

### money ✅
- Specs: `specs/domains/money/` (use-cases, specs).
- Contract: servicio interno (consumido por `accounting`).
- Pendiente: user-guide.

### tariff ✅
- Specs: `specs/domains/tariff/` (use-cases, specs).
- Contract: servicio interno (consumido por `inventory`).
- Pendiente: user-guide.

### report ✅
- Specs: `specs/domains/report/` (use-cases, specs).
- Contract: servicio interno + servlet/REST (`/api/getReport`).
- Pendiente: user-guide.

### mail ✅
- Specs: `specs/domains/mail/` (use-cases, specs).
- Contract: servicio interno (consumido por `notifications`).
- Pendiente: user-guide.

### homologate ✅
- Specs: `specs/domains/homologate/` (use-cases, specs).
- Contract: servicio interno (consumido por `accounting`, `tariff`).
- Pendiente: user-guide.

### document-transaction ✅
- Specs: `specs/domains/document-transaction/` (use-cases, specs).
- Contract: servicio interno (consumido por `documents`, `document-transition`).
- Pendiente: user-guide.

### assistant 🔧 (front-only)
- Specs: `specs/domains/assistant/` (use-cases, specs).
- Contract: **no tiene backend propio**; reutiliza `documents` (`POST /document/getDocuments`) y el caché de `TemplateService` (`GET /template/getTemplates/{profile}`).
- User-guide: `docs/user-guide/assistant.md`.
- Nota: el asistente **no es una IA**; es un buscador/navegador de comandos (`@`, `/`).
- Cambio en curso: `MatDialog` → panel lateral derecho (ver `specs.md` D1, `backlog.md` T-ASSISTANT-006).

### consumption-units ⏳
- Specs: `specs/domains/consumption-units/` (use-cases, specs).
- Contract: pendiente de documentar en `contract.md`.
- Pendiente: user-guide.

## Cuenta total de avance

- Dominios con spec completa: **25** (todos documentados).
- Dominios con use-cases back: **24** (todos excepto `consumption-units`).
- Dominios con use-cases front: **1** (`assistant`).
- Dominios con contrato en `contract.md`: todos documentados.
- API externa formalizada: ✅ (`openapi.yaml`).
- Secciones de user-guide: **12** (index, login, navigation, documents, tasks, accounting,
  massive-notifications, config-persons, auth-transition, assistant).
- Backlog consolidado: ver `specs/backlog.md` (tasks + nuevos casos de uso).
