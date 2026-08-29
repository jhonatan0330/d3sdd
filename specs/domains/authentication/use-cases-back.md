# Casos de uso — Autenticación (AUTH) (BACKEND)

> Capa backend (d3brain). Contratos/endpoints aquí. Los pasos de UI (si los hubiera) van en use-cases-front.md.

Dominio: `authentication`. Contrato asociado: [`contract.md`](../../contract.md).

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-AUTH-001 | Login con usuario y clave | Usuario | ✅ documentado |
| CU-AUTH-002 | Login con Google Sign-In | Usuario | 🔧 en implementación |
| CU-AUTH-003 | Validar token de sesión | Sistema (SPA) | ✅ documentado |
| CU-AUTH-004 | Cambiar clave | Usuario autenticado | ✅ documentado |
| CU-AUTH-005 | Solicitar nueva clave (recuperación) | Usuario | ✅ documentado |
| CU-AUTH-006 | Cerrar sesión / invalidar token | Usuario | ✅ documentado |

---

## CU-AUTH-001 — Login con usuario y clave

- **Actor:** Usuario (operador de la SPA).
- **Precondición:** El usuario existe y está activo (`estado = A`) en `usuario_usrp`.
- **Pasos:**
  1. El usuario ingresa `sesion` (usuario) y `clave` (password) en `/sign-in`.
  2. La SPA envía `POST /main/autenticarUsuarioAutenticacion` con
     `UsuarioAutenticacionFilterDTO { sesion, clave, claveAnterior }`.
  3. El backend valida credenciales y crea una fila en `usuariosesion_ussp`
     (token opaco hoy; JWT en migración).
  4. Devuelve `UsuarioAutenticacionDTO { token, usuarioDTO, organizacion, mensaje?, fechaMaxima? }`.
  5. La SPA persiste `token` (JWT_TOKEN) y `usuario` (APP_USER) y navega al dashboard.
- **Postcondición:** Sesión activa; el token es usado en `Authorization`/`securityToken`.
- **Errores:**
  - Credenciales inválidas → `401` / `ServerException`.
  - Usuario inactivo → rechazo de acceso.
- **Notas:** `claveAnterior` lleva `environment.dateCompile` (control de versión de cliente).

---

## CU-AUTH-002 — Login con Google Sign-In

- **Actor:** Usuario.
- **Precondición:**
  - El correo de Google existe en `usuario_usrp.cusr_correo`.
  - `google.client-id` configurado en backend.
- **Pasos:**
  1. El usuario elige "Ingresar con Google" y la SPA obtiene el `id_token`.
  2. SPA envía `POST /main/googleAuthenticate` con `GoogleAuthenticationDTO { idToken, urlServer }`.
  3. Backend verifica el `id_token` (aud = client-id, iss, exp) vía `GoogleIdTokenVerifier`.
  4. Busca el usuario por `cusr_correo` y valida estado activo.
  5. Crea sesión + token (mismo flujo que CU-AUTH-001).
- **Postcondición:** Sesión activa con token equivalente al login normal.
- **Errores:**
  - `id_token` inválido/expirado → `401`.
  - Correo no existe en `usuario_usrp` → rechazo (sin auto-registro).
- **Estado:** 🔧 en implementación (ver `design.md`).

---

## CU-AUTH-003 — Validar token de sesión

- **Actor:** Sistema (SPA al arrancar / reanudar sesión).
- **Precondición:** Existe un `token` persistido localmente.
- **Pasos:**
  1. SPA envía `POST /main/checkToken` con `{ securityToken, claveAnterior }`.
  2. Backend resuelve `jti` (JWT) o trata token como llave opaca, consulta sesión en
     caché/BD y valida estado `A` y `fechaCierre` futura.
  3. Devuelve perfil; SPA re-hidrata usuario/empresa y plantillas.
- **Postcondición:** Sesión restaurada o cerrada.
- **Errores:** Token inválido/expirado → `401` → SPA fuerza `signout()`.

---

## CU-AUTH-004 — Cambiar clave

- **Actor:** Usuario autenticado (propia) o admin (otro usuario).
- **Pasos:**
  1. `POST /main/cambiarClave` con `UsuarioAutenticacionDTO { llaveTabla, usuario, claveAnterior, clave }`.
  2. Backend valida clave anterior y actualiza.
  3. Dispara `closeAllSession` (invalida todas las sesiones salvo la actual resolviendo `jti`).
- **Postcondición:** Clave actualizada; otras sesiones invalidadas.
- **Errores:** Clave anterior incorrecta → `400`.

---

## CU-AUTH-005 — Solicitar nueva clave (recuperación)

- **Actor:** Usuario (sin sesión).
- **Pasos:**
  1. `POST /main/solicitarNuevaClave` con `{ usuarioDTO.identificacion, usuarioDTO.correo }`.
  2. Backend genera/emailiza nueva clave o token de restablecimiento.
- **Postcondición:** Usuario recibe vía correo el mecanismo de restablecimiento.
- **Errores:** Identificación/correo no coinciden → `400`.

---

## CU-AUTH-006 — Cerrar sesión / invalidar token

- **Actor:** Usuario.
- **Pasos:**
  1. En SPA, `LoginService.signout()` limpia `JWT_TOKEN`/`APP_USER`, cierra diálogos y
     navega a `/sign-in`.
  2. (Backend) Invalidación del lado servidor ocurre al cambiar clave (CU-AUTH-004) o por
     cierre explícito de sesión (pendiente de endpoint dedicado — ver backlog).
- **Postcondición:** Token local eliminado; sesión remota en estado de cierre.
- **Pendiente:** endpoint de logout explícito en `/main/*` (ver backlog T-AUTH-007).

