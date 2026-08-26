# Tasks — Autenticación (AUTH)

Desglose de implementación. Cada ítem verificable en back (`d3brain`) y front (`d3_front`).
Estado: `[ ]` pendiente, `[x]` hecho, `[~]` en curso.

## Backend (d3brain)

- [x] **T-AUTH-001** Endpoint `POST /main/autenticarUsuarioAutenticacion` (login opaco).
- [x] **T-AUTH-002** Endpoint `POST /main/checkToken` con validación por `securityToken`.
- [x] **T-AUTH-003** Endpoint `POST /main/cambiarClave` (+ `cambiarClaveOtherSystem`).
- [x] **T-AUTH-004** Endpoint `POST /main/solicitarNuevaClave`.
- [x] **T-AUTH-005** Endpoint `GET /main/obtenerPrincipalOrganizacion`.
- [~] **T-AUTH-006** Migración a JWT HS256 (`JwtService`, claims, `jti`). Ver `design.md` D1.
  - [ ] Generación de JWT en `autenticar()` y flujo Google.
  - [ ] Validación dual JWT/opaco en `UsuarioSesionSvc`.
  - [ ] `closeAllSession` al cambiar clave resolviendo `jti`.
- [~] **T-AUTH-007** Endpoint `POST /main/googleAuthenticate` + `GoogleAuthenticationService`
  (GoogleIdTokenVerifier). Ver `design.md` D2.
  - [ ] Verificación `aud`/`iss`/`exp`.
  - [ ] Búsqueda por `cusr_correo` y validación estado.
- [ ] **T-AUTH-008** Endpoint `POST /main/cerrarSesion` (logout explícito que invalide sesión remota).
- [ ] **T-AUTH-009** Política de expiración/refresh de JWT (definir `jwt.expiration` por defecto).

## Frontend (d3_front)

- [x] **T-AUTH-010** `LoginService.signin()` → `/main/autenticarUsuarioAutenticacion`.
- [x] **T-AUTH-011** `LoginService.checkTokenIsValid()` → `/main/checkToken` + re-hidratación.
- [x] **T-AUTH-012** `LoginService.changePwd()` / `changePwdOther` → `/main/cambiarClave`.
- [x] **T-AUTH-013** `LoginService.recoverPassword()` → `/main/solicitarNuevaClave`.
- [x] **T-AUTH-014** `LoginService.signout()` limpia token local y navega a `/sign-in`.
- [ ] **T-AUTH-015** Adaptar el front para leer JWT (parsear `exp`) y refrescar/redirigir al expirar.
- [ ] **T-AUTH-016** Botón/opción "Ingresar con Google" que llama `/main/googleAuthenticate`.
- [ ] **T-AUTH-017** Migrar envío de `securityToken` en DTOs a header `Authorization` (legacy cleanup).
- [ ] **T-AUTH-018** Llamar a `POST /main/cerrarSesion` en `signout()` (cuando T-AUTH-008 exista).

## Verificación

- Back: `./gradlew.bat build -x test`
- Front: `npm run build` y `npx tsc -p tsconfig.app.json --noEmit`
