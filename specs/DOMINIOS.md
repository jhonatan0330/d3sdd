# Dominios de negocio — inventario y avance SDD

Este archivo es el **índice maestro** de dominios del ecosistema D3 (front `d3_front` +
back `d3brain`). Refleja el avance de documentación SDD y del manual de usuario.

Leyenda: ✅ completo · 🔧 en curso · ⏳ pendiente · — no aplica

| Dominio | Descripción | use-cases | contract | tasks | User-guide | Backlog | Estado global |
|---------|-------------|:---------:|:--------:|:-----:|:----------:|---------|:-------------:|
| **authentication** | Login, Google, checkToken, cambio/recuperación clave, logout | ✅ | ✅ | 🔧 | ✅ (login) | AUTH-NEW-001..005 | 🔧 |
| **documents** | Expedientes/pedidos: guardar, consultar, listar, changeState, upload, plantillas | ✅ | ✅ | ✅ | ✅ | DOC-NEW-002..003 | 🔧 |
| **tasks** | Crear/asignar tareas (`TaskRest`) | ✅ | ✅ | ✅ | ✅ | TASK-NEW-001..002 | 🔧 |
| **accounting** | Comprobantes y plan contable (`VoucherController`, `PlanAccountingController`) | ✅ | ✅ (§8) | ✅ | ✅ ([accounting.md](../../docs/user-guide/accounting.md)) | ACC-NEW-001..002 | ✅ |
| **massive** | Carga masiva de documentos (`MassiveRest`) | ✅ | ✅ (§9) | ✅ | ✅ ([massive-notifications.md](../../docs/user-guide/massive-notifications.md)) | MAS-NEW-001 | ✅ |
| **notifications** | Centro de notificaciones (`NotificationController`) | ✅ | ✅ (§10) | ✅ | ✅ ([massive-notifications.md](../../docs/user-guide/massive-notifications.md)) | NOT-NEW-001 | 🔧 |
| **config-forms** | Org, procesos, plantillas, servidores (`PropertyController`,`ConfigurationController`) | ✅ | ✅ (§11) | ✅ | ✅ ([config-persons.md](../../docs/user-guide/config-persons.md)) | CFG-NEW-001 | 🔧 |
| **document-transition** | Cambios de estado de documentos (`APIController`,`TemplateController`) | ✅ | ✅ (§14) | ✅ | ✅ ([auth-transition.md](../../docs/user-guide/auth-transition.md)) | — | ✅ |
| **persons** | Módulo de personas/usuarios (`UserController`) | ✅ | ✅ (§12) | ✅ | ✅ ([config-persons.md](../../docs/user-guide/config-persons.md)) | — | ✅ |
| **authorization** | Perfil/autorización/roles/2FA (`UserController` roles) | ✅ | ✅ (§13) | ✅ | ✅ ([auth-transition.md](../../docs/user-guide/auth-transition.md)) | — | ✅ |
| **api-external** | API pública `/api/*` (login/get/send/report) | — (transversal) | ✅ (openapi) | ✅ (ApiRest) | — | — | ✅ |

## Detalle por dominio

### authentication ✅ base
- Specs: `specs/domains/authentication/` (use-cases, requirements, design, tasks).
- Contract: `/main/*` y JWT/Google documentados en `contract/`.
- User-guide: `docs/user-guide/login.md`.
- Pendiente: logout remoto, refresh token, Google en SPA (ver backlog).

### documents ✅
- Specs: `specs/domains/documents/` (use-cases, requirements, design, tasks).
- Contract: endpoints `/rest/*`, `/document/*`, `/template/*` documentados en
  `contract/api-contract.md` §6 y `openapi.yaml`.
- User-guide: `docs/user-guide/documents.md`.
- Pendiente: migrar token en body de `/document/getDocument`/`saveDocument` a header
  (T-DOC-012); auditoría de cambios de estado (DOC-NEW-002).

### accounting / massive / notifications / config-forms / persons / authorization / document-transition ✅
- Specs completos en `specs/domains/<dominio>/` (use-cases, requirements, design, tasks).
- Contract: endpoints agregados en `contract/api-contract.md` §8–§14.
- User-guide: páginas por dominio en `docs/user-guide/`.

## Cuenta total de avance

- Dominios con spec completa: **10** (authentication, documents, tasks, accounting, massive,
  notifications, config-forms, persons, authorization, document-transition).
- Dominios con contrato en `contract/`: todos documentados (§1–§14).
- API externa formalizada: ✅ (`openapi.yaml`).
- Secciones de user-guide: **12** (index, login, navigation, documents, tasks, accounting,
  massive-notifications, config-persons, auth-transition).
- Ítems en backlog: ~20 (ver `specs/backlog.md`).

## Próximos pasos sugeridos
1. Renombrado de paquetes a `d3.*` ya realizado (ver ADR-001); pendiente actualizar `d3brain/AGENTS.md`.
2. Completar esquemas `openapi.yaml` de los DTOs nuevos.
3. Cubrir en Angular los servicios aún sin spec de front (contabilidad, notificaciones).

## Módulos detectados tras la reorganización (sin spec SDD aún)

La reorganización de paquetes reveló módulos `d3.*` que aún no tienen carpeta de dominio.
Candidatos a nuevos dominios (ver inventario en ADR-001):

| Módulo | Paquete | Posible dominio |
|--------|---------|-----------------|
| Facturación electrónica | `d3.fe` (`FEController`, `fe`) | `fe` |
| Multi-tenancy | `d3.multitenancy` | `multitenancy` (ver INF-NEW-001) |
| Inventario | `d3.inventory` | `inventory` |
| Dinero/moneda | `d3.money` | `money` |
| Tarifas | `d3.tariff` | `tariff` |
| Reportes | `d3.report` | `report` |
| Correo | `d3.mail` | `mail` |
| Subida de archivos | `d3.upload` (`FileController`) | `upload` |
| Diseñador de procesos | `d3.process_designer` | `process-designer` |
| Web services | `d3.webservice` | `webservice` |
| Homologación | `d3.homologate` | `homologate` |
| Log de transacciones | `d3.document_transaction` | `document-transaction` |
| Utilidades base | `d3.java`, `d3.shared` | (transversal) |
