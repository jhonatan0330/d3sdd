# Diseño — Notificaciones (NOT)

## Paquetes (objetivo ADR-001)
- `d3.notification` ← `com.softure.notification`
- `d3.core` (UsuarioDTO/UsuarioSvc) ← `com.softure.logisticpymes`

## Componentes
- `NotificationController` (`/notification`): orquesta actividades.
- `ActividadSvc`: `listUserActivities`, `readActivity`, `guardar`.
- `UsuarioSvc`: `getUsersState` (usuarios por documento para transferencia).

## DTOs
- `ActividadDTO` (campos: `llaveTabla`, `documento`), `UsuarioDTO`.
