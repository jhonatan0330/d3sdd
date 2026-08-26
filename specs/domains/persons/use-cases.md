# Casos de uso — Personas / Usuarios (PER)

Dominio: `persons` (entidad usuario). Contrato: §13.

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-PER-001 | Listar usuarios por rol/filtro | Usuario autenticado | ✅ |
| CU-PER-002 | Obtener usuario por id | Usuario autenticado | ✅ |
| CU-PER-003 | Obtener usuario por documento | Usuario autenticado | ✅ |
| CU-PER-004 | Propiedades asignadas a un usuario | Usuario autenticado | ✅ |

---

## CU-PER-001 — Listar usuarios
`POST user/getUsers` con `UsuarioFilterDTO` → `List<UsuarioDTO>`.

## CU-PER-002 — Usuario por id
`GET user/{userId}` → `UsuarioDTO`.

## CU-PER-003 — Usuario por documento
`GET user/document/{documentId}` → `UsuarioDTO`.

## CU-PER-004 — Propiedades del usuario
`GET user/properties/{userId}` → `List<PropiedadDTO>`.
