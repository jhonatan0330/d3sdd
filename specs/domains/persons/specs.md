# Specs — Personas / Usuarios (PER)

## Requisitos

### Funcionales
- R-PER-001: Catálogo de usuarios filtrable por rol.
- R-PER-002: Consulta de usuario por id y por documento.
- R-PER-003: Propiedades (configuración) asignadas por usuario.

### No funcionales
- NF-PER-001: Autenticación por `Authorization`.
- NF-PER-002: `UsuarioDTO` vive en `d3.logisticpymes` (ARCH-001).

## Diseño

### Paquetes (realizado según ARCH-001)
- `d3.logisticpymes` (UsuarioDTO, UsuarioSvc, UsuarioFilterDTO)
- `d3.property` (PropertyGetWithCacheService)

### Componentes
- `UserController` (`/user`): orquesta consultas de usuarios.
- `UsuarioSvc`: `listarRol`, `consultaXId`, `getUserByDocument`.
- `PropertyGetWithCacheService`: `getToUser` (propiedades en caché).

> Autenticación/2FA y roles se documentan en `authentication` y `authorization`.
