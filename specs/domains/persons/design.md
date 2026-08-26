# Diseño — Personas / Usuarios (PER)

## Paquetes (realizado según ADR-001)
- `d3.logisticpymes` (UsuarioDTO, UsuarioSvc, UsuarioFilterDTO)
- `d3.property` (PropertyGetWithCacheService)

## Componentes
- `UserController` (`/user`): orquesta consultas de usuarios.
- `UsuarioSvc`: `listarRol`, `consultaXId`, `getUserByDocument`.
- `PropertyGetWithCacheService`: `getToUser` (propiedades en caché).

> Autenticación/2FA y roles se documentan en `authentication` y `authorization`.
