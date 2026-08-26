# Dominios de negocio — inventario y avance SDD

Este archivo es el **índice maestro** de dominios del ecosistema D3 (front `d3_front` +
back `d3brain`). Refleja el avance de documentación SDD y del manual de usuario.

Leyenda: ✅ completo · 🔧 en curso · ⏳ pendiente · — no aplica

| Dominio | Descripción | use-cases | contract | tasks | User-guide | Backlog | Estado global |
|---------|-------------|:---------:|:--------:|:-----:|:----------:|---------|:-------------:|
| **authentication** | Login, Google, checkToken, cambio/recuperación clave, logout | ✅ | ✅ | 🔧 | ✅ (login) | AUTH-NEW-001..005 | 🔧 |
| **documents** | Expedientes/pedidos: get/send, guardar, changeState, upload, plantillas | ⏳ | 🔧 (endpoints /rest,/document,/template) | ⏳ | ⏳ | DOC-NEW-001..004 | ⏳ |
| **tasks** | Crear/asignar tareas (`TaskRest`) | ⏳ | 🔧 (endpoint /task) | ⏳ | ⏳ (nav) | TASK-NEW-001..002 | ⏳ |
| **accounting** | Comprobantes y plan contable (`VoucherRest`, `PlanAccountingRest`) | ⏳ | 🔧 | ⏳ | ⏳ (nav) | ACC-NEW-001..002 | ⏳ |
| **massive** | Carga masiva de documentos (`MassiveRest`) | ⏳ | 🔧 | ⏳ | ⏳ (nav) | MAS-NEW-001 | ⏳ |
| **notifications** | Centro de notificaciones (`notification-center.service`) | ⏳ | 🔧 | ⏳ | ⏳ (nav) | NOT-NEW-001 | ⏳ |
| **config-forms** | Org, procesos, plantillas, servidores (`configuration-forms`) | ⏳ | 🔧 | ⏳ | ⏳ (nav) | CFG-NEW-001 | ⏳ |
| **document-transition** | Cambios de estado de documentos (`document-transition`) | ⏳ | 🔧 (/rest/changeState) | ⏳ | ⏳ (nav) | — | ⏳ |
| **persons** | Módulo de personas (eager) | ⏳ | ┧ | ⏳ | ⏳ (nav) | — | ⏳ |
| **authorization** | Perfil/autorización de usuario (`authorization`) | ⏳ | 🔧 | ⏳ | ⏳ (nav) | — | ⏳ |
| **api-external** | API pública `/api/*` (login/get/send/report) | — (transversal) | ✅ (openapi) | ✅ (ApiRest) | — | — | ✅ |

## Detalle por dominio

### authentication ✅ base
- Specs: `specs/domains/authentication/` (use-cases, requirements, design, tasks).
- Contract: `/main/*` y JWT/Google documentados en `contract/`.
- User-guide: `docs/user-guide/login.md`.
- Pendiente: logout remoto, refresh token, Google en SPA (ver backlog).

### documents ⏳
- Backend expone `/rest/guardarDocumento`, `/rest/consultarDocumento`, `/rest/validateBeforeNew`,
  `/rest/changeState`, `/rest/consultarDatosBase`, `/rest/upload`, `/rest/changePicture`,
  `/document/getDocuments`, `/document/getInventory/{id}`, `/template/*`, `/user/dfa`.
- Falta: crear `specs/domains/documents/` y secciones de user-guide.

### tasks / accounting / massive / notifications / config-forms ⏳
- Endpoints y servicios identificados (ver `AGENTS.md` y código). Falta spec por dominio.

## Cuenta total de avance

- Dominios con spec iniciada: **1** (authentication).
- Dominios con contrato en `contract/`: authentication ✅ + 9 parciales (endpoints dispersos).
- API externa formalizada: ✅ (`openapi.yaml`).
- Secciones de user-guide: **3** (index, login, navigation).
- Ítems en backlog: ~20 (ver `specs/backlog.md`).

## Próximos dominios sugeridos (orden)
1. `documents` (núcleo del negocio D3).
2. `tasks`.
3. `accounting`.
