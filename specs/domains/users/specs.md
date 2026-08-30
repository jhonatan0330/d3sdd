# Specs — Usuarios (USR)

## Requisitos

### Funcionales
- R-USR-001: Catálogo de usuarios filtrable por rol.
- R-USR-002: Consulta de usuario por id y por documento.
- R-USR-003: Propiedades (configuración) asignadas por usuario.

### No funcionales
- NF-USR-001: Autenticación por `Authorization`.
- NF-USR-002: `UsuarioDTO` vive en `d3.logisticpymes` (ARCH-001).

## Diseño

### Paquetes (realizado según ARCH-001)
- `d3.logisticpymes` (UsuarioDTO, UsuarioSvc, UsuarioFilterDTO)
- `d3.property` (PropertyGetWithCacheService)

### Componentes
- `UserController` (`/user`): orquesta consultas de usuarios.
- `UsuarioSvc`: `listarRol`, `consultaXId`, `getUserByDocument`.
- `PropertyGetWithCacheService`: `getToUser` (propiedades en caché).

> Autenticación/2FA y roles se documentan en `authentication` y `authorization`.
