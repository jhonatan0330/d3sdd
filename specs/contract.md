# Acuerdo de API — D3 (contract)

Contrato compartido entre `d3_front` (Angular) y `d3brain` (Spring Boot). **Fuente de verdad**:
cualquier implementación en uno u otro proyecto que se desvíe de aquí es un defecto.

> Contraparte máquina-legible: [`openapi.yaml`](openapi.yaml). Mantener ambos sincronizados.

## 1. Base URL

No es fija. El cliente SPA resuelve la URL del backend en runtime:

1. Lee `/assets/conf.xml`.
2. Si trae una URL (distinta de `''` o `'SW42'`), la usa; si no, usa `location.origin`.
3. La guarda en `LocalConstants.URL_CONF` y la antepone a cada path vía `LocalStoreService.getUrlAccess(path)`.

Los paths en este documento son **relativos** a esa base (ej. `/document/main/autenticarUsuarioAutenticacion`).

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
| POST | `/document/main/autenticarUsuarioAutenticacion` | Login con usuario/clave | No |
| POST | `/document/main/googleAuthenticate` | Login con `id_token` de Google | No |
| POST | `/document/main/checkToken` | Validar token de sesión actual | Sí (`securityToken`) |
| POST | `/document/main/cambiarClave` | Cambiar clave propia u de otro usuario | Sí |
| POST | `/document/main/cambiarClaveOtherSystem` | Cambiar clave en sistema externo | Sí |
| POST | `/document/main/solicitarNuevaClave` | Recuperar / solicitar nueva clave | No |
| GET  | `/document/main/obtenerPrincipalOrganizacion` | Org principal (carousel, módulos) | Sí |

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

## 6. API interna de documentos (dominio `documents`)

Además de la API externa `/api/*` y la auth `/main/*`, la SPA consume los siguientes
endpoints para el ciclo de vida de expedientes. Detalle completo en
[`domains/documents`](domains/documents/use-cases-back.md).

| Método | Path | Descripción | Auth |
|--------|------|-------------|------|
| POST | `/document/api/guardarDocumento` | Crear/editar documento (`PedidoVentaDTO`); header `non-duplicate` opcional | Sí (header) |
| POST | `/document/api/validateBeforeNew` | Validar reglas previas a crear | Sí |
| POST | `/document/api/consultarDocumento` | Documento completo por `llaveTabla` | Sí |
| POST | `/document/api/listarDocumentos` | Listar con filtros | Sí |
| POST | `/document/api/changeState` | Cambiar estado (`PedidoVentaAjusteDTO`) | Sí |
| POST | `/document/api/consultarDatosBase` | Datos base/dependentes de campo | Sí |
| POST | `/document/api/obtenerCampos` | Campos de una plantilla | Sí |
| POST | `/document/api/upload` | Subir archivo (`MultipartFile`) → URL | Sí (opc) |
| POST | `/document/api/saveByMassive` | Guardar desde carga masiva | Sí |
| POST | `/document/api/getMessageToProcessField/{property}/{fieldValue}` | Mensaje de proceso de campo | Sí |
| POST | `/document/api/logOut` | Cerrar sesión | Sí |
| POST | `/document/api/consultarUsuario` | Consultar usuario | Sí |
| POST | `/document/getDocument` | Documento completo (**token en body**, legacy) | Sí (body) |
| POST | `/document/getDocuments` | Listar (header) | Sí |
| POST | `/document/saveDocument` | Guardar (**token en body**, legacy) | Sí (body) |
| POST | `/document/upload` | Subir archivo | Sí (opc) |
| GET  | `/document/getInventory/{id}` | Inventario por producto | Sí |
| POST | `/document/readActivity` | Marcar actividad leída | Sí |
| GET  | `/document/main/checkSession` | Validar sesión (get session DTO) | Sí |
| POST | `/document/main/consultaUsuarioDocumentoPlantilla` | Plantillas del usuario | Sí |
| POST | `/document/main/listarUsuarioPedidoVenta` | Pedidos de venta del usuario | Sí |
| GET  | `/template/getTemplates/{profile}` | Plantillas por perfil (`ADMIN`/`READER`/usuario) | Sí |
| GET  | `/template/getFields?id=` | Campos de plantilla | Sí |
| POST | `/template/getFieldData` | Datos de campo | Sí |
| POST | `/template/getPropertyRelations` | Relaciones de propiedad | Sí |
| POST | `/template/validateLoad` | Validar carga de plantilla | Sí |
| POST | `/template/getTrace` | Trazabilidad (gestores) | Sí |
| GET  | `/template/getTraceFields/{documentId}/{transaction}` | Campos de trazabilidad | Sí |

