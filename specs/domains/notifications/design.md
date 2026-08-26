# Diseño — Notificaciones (NOT)

## Paquetes (realizado según ADR-001)
- `d3.notification` (notificaciones)
- `d3.logisticpymes` (UsuarioDTO/UsuarioSvc)

## Componentes
- `NotificationController` (`/notification`): orquesta actividades.
- `ActividadSvc`: `listUserActivities`, `readActivity`, `guardar`.
- `UsuarioSvc`: `getUsersState` (usuarios por documento para transferencia).

## DTOs
- `ActividadDTO` (campos: `llaveTabla`, `documento`), `UsuarioDTO`.
