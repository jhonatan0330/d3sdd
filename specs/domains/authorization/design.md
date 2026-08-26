# Diseño — Autorización y Roles (AUT)

## Paquetes (objetivo ADR-001)
- `d3.authorization` ← `com.softure.authorization` (RolAcceso*)
- `d3.auth` ← `com.softure.authentication` (UsuarioAutenticacion*)

## Componentes
- `UserController` (`/user`): `getRole`, `roles/{userId}`,
  `cambiarClaveUsuarioAutenticacion`, `dfa`.
- `RolAccesoSvc`: `listarConsulta`, `consultaUsuarioDocumento`.
- `UsuarioAutenticacionSvc`: `cambiarClave`, `dobleFactorAutenticacion`.

## DTOs
- `RolAccesoDTO`, `RolAccesoFilterDTO`, `UsuarioAutenticacionDTO`.
