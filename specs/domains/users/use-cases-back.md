# Casos de uso — Usuarios (USR) (BACKEND)

> Capa backend (d3brain). Contratos/endpoints aquí. Los pasos de UI (si los hubiera) van en use-cases-front.md.

Dominio: `users` (entidad usuario). Contrato: §13.

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-USR-001 | Listar usuarios por rol/filtro | Usuario autenticado | ✅ |
| CU-USR-002 | Obtener usuario por id | Usuario autenticado | ✅ |
| CU-USR-003 | Obtener usuario por documento | Usuario autenticado | ✅ |
| CU-USR-004 | Propiedades asignadas a un usuario | Usuario autenticado | ✅ |

---

## CU-USR-001 — Listar usuarios
`POST user/getUsers` con `UsuarioFilterDTO` → `List<UsuarioDTO>`.

## CU-USR-002 — Usuario por id
`GET user/{userId}` → `UsuarioDTO`.

## CU-USR-003 — Usuario por documento
`GET user/document/{documentId}` → `UsuarioDTO`.

## CU-USR-004 — Propiedades del usuario
`GET user/properties/{userId}` → `List<PropiedadDTO>`.