> Endpoints legacy `/document/getDocument` y `/document/saveDocument` reciben el token en el
> body; pendiente migrar a header (ver `domains/documents/tasks.md` T-DOC-012).

## 7. API de tareas (dominio `tasks`)

La SPA consume los siguientes endpoints para gestión de tareas personales. Detalle en
[`domains/tasks`](domains/tasks/use-cases-back.md).

| Método | Path | Descripción | Auth |
|--------|------|-------------|------|
| GET  | `/task/` | Listar tareas del usuario | Sí |
| GET  | `/task/{id}?id={key}` | Ver tarea por id (id en query param) | Sí |
| POST | `/task/create` | Crear tarea (`TaskRequest`) | Sí |
| POST | `/task/update` | Actualizar tarea (`TaskRequest` con `key`) | Sí |
| POST | `/task/delete/{id}` | Eliminar tarea | Sí |

DTOs: `TaskDTO { user, title, notes, completed, dueDate, priority, order, createdAt }`,
`TaskRequest { key, user, title, notes, completed, dueDate, priority, order }`.

## 8. API de contabilidad (dominio `accounting`)

Comprobantes (vouchers) y plan contable. Detalle en
[`domains/accounting`](domains/accounting/use-cases-back.md).

| Método | Path | Descripción | Auth |
|--------|------|-------------|------|
| GET  | `acc/voucher/{catalog}` | Listar comprobantes por catálogo | Sí |
| GET  | `acc/voucher/one/{voucherId}` | Ver comprobante | Sí |
| POST | `acc/voucher/manual` | Crear comprobante manual | Sí |
| DELETE | `acc/voucher/manual/{voucherId}` | Eliminar comprobante manual | Sí |
| POST | `acc/voucher/generate-voucher` | Generar desde documento | Sí |
| POST | `acc/voucher/document` | Id de comprobante por documento | Sí |
| POST | `acc/voucher/range-clear-voucher` | Limpiar rango | Sí |
| POST | `acc/voucher/range-create-voucher` | Crear rango | Sí |
| GET  | `acc/plan/catalog` | Catálogos | Sí |
| GET  | `acc/plan/catalog/{id}` | Catálogo | Sí |
| GET  | `acc/plan/account/{catalog}` | Cuentas (filtro) | Sí |
| GET  | `acc/plan/account/{catalog}/{id}` | Cuenta | Sí |
| GET  | `acc/plan/balance/{catalog}` | Saldos | Sí |
| POST | `/acc/api/voucher` | API externa comprobante (`x-api-key`) | API key |
| GET  | `/acc/api/ok` | Healthcheck API contabilidad | API key |
| GET  | `/acc/api/ping` | Ping acumulador contable | No |

DTOs: `Voucher`, `VoucherDTO`, `VoucherPrepareRequest`, `VoucherRangeRequest`,
`AccountDTO`, `CatalogDTO`, `ResultMapDTO`.

## 9. API de carga masiva (dominio `massive`)

Importación de datos en lote. Detalle en
[`domains/massive`](domains/massive/use-cases-back.md).

| Método | Path | Descripción | Auth |
|--------|------|-------------|------|
| POST | `massiveload/upload` | Cargar archivo (`MassiveMasterRequest`) | Sí |
| POST | `massiveload/validate/{loadId}` | Validar carga | Sí |
| POST | `massiveload/execute/{loadId}` | Ejecutar/procesar carga | Sí |
| GET  | `massiveload/{loadId}` | Obtener carga (detalle) | Sí |
| GET  | `massiveload/{loadId}/items` | Ítems de la carga | Sí |
| POST | `massiveload/template` | ❑ Descargar plantilla base: `{templateId, format}` → `{url}` (CU-MAS-005, por crear) | Sí |

## 10. API de notificaciones (dominio `notifications`)

Centro de actividades/transferencia. Detalle en
[`domains/notifications`](domains/notifications/use-cases-back.md).

