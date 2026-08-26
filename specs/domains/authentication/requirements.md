# Requisitos — Autenticación (AUTH)

Formato EARS. Referencia: `use-cases.md` y `contract/api-contract.md`.

## Requisitos funcionales

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

## Requisitos de seguridad

- **RS-AUTH-001** El token *será* firmado (JWT HS256) con clave ≥ 32 bytes una vez completada
  la migración; el `jti` *será* la llave de sesión (`usuariosesion_ussp`).
- **RS-AUTH-002** El sistema *no debe* incluir datos sensibles en los claims del JWT.
- **RS-AUTH-003** El sistema *no debe* loguear tokens, secretos ni claves.
- **RS-AUTH-004** La API externa `/api/*` *requerirá* `x-api-key` en todo request.
- **RS-AUTH-005** El `id_token` de Google *deberá* verificarse validando `aud`, `iss` y `exp`.

## Reglas de negocio

- **RN-AUTH-001** Multi-tenancy: el tenant se deriva de la sesión/API key, no del request.
- **RN-AUTH-002** `claveAnterior` transporta `environment.dateCompile` para control de versión
  de cliente (no es la clave anterior real).
- **RN-AUTH-003** Estados de registros: `A` activo / `I` inactivo (`SharedConstants`).

## Trazabilidad

| Requisito | Caso de uso | Contrato |
|-----------|-------------|----------|
| RF-AUTH-001..002 | CU-AUTH-001 | `/main/autenticarUsuarioAutenticacion` |
| RF-AUTH-005..006 | CU-AUTH-002 | `/main/googleAuthenticate` |
| RF-AUTH-003 | CU-AUTH-003 | `/main/checkToken` |
| RF-AUTH-007..008 | CU-AUTH-004 | `/main/cambiarClave` |
| RF-AUTH-009 | CU-AUTH-005 | `/main/solicitarNuevaClave` |
| RF-AUTH-010 | CU-AUTH-006 | signout + checkToken |
