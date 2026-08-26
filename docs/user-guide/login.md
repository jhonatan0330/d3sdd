# Ingreso a la aplicación

Pantalla: `/sign-in`. Casos de uso relacionados:
[CU-AUTH-001](../specs/domains/authentication/use-cases.md),
[CU-AUTH-002](../specs/domains/authentication/use-cases.md),
[CU-AUTH-005](../specs/domains/authentication/use-cases.md).

## Ingreso con usuario y clave

1. Abre la aplicación; verás el carrusel/imagen de la organización (configurado por la
   empresa en `obtenerPrincipalOrganizacion`).
2. Ingresa tu **usuario** (`sesion`) y **clave** (`clave`).
3. Haz clic en **Ingresar**.
4. La app valida credenciales y, si son correctas, guarda tu sesión y te lleva al panel.

> Si la clave es incorrecta o tu usuario está inactivo, la app muestra el error y no ingresa.

## Ingreso con Google (*en desarrollo*)

1. Haz clic en **Ingresar con Google** (si está habilitado).
2. Eliges tu cuenta de Google; la app envía el `id_token` al backend.
3. El backend valida que tu correo de Google exista en el sistema. Si no existe, el acceso
   es rechazado (no hay auto-registro).

> Requisito: tu correo de Google debe coincidir con `cusr_correo` en el sistema.

## Recuperar / solicitar nueva clave

1. En la pantalla de ingreso, usa la opción de recuperación.
2. Ingresa tu **identificación** y **correo**.
3. El sistema procesa la solicitud y te envía el mecanismo de restablecimiento.

## Cerrar sesión

Desde el menú de usuario, selecciona **Cerrar sesión**. La app elimina tu sesión local y
te devuelve a `/sign-in`.

> NOTA: hoy el cierre de sesión limpia el token en el navegador; la invalidación remota de la
> sesión ocurrirá al cambiar clave (ver [CU-AUTH-006](../specs/domains/authentication/use-cases.md)
> y backlog `AUTH-NEW-001`).
