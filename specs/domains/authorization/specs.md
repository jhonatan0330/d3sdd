# Specs — Autorización y Roles (AUT)

## Requisitos

### Funcionales
- R-AUT-001: Catálogo de roles de acceso (`RolAccesoDTO`).
- R-AUT-002: Asignación de roles por usuario.
- R-AUT-003: Cambio de contraseña y validación de doble factor (2FA).

### No funcionales
- NF-AUT-001: Autenticación por `Authorization`.
- NF-AUT-002: `RolAccesoDTO` vive en `d3.authorization` (ARCH-001: `com.softure.authorization`).

## Diseño

### Paquetes (realizado según ARCH-001)
- `d3.authorization` (RolAcceso*)
- `d3.authentication` (UsuarioAutenticacion*)

### Componentes
- `UserController` (`/user`): `getRole`, `roles/{userId}`,
  `cambiarClaveUsuarioAutenticacion`, `dfa`.
- `RolAccesoSvc`: `listarConsulta`, `consultaUsuarioDocumento`.
- `UsuarioAutenticacionSvc`: `cambiarClave`, `dobleFactorAutenticacion`.

### DTOs
- `RolAccesoDTO`, `RolAccesoFilterDTO`, `UsuarioAutenticacionDTO`.
