# Specs — Notificaciones (NOT)

## Requisitos

### Funcionales
- R-NOT-001: Bandeja de actividades del usuario autenticado.
- R-NOT-002: Marcar actividad como leída.
- R-NOT-003: Reasignar (transferir) una actividad a otro usuario.
- R-NOT-004: Listar usuarios elegibles para transferencia (por documento/rol).

### No funcionales
- NF-NOT-001: Autenticación por `Authorization`.
- NF-NOT-002: `ActividadDTO` debe llevar `documento` (llave) en transferencia.

## Diseño

### Paquetes (realizado según ARCH-001)
- `d3.notification` (notificaciones)
- `d3.logisticpymes` (UsuarioDTO/UsuarioSvc)

### Componentes
- `NotificationController` (`/notification`): orquesta actividades.
- `ActividadSvc`: `listUserActivities`, `readActivity`, `guardar`.
- `UsuarioSvc`: `getUsersState` (usuarios por documento para transferencia).

### DTOs
- `ActividadDTO` (campos: `llaveTabla`, `documento`), `UsuarioDTO`.
