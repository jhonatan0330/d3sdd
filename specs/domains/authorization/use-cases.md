# Casos de uso — Autorización y Roles (AUT)

Dominio: `authorization` (roles de acceso + identidad 2FA). Contrato: §14.

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-AUT-001 | Listar roles de acceso | Usuario autenticado | ✅ |
| CU-AUT-002 | Roles asignados a un usuario | Usuario autenticado | ✅ |
| CU-AUT-003 | Cambiar contraseña de autenticación | Usuario autenticado | ✅ |
| CU-AUT-004 | Doble factor de autenticación (2FA) | Usuario autenticado | ✅ |

---

## CU-AUT-001 — Listar roles
`GET user/getRole` → `List<RolAccesoDTO>` (solo activos).

## CU-AUT-002 — Roles de usuario
`GET user/roles/{userId}` → `List<RolAccesoDTO>`.

## CU-AUT-003 — Cambiar contraseña
`POST user/cambiarClaveUsuarioAutenticacion` con `UsuarioAutenticacionDTO` → `UsuarioAutenticacionDTO`.

## CU-AUT-004 — 2FA
`POST user/dfa` con `UsuarioAutenticacionDTO` (`usuario`, `token`, IP) → valida doble factor.
(Login/JWT/Google se documentan en `authentication`.)
