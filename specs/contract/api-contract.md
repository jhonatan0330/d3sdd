# Acuerdo de API — D3 (contract)

Contrato compartido entre `d3_front` (Angular) y `d3brain` (Spring Boot). **Fuente de verdad**:
cualquier implementación en uno u otro proyecto que se desvíe de aquí es un defecto.

> Contraparte máquina-legible: [`openapi.yaml`](openapi.yaml). Mantener ambos sincronizados.

## 1. Base URL

No es fija. El cliente SPA resuelve la URL del backend en runtime:

1. Lee `/assets/conf.xml`.
2. Si trae una URL (distinta de `''` o `'SW42'`), la usa; si no, usa `location.origin`.
3. La guarda en `LocalConstants.URL_CONF` y la antepone a cada path vía `LocalStoreService.getUrlAccess(path)`.

Los paths en este documento son **relativos** a esa base (ej. `/main/autenticarUsuarioAutenticacion`).

## 2. Autenticación y sesión

Modelo **mixto en transición a JWT** (ver `d3brain/AGENTS.md` y `domains/authentication/design.md`):

| Mecanismo | Dónde | Estado |
|-----------|-------|--------|
| Token opaco (llave de `usuariosesion_ussp`) en header `Authorization` o campo `securityToken` del DTO | API interna SPA | Activo hoy |
| JWT HS256 con claim `jti` = llave de sesión | API interna SPA | En implementación |
| `x-api-key` (header) + `Authorization` bearer | API externa `/api/*` | Activo |

Reglas:

- El token viaja por header `Authorization: Bearer <token>` **o** por el campo `securityToken`
  de los DTOs (convención legacy que el front sigue usando en filtros).
- Login normal y Google Sign-In generan el **mismo tipo** de token y sesión.
- Multi-tenancy: el tenant se resuelve por catálogo JDBC externo vía `TenantContext`
  (cache por tenant). **No** es parámetro del request; se deriva de la sesión / API key.
- Al cambiar clave se cierra la sesión (`closeAllSession`), invalidando todos los JWT salvo el actual.

## 3. Formato de errores

Las excepciones de dominio `ServerException` se serializan como `SharedApiErrorResponse`:

```json
{
  "status": 400,
  "error_code": "CODIGO",
  "message": "Mensaje para el usuario",
  "detail": "Detalle técnico (opcional)"
}
```

- `ServerException` puede empaquetar el origen como `[[origen]]mensaje`. El cliente debe
  usar `getTextMessage()` para mostrar solo el mensaje y `getOrigen()` para el origen.
- Estados lógicos de registros: `A` (activo) / `I` (inactivo) — `SharedConstants`.
- HTTP status coherente con el tipo de error (4xx cliente, 5xx servidor).

## 4. API interna (consumida por la SPA)

Todas usan `POST` salvo donde se indique. El token se envía en `Authorization` o en
`securityToken` del body.

| Método | Path | Descripción | Auth |
|--------|------|-------------|------|
| POST | `/main/autenticarUsuarioAutenticacion` | Login con usuario/clave | No |
| POST | `/main/googleAuthenticate` | Login con `id_token` de Google | No |
| POST | `/main/checkToken` | Validar token de sesión actual | Sí (`securityToken`) |
| POST | `/main/cambiarClave` | Cambiar clave propia u de otro usuario | Sí |
| POST | `/main/cambiarClaveOtherSystem` | Cambiar clave en sistema externo | Sí |
| POST | `/main/solicitarNuevaClave` | Recuperar / solicitar nueva clave | No |
| GET  | `/main/obtenerPrincipalOrganizacion` | Org principal (carousel, módulos) | Sí |

DTOs de auth (definir campos faltantes en `openapi.yaml`):

- Request login: `UsuarioAutenticacionFilterDTO { sesion, clave, claveAnterior, securityToken?, usuario? }`
- Response login: `UsuarioAutenticacionDTO { token, usuarioDTO, organizacion, mensaje?, fechaMaxima? }`

## 5. API externa `/api/*`

Autenticación por `x-api-key` (obligatorio en todos) + `Authorization` (requerido salvo en
`/api/login`, `/api/ok`, `/api/ping`).

| Método | Path | Descripción | API key | Token |
|--------|------|-------------|:-------:|:-----:|
| POST | `/api/login` | Login API (user/password) → `SharedIdResponse` | ✅ | ❌ |
| POST | `/api/get` | Listar documentos por filtro | ✅ | ✅ |
| POST | `/api/send` | Crear/actualizar documento | ✅ | ✅ |
| POST | `/api/getReport` | Generar reporte → `SharedIdResponse` | ✅ | ✅ |
| POST | `/api/getWithLogin` | Login implícito + get | ✅ | ❌ (login en body) |
| POST | `/api/getDataFieldWithLogin` | Login implícito + datos de campo | ✅ | ❌ |
| POST | `/api/sendWithLogin` | Login implícito + send | ✅ | ❌ |
| GET  | `/api/ok` | Healthcheck autenticado por API key | ✅ | ❌ |
| GET  | `/api/ping` | Healthcheck público | ❌ | ❌ |

DTOs (ver `openapi.yaml` para esquemas completos):

- `LoginRequest { user, password }`
- `DocumentFilterRequest { template, id?, code?, filterText?, states[], active?, page, size, creationDateMin/Max, dateMin/Max, filters[] }`
- `DocumentRequest { template, id?, code?, active?, stateId?, stateName?, fields[] }`
- `FieldRequest { field, value?, products[]?, parentDocument? }`
- `ReportRequest { template, code?, documentId?, parameters[] }`
- `DocumentResponse { template, id, code, active, stateId, stateName, fields[], fullValue, pendingValue }`
- `SharedIdResponse { id, code?, state?, comment?, messages[] }`

## 6. Otros endpoints usados por la SPA (contract parcial)

Documentados en sus dominios, listados aquí para trazabilidad:

- Documentos: `/rest/guardarDocumento`, `/rest/consultarDocumento`, `/rest/validateBeforeNew`,
  `/rest/changeState`, `/rest/consultarDatosBase`, `/rest/upload`, `/rest/changePicture`,
  `/document/getDocuments`, `/document/getInventory/{id}`, `/template/*`, `/user/dfa`,
  `/user/document/{query}`.

## 7. Cambios y versionado

No hay versionado de API formal. Registrar aquí cambios breaking:

| Fecha | Cambio | Breaking | Afecta |
|-------|--------|:--------:|--------|
| 2026-08-26 | Creación del contract SDD (baseline) | No | Todos |

## 8. Pendientes de contrato

- [ ] Definir esquemas completos de `UsuarioAutenticacionDTO` / `OrganizacionDTO`.
- [ ] Documentar explícitamente el header `non-duplicate` (session) usado en `/rest/guardarDocumento`.
- [ ] Decidir y documentar política de expiración/refresh de JWT.
- [ ] Listar códigos de error (`error_code`) estandarizados por dominio.
