# Casos de uso — Notificaciones (NOT)

Dominio: `notifications`. Contrato: §11.

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-NOT-001 | Listar actividades/notificaciones del usuario | Usuario autenticado | ✅ |
| CU-NOT-002 | Marcar actividad como leída | Usuario autenticado | ✅ |
| CU-NOT-003 | Transferir (reasignar) actividad | Usuario autenticado | ✅ |
| CU-NOT-004 | Listar usuarios para transferir | Usuario autenticado | ✅ |

---

## CU-NOT-001 — Listar
`GET notification/getNotifications` (header `Authorization`) → `List<ActividadDTO>`.

## CU-NOT-002 — Marcar leída
`POST notification/readActivity` con `ActividadDTO` → `ActividadDTO`.

## CU-NOT-003 — Transferir
`POST notification/transfer` con `ActividadDTO` → `ActividadDTO`.

## CU-NOT-004 — Usuarios para transferir
`POST notification/userToTransfer` con `ActividadDTO` (debe incluir `documento`) → `List<UsuarioDTO>`.
