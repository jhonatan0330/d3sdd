# Dominios de negocio — inventario y avance SDD

Este archivo es el **índice maestro** de dominios del ecosistema D3 (front `d3_front` +
back `d3brain`). Refleja el avance de documentación SDD y del manual de usuario.

Leyenda: ✅ completo · 🔧 en curso · ⏳ pendiente · — no aplica

| Dominio | Descripción | use-cases | contract | tasks | User-guide | Backlog | Estado global |
|---------|-------------|:---------:|:--------:|:-----:|:----------:|---------|:-------------:|
| **authentication** | Login, Google, checkToken, cambio/recuperación clave, logout | ✅ | ✅ | 🔧 | ✅ (login) | AUTH-NEW-001..005 | 🔧 |
| **documents** | Expedientes/pedidos: guardar, consultar, listar, changeState, upload, plantillas | ✅ | ✅ | ✅ | ✅ | DOC-NEW-002..003 | 🔧 |
| **tasks** | Crear/asignar tareas (`TaskRest`) | ✅ | ✅ | ✅ | ✅ | TASK-NEW-001..002 | 🔧 |
| **accounting** | Comprobantes y plan contable (`VoucherRest`, `PlanAccountingRest`) | ⏳ | 🔧 | ⏳ | ⏳ (nav) | ACC-NEW-001..002 | ⏳ |
| **massive** | Carga masiva de documentos (`MassiveRest`) | ⏳ | 🔧 | ⏳ | ⏳ (nav) | MAS-NEW-001 | ⏳ |
| **notifications** | Centro de notificaciones (`notification-center.service`) | ⏳ | 🔧 | ⏳ | ⏳ (nav) | NOT-NEW-001 | ⏳ |
| **config-forms** | Org, procesos, plantillas, servidores (`configuration-forms`) | ⏳ | 🔧 | ⏳ | ⏳ (nav) | CFG-NEW-001 | ⏳ |
| **document-transition** | Cambios de estado de documentos (`document-transition`) | ⏳ | 🔧 (/rest/changeState) | ⏳ | ⏳ (nav) | — | ⏳ |
| **persons** | Módulo de personas (eager) | ⏳ | 🔧 | ⏳ | ⏳ (nav) | — | ⏳ |
| **authorization** | Perfil/autorización de usuario (`authorization`) | ⏳ | 🔧 | ⏳ | ⏳ (nav) | — | ⏳ |
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

### tasks / accounting / massive / notifications / config-forms ⏳
- Endpoints y servicios identificados (ver `AGENTS.md` y código). Falta spec por dominio.

## Cuenta total de avance

- Dominios con spec iniciada: **3** (authentication, documents, tasks).
- Dominios con contrato en `contract/`: authentication ✅ + 9 parciales (endpoints dispersos).
- API externa formalizada: ✅ (`openapi.yaml`).
- Secciones de user-guide: **5** (index, login, navigation, documents, tasks).
- Ítems en backlog: ~20 (ver `specs/backlog.md`).

## Próximos dominios sugeridos (orden)
1. `accounting`.
2. `massive` / `notifications` / `config-forms`.
3. `document-transition` / `persons` / `authorization`.
