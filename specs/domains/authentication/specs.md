# Specs — Autenticación (AUTH)

## Requisitos

Formato EARS. Referencia: `use-cases-back.md` y `contract.md`.

### Requisitos funcionales

- **RF-AUTH-001** El sistema *debe* permitir autenticar un usuario con `sesion` + `clave`
  vía `POST /main/autenticarUsuarioAutenticacion`.
- **RF-AUTH-002** El sistema *debe* devolver un token de sesión y el perfil
  (`usuarioDTO`, `organizacion`) al autenticar correctamente.
- **RF-AUTH-003** Cuando el token se envía en `Authorization` o `securityToken`, el sistema
  *debe* validarlo contra la sesión activa antes de ejecutar la operación.
- **RF-AUTH-004** El sistema *debe* rechazar el acceso si el usuario está inactivo
  (`estado = I`).
- **RF-AUTH-005** El sistema *puede* autenticar usuarios mediante Google Sign-In cuando el
  correo exista en `usuario_usrp.cusr_correo`.
- **RF-AUTH-006** El sistema *no debe* auto-registrar usuarios desde Google Sign-In.
- **RF-AUTH-007** El usuario *debe* poder cambiar su clave (y un administrador la de terceros)
  vía `POST /main/cambiarClave`.
- **RF-AUTH-008** Al cambiar la clave, el sistema *debe* cerrar todas las demás sesiones del
  usuario (revocación por `jti`).
- **RF-AUTH-009** El sistema *debe* permitir solicitar el restablecimiento de clave vía
  `POST /main/solicitarNuevaClave`.
- **RF-AUTH-010** El sistema *debe* validar un token persistido al arranque de la SPA
  (`POST /main/checkToken`).

### Requisitos de seguridad

- **RS-AUTH-001** El token *será* firmado (JWT HS256) con clave ≥ 32 bytes una vez completada
  la migración; el `jti` *será* la llave de sesión (`usuariosesion_ussp`).
- **RS-AUTH-002** El sistema *no debe* incluir datos sensibles en los claims del JWT.
- **RS-AUTH-003** El sistema *no debe* loguear tokens, secretos ni claves.
- **RS-AUTH-004** La API externa `/api/*` *requerirá* `x-api-key` en todo request.
- **RS-AUTH-005** El `id_token` de Google *deberá* verificarse validando `aud`, `iss` y `exp`.

### Reglas de negocio

- **RN-AUTH-001** Multi-tenancy: el tenant se deriva de la sesión/API key, no del request.
- **RN-AUTH-002** `claveAnterior` transporta `environment.dateCompile` para control de versión
  de cliente (no es la clave anterior real).
- **RN-AUTH-003** Estados de registros: `A` activo / `I` inactivo (`SharedConstants`).

### Trazabilidad

| Requisito | Caso de uso | Contrato |
|-----------|-------------|----------|
| RF-AUTH-001..002 | CU-AUTH-001 | `/main/autenticarUsuarioAutenticacion` |
| RF-AUTH-005..006 | CU-AUTH-002 | `/main/googleAuthenticate` |
| RF-AUTH-003 | CU-AUTH-003 | `/main/checkToken` |
| RF-AUTH-007..008 | CU-AUTH-004 | `/main/cambiarClave` |
| RF-AUTH-009 | CU-AUTH-005 | `/main/solicitarNuevaClave` |
| RF-AUTH-010 | CU-AUTH-006 | signout + checkToken |

## Diseño

Decisiones de diseño del dominio de autenticación. Referencia: `use-cases-back.md`,
`specs.md` y `d3brain/AGENTS.md` (sección Seguridad).

### D1. Modelo de sesión: opaco → JWT

**Decisión:** Migrar de token opaco (llave de `usuariosesion_ussp`) a JWT HS256, manteniendo
la tabla de sesiones como fuente de verdad para revocación.

- `JwtService` (`d3.authentication.application`): genera/parsea/valida JWT HS256.
- Claims: `sub`/`user` (llave usuario), `userId`, `userName`, `org`, `tenant`,
  `jti` (id de sesión = `cuss_llave`), `iat`, `exp`.
- **Compatibilidad:** la validación resuelve `jti` si el token tiene 3 segmentos (`.`).
  si no, lo trata como llave opaca (soporta `getTokenPublic`, `generateAdministratorToken`,
  flujos internos).

**Justificación:** permite revocación inmediata (cambio de clave), reduce consultas a BD por
request y habilita la API externa firmada.

### D2. Google Sign-In (manual, sin Spring Security)

**Decisión:** verificación del `id_token` con `GoogleIdTokenVerifier`
(`com.google.api.client:google-api-client`).

- No hay auto-registro: el correo debe existir en `usuario_usrp.cusr_correo`.
- Flujo: verificar `id_token` (aud/client-id, iss, exp) → buscar usuario → crear sesión + JWT
  igual que login normal.
- Endpoint: `POST /main/googleAuthenticate` (`GoogleAuthenticationDTO { idToken, urlServer }`).

**Riesgo:** dependencia de `google-api-client`; no usar Spring Security (decisión de arquitectura
actual del proyecto).

### D3. Revocación y cierre de sesión

- Al cambiar clave → `closeAllSession` cierra todas salvo la actual, resolviendo `jti`.
- El frontend `LoginService.signout()` limpia el token local y navega a `/sign-in`, pero hoy
  **no** hay endpoint de logout que invalide la sesión remota de forma explícita. Ver backlog
  T-AUTH-007 (endpoint `POST /main/cerrarSesion`).

### D4. Transporte del token

- Header `Authorization: Bearer <token>` (preferido).
- Campo `securityToken` en DTOs (legacy, aún usado por la SPA en filtros). Mantener hasta
  migrar el front a solo header.
- API externa: `x-api-key` (header) obligatorio + `Authorization` en endpoints autenticados.

### D5. Secretos y configuración

- `jwt.secret` (≥32 bytes), `jwt.expiration`, `jwt.issuer`, `google.client-id` en
  `application.properties` / variables de entorno. **No** commitear valores reales.

### Diagrama de flujo (login)

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