| Método | Path | Descripción | Auth |
|--------|------|-------------|------|
| GET  | `notification/getNotifications` | Listar actividades | Sí |
| POST | `notification/readActivity` | Marcar leída | Sí |
| POST | `notification/transfer` | Transferir actividad | Sí |
| POST | `notification/userToTransfer` | Usuarios para transferir | Sí |

DTOs: `ActividadDTO` (`llaveTabla`, `documento`), `UsuarioDTO`.

## 11. API de configuración y formularios (dominio `config-forms`)

Propiedades y export/import de configuración. Detalle en
[`domains/config-forms`](domains/config-forms/use-cases-back.md).

| Método | Path | Descripción | Auth |
|--------|------|-------------|------|
| GET  | `/api/config/property/{type}/{field}` | Propiedades por tipo/campo | Sí |
| GET  | `/api/config/property/type/{type}/{filterName}` | Valores definidos | Sí |
| GET  | `/api/config/property/{key}` | Propiedad por llave | Sí |
| POST | `/api/config/property/` | Crear/editar propiedad | Sí |
| GET  | `/api/config/configuration/export` | Exportar configuración | Sí |
| POST | `/api/config/configuration/module` | Exportar por módulos | Sí |
| POST | `/api/config/configuration/import` | Importar configuración | Sí |
| POST | `/api/config/configuration/compare` | Comparar configuración | Sí |

DTOs: `PropiedadDTO`, `PropiedadValorDefinidoDTO`, `ExportListRequest`, `FileVO`.

## 12. API de personas/usuarios (dominio `persons`)

Catálogo de usuarios. Detalle en
[`domains/persons`](domains/persons/use-cases-back.md).

| Método | Path | Descripción | Auth |
|--------|------|-------------|------|
| POST | `user/getUsers` | Listar por filtro/rol | Sí |
| GET  | `user/{userId}` | Usuario por id | Sí |
| GET  | `user/document/{documentId}` | Usuario por documento | Sí |
| GET  | `user/properties/{userId}` | Propiedades asignadas | Sí |

DTOs: `UsuarioDTO`, `UsuarioFilterDTO`.

## 13. API de autorización y roles (dominio `authorization`)

Roles de acceso e identidad 2FA. Detalle en
[`domains/authorization`](domains/authorization/use-cases-back.md).

| Método | Path | Descripción | Auth |
|--------|------|-------------|------|
| GET  | `user/getRole` | Listar roles | Sí |
| GET  | `user/roles/{userId}` | Roles de usuario | Sí |
| POST | `user/cambiarClaveUsuarioAutenticacion` | Cambiar contraseña | Sí |
| POST | `user/dfa` | Doble factor (2FA) | Sí |

DTOs: `RolAccesoDTO`, `UsuarioAutenticacionDTO`.

## 14. API de transición de documentos (dominio `document-transition`)

Cambio de estado y trazabilidad (complementa §6). Detalle en
[`domains/document-transition`](domains/document-transition/use-cases-back.md).

| Método | Path | Descripción | Auth |
|--------|------|-------------|------|
| POST | `document/api/changeState` | Cambiar estado (`PedidoVentaAjusteDTO`) | Sí |
| POST | `template/getTrace` | Traza de relaciones | Sí |
| GET  | `template/getTraceFields/{documentId}/{transaction}` | Campos de traza | Sí |

## 15. Cambios y versionado

No hay versionado de API formal. Registrar aquí cambios breaking:

| Fecha | Cambio | Breaking | Afecta |
|-------|--------|:--------:|--------|
| 2026-08-26 | Creación del contract SDD (baseline) | No | Todos |
| 2026-08-26 | Documentación dominio `documents` (endpoints `/rest`,`/document`,`/template`) | No | Documentos |
| 2026-08-26 | Documentación dominio `tasks` (endpoints `/task/*`) | No | Tareas |
| 2026-08-26 | Documentación `accounting`,`massive`,`notifications`,`config-forms`,`persons`,`authorization`,`document-transition` | No | Nuevos dominios |
| 2026-08-29 | Fusión de controllers: unificación de rutas por dominio (`/main/*`→`/document/main/*`, `/rest/*`→`/document/api/*`, `api_account/*`→`/acc/api/*`, `/property/*`→`/api/config/property/*`, `/configuration/*`→`/api/config/configuration/*`, `/process_designer/*`→`/template/designer/*`) | Sí | Frontend, consumers externos |

