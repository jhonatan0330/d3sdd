# Navegación y módulos

La app usa un menú lateral (`mat-sidenav` + `simple-nav`). El acceso está protegido por
`AuthGuard`: si no hay sesión válida, redirige a `/sign-in`.

## Menú principal

Los módulos disponibles dependen de los permisos de tu organización (propiedad
`APP_MODULES` de la organización principal). Módulos típicos:

| Módulo | Descripción |
|--------|-------------|
| **Perfil** (`authorization`) | Datos del usuario y configuración de perfil. |
| **CRUDs** (`cruds`) | Mantenimientos genéricos. |
| **Tareas** (`tasks`) | Gestión de tareas. |
| **Carga masiva** (`massive`) | Carga de documentos en lote. |
| **Contabilidad** (`accounting`) | Comprobantes y plan contable. |
| **Transición de documentos** (`document-transition`) | Cambios de estado de expedientes. |
| **Notificaciones** (`notification`) | Centro de notificaciones. |
| **Configuración de formularios** (`configuration-forms`) | Org, procesos, plantillas, etc. |
| **Recuperar / Nueva clave** | Flujos de cambio de contraseña. |

> El set exacto de módulos visibles se negocia con la organización al ingresar
> (`obtenerPrincipalOrganizacion`).

## Uso general

- Selecciona un módulo en el menú lateral para cargarlo (los módulos se cargan de forma
  perezosa para mejorar el rendimiento).
- El contenido principal se actualiza sin recargar la página (SPA).
- Al cerrar sesión, vuelves al ingreso y se bloquea el acceso a los módulos protegidos.

## Módulos pendientes de documentar en el SDD

Cada uno tendrá su caso de uso en `specs/domains/` cuando se active del backlog:
`tasks`, `accounting`, `massive`, `notifications`, `config-forms`, `documents`.
