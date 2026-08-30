# Dominios de negocio — inventario y avance SDD

Este archivo es el **índice maestro** de dominios del ecosistema D3 (front `d3_front` +
back `d3brain`). Refleja el avance de documentación SDD y del manual de usuario.

Leyenda: ✅ completo · 🔧 en curso · ⏳ pendiente · — no aplica

| Dominio | Descripción | use-cases back | use-cases front | contract | User-guide | Estado global |
|---------|-------------|:--------------:|:---------------:|:--------:|:----------:|:-------------:|
| [**authentication**](#authentication) | Login, Google, checkToken, cambio/recuperación clave, logout | ✅ | — | ✅ | ✅ (login) | 🔧 |
| [**document**](#document) | Expedientes/pedidos: guardar, consultar, listar, changeState, upload, plantillas, transición, log transacciones | ✅ | — | ✅ | ✅ | 🔧 |
| [**tasks**](#tasks) | Crear/asignar tareas (`TaskRest`) | ✅ | — | ✅ | ✅ | 🔧 |
| [**accounting**](#accounting) | Comprobantes y plan contable (`VoucherController`, `PlanAccountingController`) | ✅ | — | ✅ | ✅ | ✅ |
| [**massiveload**](#massiveload) | Carga masiva de documentos (`MassiveController`) | ✅ | ✅ | ✅ | ✅ | ✅ |
| [**notification**](#notification) | Centro de notificaciones (`NotificationController`) | ✅ | — | ✅ | ✅ | 🔧 |
| [**configuration**](#configuration) | Configuración de instancia, propiedades, formularios dinámicos, homologación | ✅ | — | ✅ | ✅ | 🔧 |
| [**users**](#users) | Módulo de usuarios/personas (`UserController`) | ✅ | — | ✅ | ✅ | ✅ |
| [**authorization**](#authorization) | Perfil/autorización/roles/2FA (`UserController` roles) | ✅ | — | ✅ | ✅ | ✅ |
| [**api-external**](#api-external) | API pública `/api/*` (login/get/send/report) | — (transversal) | — | ✅ | — | ✅ |
| [**fe**](#fe) | Facturación electrónica DIAN (`FEController`) | ✅ | — | ✅ | ⏳ | ✅ |
| [**upload**](#upload) | Subida/servido de archivos (`FileController`) | ✅ | — | ✅ | ⏳ | ✅ |
| [**webservice**](#webservice) | Web services externos (`WebServiceController`) | ✅ | — | ✅ | ⏳ | ✅ |
| [**process**](#process) | Diseñador de procesos (`ProcessDesignerController`) | ✅ | — | ✅ | ⏳ | ✅ |
| [**multitenancy**](#multitenancy) | Tenants / multi-tenant | ✅ | — | 🔧 | ⏳ | ✅ |
| [**inventory**](#inventory) | Inventario de productos | ✅ | — | 🔧 | ⏳ | ✅ |
| [**money**](#money) | Cuentas/movimientos/turnos | ✅ | — | 🔧 | ⏳ | ✅ |
| [**tariff**](#tariff) | Tarifas/tarifarios | ✅ | — | 🔧 | ⏳ | ✅ |
| [**report**](#report) | Reportes Jasper | ✅ | — | 🔧 | ⏳ | ✅ |
| [**mail**](#mail) | Correo/plantillas | ✅ | — | 🔧 | ⏳ | ✅ |
| [**assistant**](#assistant) | Asistente de comandos de la SPA (`@doc`, `/módulo`, F9) — **front-only** | — | ✅ | — (reusa document) | ✅ | 🔧 |
| [**consumption-units**](#consumption-units) | Saldo de unidades de consumo, incremento diario, compra MB/GB, descuento por cargas | ⏳ | — | ⏳ | ⏳ | ⏳ |
| [**shared**](#shared) | Objetos transversales: DTOs, servicios utilitarios, interceptores, componentes reutilizables | ✅ | ✅ | — | — | ✅ |
| [**layout**](#layout) | Shell de la interfaz: header, sidebar, footer, navegación, dashboard — **front-only** | — | ✅ | — | ✅ | ✅ |

## Detalle por dominio

### authentication 🔧
- Specs: `specs/domains/authentication/` (use-cases, specs).
- Contract: `/main/*` y JWT/Google documentados en `contract.md`.
- User-guide: `docs/user-guide/login.md`.
- Pendiente: logout remoto, refresh token, Google en SPA (ver backlog).

### document 🔧
- Specs: `specs/domains/document/` (use-cases, specs).
- Contract: endpoints `/rest/*`, `/document/*`, `/template/*` documentados en `contract.md`.
- User-guide: `docs/user-guide/documents.md`.
- Incluye: documentos/expedientes, transición de estado y log de transacciones (antes en `documents`, `document-transition`, `document-transaction`).
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

### massiveload ✅
- Specs: `specs/domains/massiveload/` (use-cases, specs).
- Contract: endpoints agregados en `contract.md`.
- User-guide: `docs/user-guide/massive-notifications.md`.

### notification 🔧
- Specs: `specs/domains/notification/` (use-cases, specs).
- Contract: endpoints agregados en `contract.md`.
- User-guide: `docs/user-guide/massive-notifications.md`.

### configuration 🔧
- Specs: `specs/domains/configuration/` (use-cases, specs).
- Contract: endpoints agregados en `contract.md`.
- User-guide: `docs/user-guide/config-persons.md`.
- Incluye: configuración de instancia, propiedades, formularios dinámicos y homologación (antes en `config-forms` y `homologate`).

### users ✅
- Specs: `specs/domains/users/` (use-cases, specs).
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

### process ✅
- Specs: `specs/domains/process/` (use-cases, specs).
- Contract: endpoints en `contract.md`.
- Pendiente: user-guide.

### multitenancy ✅
- Specs: `specs/domains/multitenancy/` (use-cases, specs).
- Contract: infraestructura transversal (sin REST propio).
- Pendiente: user-guide, creación de tenants (INF-NEW-001).

### inventory ✅
- Specs: `specs/domains/inventory/` (use-cases, specs).
- Contract: servicio interno (consumido por `document`, `accounting`).
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
- Contract: servicio interno (consumido por `notification`).
- Pendiente: user-guide.

### assistant 🔧 (front-only)
- Specs: `specs/domains/assistant/` (use-cases, specs).
- Contract: **no tiene backend propio**; reutiliza `document` (`POST /document/getDocuments`) y el caché de `TemplateService` (`GET /template/getTemplates/{profile}`).
- User-guide: `docs/user-guide/assistant.md`.
- Nota: el asistente **no es una IA**; es un buscador/navegador de comandos (`@`, `/`).
- Cambio en curso: `MatDialog` → panel lateral derecho (ver `specs.md` D1, `backlog.md` T-ASSISTANT-006).

### consumption-units ⏳
- Specs: `specs/domains/consumption-units/` (use-cases, specs).
- Contract: pendiente de documentar en `contract.md`.
- Pendiente: user-guide.

### shared ✅
- Specs: `specs/domains/shared/` (specs).
- No tiene endpoints REST; objetos transversales.
- Front: `src/app/shared/` — componentes, servicios, interceptors, guards.
- Backend: `d3.shared/` — DTOs, servicios utilitarios, configuración base.

### layout ✅ (front-only)
- Specs: `specs/domains/layout/` (specs).
- **Front-only**: no tiene backend propio.
- Front: `src/app/layout/` — shell, sidebar, dashboard, navegación.
- Reutiliza endpoints de `document`, `configuration` y `notification` para datos del dashboard.

## Cuenta total de avance

- Dominios con spec completa: **23** (todos documentados).
- Dominios con use-cases back: **22** (todos excepto `consumption-units`).
- Dominios con use-cases front: **2** (`assistant`, `massiveload`).
- Dominios con contrato en `contract.md`: todos documentados.
- API externa formalizada: ✅ (`openapi.yaml`).
- Secciones de user-guide: **12** (index, login, navigation, document, tasks, accounting,
  massiveload, configuration, auth-transition, assistant).
- Backlog consolidado: ver `specs/backlog.md` (tasks + nuevos casos de uso).