## 16. Pendientes de contrato

- [x] Fusión de controllers: consolidación de rutas por dominio (2026-08-29).
- [ ] Definir esquemas completos de `UsuarioAutenticacionDTO` / `OrganizacionDTO`.
- [ ] Documentar explícitamente el header `non-duplicate` (session) usado en `/document/api/guardarDocumento`.
- [ ] Decidir y documentar política de expiración/refresh de JWT.
- [ ] Listar códigos de error (`error_code`) estandarizados por dominio.
- [ ] Documentar DTOs nuevos en `openapi.yaml`: `Voucher*`, `AccountDTO`, `ActividadDTO`,
  `PropiedadDTO`, `FileVO`, `UsuarioDTO`, `RolAccesoDTO`, `PedidoVentaAjusteDTO`.

## 17. Otros módulos (servicios / endpoints auxiliares)

Módulos `d3.*` expuestos por HTTP o de servicios, documentados en `specs/domains/<modulo>/`.

### 17.1 Con endpoint HTTP propio

| Método | Path | Descripción | Auth | Dominio |
|--------|------|-------------|------|---------|
| POST | `fe/sign` | Firmar documento (DIAN) | Sí | `fe` |
| POST | `fe/signWithZip` | Firmar con ZIP | Sí | `fe` |
| POST | `fe/signNE` | Firmar nota electrónica | Sí | `fe` |
| POST | `fe/signNEWithZip` | Firmar NE con ZIP | Sí | `fe` |
| POST | `fe/generateCU` | Generar CU/CUFE | Sí | `fe` |
| GET  | `/files/{visibility}/{type}/{year}/{month}/{day}/{filename}` | Servir archivo | Sí | `upload` |
| POST | `/webservice/copy` | Copiar web service | Sí | `webservice` |
| POST | `/template/designer/copy` | Copiar proceso | Sí | `process-designer` |

### 17.2 Módulos de servicio (sin controlador REST propio)

| Módulo | Paquete | Consumido por |
|--------|---------|---------------|
| Multi-tenancy | `d3.multitenancy` | Todos (infra) |
| Inventario | `d3.inventory` | `documents`, `accounting` |
| Dinero/Cajas | `d3.money` | `accounting` |
| Tarifas | `d3.tariff` | `inventory`, cálculo de consumo |
| Reportes | `d3.report` | `/api/getReport`, servlet |
| Correo | `d3.mail` | `notifications`, recuperación clave |
| Homologación | `d3.homologate` | `accounting`, `tariff` |
| Log de transacciones | `d3.document_transaction` | `documents`, `document-transition` |

> Estos módulos no exponen REST propio hoy; su contrato es interno (Svc/Mapper/DTO).

## 18. API de unidades de consumo (dominio `consumption-units`)

Saldo, incremento diario, compra MB/GB y descuento por cargas. Detalle en
[`domains/consumption-units`](domains/consumption-units/use-cases-back.md).

| Método | Path | Descripción | Auth |
|--------|------|-------------|------|
| GET  | `/consumption-units/balance` | Consultar saldo actual | Sí |
| GET  | `/consumption-units/movements` | Listar movimientos históricos | Sí |
| POST | `/consumption-units/purchase` | Comprar MB/GB (`PurchaseRequest { amount, unit: MB|GB, reference }`) | Sí |

DTOs:
- `ConsumptionUnitBalanceDTO { balanceBytes, tenant, state, updatedAt }`
- `ConsumptionUnitMovementDTO { type: INICIAL|INCREMENTO_DIARIO|COMPRA|DESCUENTO_CARGA, amountBytes, reference, date }`
- `PurchaseRequest { amount, unit, reference }`

**Comportamiento transversal (R-CU-007):**
- Todas las respuestas HTTP (salvo exclusiones) incluyen header `X-Consumption-Delay-Seconds: <segundos>` cuando el saldo es negativo.
- Delay aplicado = `min(|saldo_MB|, maxDelaySeconds)` donde `maxDelaySeconds` configurable (default 60).
- Exclusiones: `/health/**`, `/actuator/**`, `/api/ping`, `/api/ok`, `/api/login`, `/document/main/autenticarUsuarioAutenticacion`, `/document/main/googleAuthenticate`, `/document/main/checkToken`, `/document/main/solicitarNuevaClave`, `/consumption-units/**`.

