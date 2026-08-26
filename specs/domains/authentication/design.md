# Diseño — Autenticación (AUTH)

Decisiones de diseño del dominio de autenticación. Referencia: `use-cases.md`,
`requirements.md` y `d3brain/AGENTS.md` (sección Seguridad).

## D1. Modelo de sesión: opaco → JWT

**Decisión:** Migrar de token opaco (llave de `usuariosesion_ussp`) a JWT HS256, manteniendo
la tabla de sesiones como fuente de verdad para revocación.

- `JwtService` (`com.softure.authentication.application`): genera/parsea/valida JWT HS256.
- Claims: `sub`/`user` (llave usuario), `userId`, `userName`, `org`, `tenant`,
  `jti` (id de sesión = `cuss_llave`), `iat`, `exp`.
- **Compatibilidad:** la validación resuelve `jti` si el token tiene 3 segmentos (`.`);
  si no, lo trata como llave opaca (soporta `getTokenPublic`, `generateAdministratorToken`,
  flujos internos).

**Justificación:** permite revocación inmediata (cambio de clave), reduce consultas a BD por
request y habilita la API externa firmada.

## D2. Google Sign-In (manual, sin Spring Security)

**Decisión:** verificación del `id_token` con `GoogleIdTokenVerifier`
(`com.google.api.client:google-api-client`).

- No hay auto-registro: el correo debe existir en `usuario_usrp.cusr_correo`.
- Flujo: verificar `id_token` (aud/client-id, iss, exp) → buscar usuario → crear sesión + JWT
  igual que login normal.
- Endpoint: `POST /main/googleAuthenticate` (`GoogleAuthenticationDTO { idToken, urlServer }`).

**Riesgo:** dependencia de `google-api-client`; no usar Spring Security (decisión de arquitectura
actual del proyecto).

## D3. Revocación y cierre de sesión

- Al cambiar clave → `closeAllSession` cierra todas salvo la actual, resolviendo `jti`.
- El frontend `LoginService.signout()` limpia el token local y navega a `/sign-in`, pero hoy
  **no** hay endpoint de logout que invalide la sesión remota de forma explícita. Ver backlog
  T-AUTH-007 (endpoint `POST /main/cerrarSesion`).

## D4. Transporte del token

- Header `Authorization: Bearer <token>` (preferido).
- Campo `securityToken` en DTOs (legacy, aún usado por la SPA en filtros). Mantener hasta
  migrar el front a solo header.
- API externa: `x-api-key` (header) obligatorio + `Authorization` en endpoints autenticados.

## D5. Secretos y configuración

- `jwt.secret` (≥32 bytes), `jwt.expiration`, `jwt.issuer`, `google.client-id` en
  `application.properties` / variables de entorno. **No** commitear valores reales.

## Diagrama de flujo (login)

```
SPA --(sesion,clave)--> /main/autenticarUsuarioAutenticacion
        |
        v
UsuarioAutenticacionSvc.autenticar()
        |-- valida credenciales (usuario_usrp, estado=A)
        |-- crea usuariosesion_ussp
        |-- genera JWT (jti = cuss_llave)  [o token opaco hoy]
        v
UsuarioAutenticacionDTO { token, usuarioDTO, organizacion }
        |
        v
SPA: guarda JWT_TOKEN, APP_USER -> dashboard
```
