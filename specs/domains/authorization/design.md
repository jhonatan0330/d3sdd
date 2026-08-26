# Diseño — Autorización y Roles (AUT)

## Paquetes (realizado según ADR-001)
- `d3.authorization` (RolAcceso*)
- `d3.authentication` (UsuarioAutenticacion*)

## Componentes
- `UserController` (`/user`): `getRole`, `roles/{userId}`,
  `cambiarClaveUsuarioAutenticacion`, `dfa`.
- `RolAccesoSvc`: `listarConsulta`, `consultaUsuarioDocumento`.
- `UsuarioAutenticacionSvc`: `cambiarClave`, `dobleFactorAutenticacion`.

## DTOs
- `RolAccesoDTO`, `RolAccesoFilterDTO`, `UsuarioAutenticacionDTO`.
