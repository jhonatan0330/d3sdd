# Dominios de negocio — inventario y avance SDD

Este archivo es el **índice maestro** de dominios del ecosistema D3 (front `d3_front` +
back `d3brain`). Refleja el avance de documentación SDD y del manual de usuario.

Leyenda: ✅ completo · 🔧 en curso · ⏳ pendiente · — no aplica

| Dominio | Descripción | use-cases back | use-cases front | contract | User-guide | Estado global |
|---------|-------------|:--------------:|:---------------:|:--------:|:----------:|:-------------:|
| [**authentication**](#authentication) | Login, Google, checkToken, cambio/recuperación clave, logout | ✅ | — | ✅ | ✅ (login) | 🔧 |
| [**document**](#document) | Expedientes/pedidos: guardar, consultar, listar, changeState, upload, plantillas, transición, log transacciones | ✅ | — | ✅ | ✅ | 🔧 |
| [**tasks**](#tasks) | Crear/asignar tareas (`TaskRest`) | ✅ | — | ✅ | ✅ | 🔧 |
| [**accounting**](#accounting) | Comprobantes y plan contable (`VoucherController`, `PlanAccountingController`) | ✅ | — | ✅ | ✅ | ✅ |
| [**massiveload**](#massiveload) | Carga masiva de documentos (`MassiveController`) | ✅ | ✅ | ✅ | ✅ | ✅ |
| [**notification**](#notification) | Centro de notificaciones (`NotificationController`) | ✅ | — | ✅ | ✅ | 🔧 |
| [**configuration**](#configuration) | Configuración de instancia, propiedades, formularios dinámicos, homologación | ✅ | — | ✅ | ✅ | 🔧 |
| [**users**](#users) | Módulo de usuarios/personas (`UserController`) | ✅ | — | ✅ | ✅ | ✅ |
| [**authorization**](#authorization) | Perfil/autorización/roles/2FA (`UserController` roles) | ✅ | — | ✅ | ✅ | ✅ |
| [**api-external**](#api-external) | API pública `/api/*` (login/get/send/report) | — (transversal) | — | ✅ | — | ✅ |
| [**fe**](#fe) | Facturación electrónica DIAN (`FEController`) | ✅ | — | ✅ | ⏳ | ✅ |
| [**upload**](#upload) | Subida/servido de archivos (`FileController`) | ✅ | — | ✅ | ⏳ | ✅ |
| [**webservice**](#webservice) | Web services externos (`WebServiceController`) | ✅ | — | ✅ | ⏳ | ✅ |
| [**process**](#process) | Diseñador de procesos (`ProcessDesignerController`) | ✅ | — | ✅ | ⏳ | ✅ |
| [**multitenancy**](#multitenancy) | Tenants / multi-tenant | ✅ | — | 🔧 | ⏳ | ✅ |
| [**inventory**](#inventory) | Inventario de productos | ✅ | — | 🔧 | ⏳ | ✅ |
| [**money**](#money) | Cuentas/movimientos/turnos | ✅ | — | 🔧 | ⏳ | ✅ |
| [**tariff**](#tariff) | Tarifas/tarifarios | ✅ | — | 🔧 | ⏳ | ✅ |
| [**report**](#report) | Reportes Jasper | ✅ | — | 🔧 | ⏳ | ✅ |
| [**mail**](#mail) | Correo/plantillas | ✅ | — | 🔧 | ⏳ | ✅ |
| [**assistant**](#assistant) | Asistente de comandos de la SPA (`@doc`, `/módulo`, F9) — **front-only** | — | ✅ | — (reusa document) | ✅ | 🔧 |
| [**consumption-units**](#consumption-units) | Saldo de unidades de consumo, incremento diario, compra MB/GB, descuento por cargas | ⏳ | — | ⏳ | ⏳ | ⏳ |
| [**shared**](#shared) | Objetos transversales: DTOs, servicios utilitarios, interceptores, componentes reutilizables | ✅ | ✅ | — | — | ✅ |
| [**layout**](#layout) | Shell de la interfaz: header, sidebar, footer, navegación, dashboard — **front-only** | — | ✅ | — | ✅ | ✅ |

## Detalle por dominio

# Documentación Integral de Dominios — D3 Platform

> Integración de todas las especificaciones de dominio (`specs.md`), casos de uso backend (`use-cases-back.md`) y frontend (`use-cases-front.md`) en un solo documento.

---

## Tabla de Contenidos

1. [Accounting (ACC)](#1-accounting-acc)
2. [Assistant (ASSISTANT)](#2-assistant-assistant)
3. [Authentication (AUTH)](#3-authentication-auth)
4. [Authorization (AUT)](#4-authorization-aut)
5. [Configuration (CFG)](#5-configuration-cfg)
6. [Consumption-Units (CU)](#6-consumption-units-cu)
7. [Document (DOC)](#7-document-doc)
8. [FE - Facturación Electrónica](#8-fe---facturación-electrónica)
9. [Inventory (INV)](#9-inventory-inv)
10. [Layout (LAY)](#10-layout-lay)
11. [Mail (MAIL)](#11-mail-mail)
12. [MassiveLoad (MAS)](#12-massiveload-mas)
13. [Money (MONEY)](#13-money-money)
14. [Multitenancy (MT)](#14-multitenancy-mt)
15. [Notification (NOT)](#15-notification-not)
16. [Process (PRC)](#16-process-prc)
17. [Report (REPORT)](#17-report-report)
18. [Shared (SHR)](#18-shared-shr)
19. [Tariff (TARIFF)](#19-tariff-tariff)
20. [Tasks (TASK)](#20-tasks-task)
21. [Upload (UPLOAD)](#21-upload-upload)
22. [Users (USR)](#22-users-usr)
23. [WebService (WS)](#23-webservice-ws)
24. [Diagrama de Dependencias](#24-diagrama-de-dependencias)
25. [Convenciones Arquitectónicas](#25-convenciones-arquitectónicas)

---

## 1. Accounting (ACC)

### 1.1 Requisitos

#### Funcionales
- R-ACC-001: Listar, consultar, crear y eliminar comprobantes (vouchers) por catálogo contable.
- R-ACC-002: Generar/recrear comprobantes a partir de documentos del módulo de documentos.
- R-ACC-003: Gestionar rangos de numeración de comprobantes.
- R-ACC-004: Exponer el plan contable: catálogos, cuentas y saldos (`/balance/{catalog}`).
- R-ACC-005: API externa `api_account` para generar comprobantes desde sistemas externos autenticados con `x-api-key`.

#### No funcionales
- NF-ACC-001: Toda operación usa el header `Authorization` (token de sesión); la API externa usa `x-api-key` + `securityToken`.
- NF-ACC-002: Multi-tenant: el tenant se deriva de la sesión, no del request.
- NF-ACC-003: Respuestas de error en formato `SharedApiErrorResponse`.

### 1.2 Casos de Uso Backend

| ID | Nombre | Actor | Estado | Endpoint |
|----|--------|-------|--------|----------|
| CU-ACC-001 | Listar comprobantes por catálogo | Usuario autenticado | ✅ | `GET acc/voucher/{catalog}` |
| CU-ACC-002 | Ver comprobante | Usuario autenticado | ✅ | `GET acc/voucher/one/{voucherId}` |
| CU-ACC-003 | Crear comprobante manual | Usuario autenticado | ✅ | `POST acc/voucher/manual` |
| CU-ACC-004 | Eliminar comprobante manual | Usuario autenticado | ✅ | `DELETE acc/voucher/manual/{voucherId}` |
| CU-ACC-005 | Generar comprobante desde documento | Sistema | ✅ | `POST acc/voucher/generate-voucher` |
| CU-ACC-006 | Obtener id de comprobante por documento | Sistema | ✅ | `POST acc/voucher/document` |
| CU-ACC-007 | Gestionar rangos de comprobante | Usuario autenticado | ✅ | `POST acc/voucher/range-clear-voucher`, `range-create-voucher` |
| CU-ACC-008 | Consultar plan contable | Usuario autenticado | ✅ | `GET acc/plan/catalog`, `account/{catalog}`, `balance/{catalog}` |

### 1.3 Diseño

**Paquetes:** `d3.accounting_voucher`, `d3.accounting_plan`, `d3.accounting_api`

**Componentes:**
- `VoucherController` (`/acc/voucher`): comprobantes manuales y generados
- `PlanAccountingController` (`/acc/plan`): catálogos, cuentas y saldos
- `AccountApiController` (`api_account`): fachada externa con `x-api-key`

**DTOs clave:** `Voucher`, `VoucherDTO`, `VoucherPrepareRequest`, `VoucherRangeRequest`, `AccountDTO`, `CatalogDTO`, `ResultMapDTO`

**Flujo CU-ACC-005:** Documento → `VoucherPrepareRequest` → `VoucherController.generateVoucher` → servicio contable → `SharedIdResponse`

---

## 2. Assistant (ASSISTANT)

### 2.1 Requisitos

> **Front-only** (`d3_front`); no tiene backend propio (reutiliza `documents`).

### 2.2 Casos de Uso Frontend

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-ASSISTANT-001 | Abrir/cerrar el asistente | Usuario | ✅ |
| CU-ASSISTANT-002 | Buscar documento por código (`@código`) | Usuario | ✅ |
| CU-ASSISTANT-003 | Navegar a módulo/plantilla por nombre (`/texto`) | Usuario | ✅ |
| CU-ASSISTANT-004 | Mostrar ayuda / intención desconocida | Sistema (SPA) | ✅ |

#### CU-ASSISTANT-001 — Abrir/cerrar el asistente
1. El usuario pulsa el botón flotante o la tecla **F9**
2. Se despliega el panel lateral derecho (`mat-sidenav` / `cdk-overlay`)
3. El usuario cierra con **Esc**, botón de cierre, o F9 de nuevo

#### CU-ASSISTANT-002 — Buscar documento por código
1. Usuario escribe `@<código>` (p.ej. `@PV-0001`)
2. `AssistantService.interpretar()` detecta intención `buscar-por-arroba`
3. `ejecutar()` llama a `POST /document/getDocuments`
4. Un match → abre documento; varios → lista para elección

#### CU-ASSISTANT-003 — Navegar a módulo/plantilla
1. Usuario escribe `/<texto>` (p.ej. `/pedido`)
2. `filtrarTemplates()` filtra en cliente `TemplateService.template()` (caché)
3. Un match → navega a `/list/list/{templateId}`; varios → lista

### 2.3 Diseño

**D1. Contenedor:** De `MatDialog` a panel lateral derecho (`mat-sidenav`), permite seguir viendo la app mientras está abierto.

**D2. NO es IA:** Intérprete de comandos por prefijos (`@`, `/`) + búsqueda de documentos.

**D3. Dependencias:**
- Plantillas: `GET /template/getTemplates/{profile}` (caché)
- Búsqueda: `POST /document/getDocuments`
- Avatar: `jwtAuth.user()?.imagen` (solo lectura)

### 2.4 Estructura Frontend

```
d3Front/src/app/assistant/
  assistant-button/        # botón flotante (trigger F9)
  assistant-dialog/        # migrar a panel lateral
  assistant.models.ts      # AssistantMessage, AssistantState, AssistantIntent
  assistant.service.ts     # orquestador de comandos
```

---

## 3. Authentication (AUTH)

### 3.1 Requisitos

#### Requisitos funcionales
- **RF-AUTH-001** Autenticar usuario con `sesion` + `clave` vía `POST /main/autenticarUsuarioAutenticacion`
- **RF-AUTH-002** Devolver token de sesión y perfil (`usuarioDTO`, `organizacion`)
- **RF-AUTH-003** Validar token en `Authorization` o `securityToken`
- **RF-AUTH-004** Rechazar acceso si usuario inactivo (`estado = I`)
- **RF-AUTH-005** Autenticar vía Google Sign-In (correo debe existir en `usuario_usrp`)
- **RF-AUTH-006** No auto-registrar desde Google Sign-In
- **RF-AUTH-007** Cambiar clave vía `POST /main/cambiarClave`
- **RF-AUTH-008** Al cambiar clave, cerrar todas las demás sesiones (revocación por `jti`)
- **RF-AUTH-009** Solicitar restablecimiento de clave vía `POST /main/solicitarNuevaClave`
- **RF-AUTH-010** Validar token persistido al arranque de SPA (`POST /main/checkToken`)

#### Requisitos de seguridad
- **RS-AUTH-001** Token JWT HS256 con clave ≥ 32 bytes; `jti` = llave de sesión
- **RS-AUTH-002** No incluir datos sensibles en claims del JWT
- **RS-AUTH-003** No loguear tokens, secretos ni claves
- **RS-AUTH-004** API externa `/api/*` requiere `x-api-key`
- **RS-AUTH-005** Verificar `id_token` de Google validando `aud`, `iss` y `exp`

### 3.2 Casos de Uso Backend

| ID | Nombre | Actor | Estado | Endpoint |
|----|--------|-------|--------|----------|
| CU-AUTH-001 | Login con usuario y clave | Usuario | ✅ | `POST /main/autenticarUsuarioAutenticacion` |
| CU-AUTH-002 | Login con Google Sign-In | Usuario | 🔧 | `POST /main/googleAuthenticate` |
| CU-AUTH-003 | Validar token de sesión | Sistema (SPA) | ✅ | `POST /main/checkToken` |
| CU-AUTH-004 | Cambiar clave | Usuario autenticado | ✅ | `POST /main/cambiarClave` |
| CU-AUTH-005 | Solicitar nueva clave | Usuario | ✅ | `POST /main/solicitarNuevaClave` |
| CU-AUTH-006 | Cerrar sesión | Usuario | ✅ | `LoginService.signout()` |

### 3.3 Diseño

**D1. Modelo de sesión: opaco → JWT**
- `JwtService` genera/parsea/valida JWT HS256
- Claims: `sub`, `userId`, `userName`, `org`, `tenant`, `jti`, `iat`, `exp`
- Compatibilidad: resuelve `jti` si token tiene 3 segmentos; si no, trata como llave opaca

**D2. Google Sign-In:** Verificación con `GoogleIdTokenVerifier`, sin auto-registro, sin Spring Security

**D3. Revocación:** Al cambiar clave → `closeAllSession` cierra todas salvo la actual

**D4. Transporte:** `Authorization: Bearer <token>` (preferido) o campo `securityToken` (legacy)

**D5. Secretos:** `jwt.secret` (≥32 bytes), `jwt.expiration`, `google.client-id` en `application.properties`

### 3.4 Frontend

```
d3_front/src/app/authentication/
  authentication.guard.ts      -- AuthGuard
  authentication.service.ts    -- Estado de autenticación
  login.service.ts             -- Llamadas API de login
  sign-in/                     -- Pantalla de login
  recover-password/            -- Recuperación de contraseña
  new-password/                -- Nueva contraseña
  settings/                    -- Configuración de usuario
  DFA/                         -- Autenticación de dos factores
```

---

## 4. Authorization (AUT)

### 4.1 Requisitos

- R-AUT-001: Catálogo de roles de acceso (`RolAccesoDTO`)
- R-AUT-002: Asignación de roles por usuario
- R-AUT-003: Cambio de contraseña y validación de doble factor (2FA)

### 4.2 Casos de Uso Backend

| ID | Nombre | Actor | Estado | Endpoint |
|----|--------|-------|--------|----------|
| CU-AUT-001 | Listar roles de acceso | Usuario autenticado | ✅ | `GET user/getRole` |
| CU-AUT-002 | Roles asignados a un usuario | Usuario autenticado | ✅ | `GET user/roles/{userId}` |
| CU-AUT-003 | Cambiar contraseña de autenticación | Usuario autenticado | ✅ | `POST user/cambiarClaveUsuarioAutenticacion` |
| CU-AUT-004 | Doble factor de autenticación (2FA) | Usuario autenticado | ✅ | `POST user/dfa` |

### 4.3 Diseño

**Paquetes:** `d3.authorization`, `d3.authentication`

**Componentes:**
- `UserController` (`/user`): `getRole`, `roles/{userId}`, `cambiarClaveUsuarioAutenticacion`, `dfa`
- `RolAccesoSvc`: `listarConsulta`, `consultaUsuarioDocumento`
- `UsuarioAutenticacionSvc`: `cambiarClave`, `dobleFactorAutenticacion`

**DTOs:** `RolAccesoDTO`, `RolAccesoFilterDTO`, `UsuarioAutenticacionDTO`

---

## 5. Configuration (CFG)

### 5.1 Requisitos

#### Configuración y Formularios
- R-CFG-001: Propiedades parametrizables por tipo/campo con valores definidos
- R-CFG-002: Exportar/importar/comparar la configuración de la instancia (`FileVO`)
- R-CFG-003: Formularios dinámicos (módulo `process_form`)

#### Homologación
- R-HOMO-001: Mapeo de cuentas, catálogos, productos, tarifas entre sistemas externos
- R-HOMO-002: Stock y deducciones de stock homologados

### 5.2 Casos de Uso Backend

#### Configuración

| ID | Nombre | Actor | Estado | Endpoint |
|----|--------|-------|--------|----------|
| CU-CFG-001 | Listar propiedades por tipo/campo | Usuario autenticado | ✅ | `GET property/{type}/{field}` |
| CU-CFG-002 | Listar valores definidos por filtro | Usuario autenticado | ✅ | `GET property/type/{type}/{filterName}` |
| CU-CFG-003 | Obtener propiedad por llave | Usuario autenticado | ✅ | `GET property/{key}` |
| CU-CFG-004 | Crear/editar propiedad | Usuario autenticado | ✅ | `POST property/` |
| CU-CFG-005..008 | Export/Import/Compare configuración | Usuario autenticado | ✅ | `GET configuration/export`, `POST configuration/import`, `POST configuration/compare` |

#### Homologación

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-HOMO-001 | Homologar cuentas | Sistema | ✅ |
| CU-HOMO-002 | Homologar catálogos | Sistema | ✅ |
| CU-HOMO-003 | Homologar productos/stock | Sistema | ✅ |
| CU-HOMO-004 | Homologar tarifas/faq/fees | Sistema | ✅ |

### 5.3 Diseño

**Paquetes:** `d3.property`, `d3.configuration_file`, `d3.process_form`, `d3.homologate`

**Componentes:**
- `PropertyController` (`/property`): `PropiedadSvc`, `PropiedadValorDefinidoSvc`
- `ConfigurationController` (`/configuration`): `ExportConfigurationFileService`, `ImportConfigurationFileService`
- Formularios dinámicos: `TemplateController` (`/template`)
- `HomologateAdapterService` + adaptadores de entidad

### 5.4 Frontend

```
d3_front/src/app/configuration/
  config.component.ts              -- Contenedor con tabs
  shared/                          -- Componentes compartidos
  web-services/                    -- Config de servicios web
  messages/                        -- Config de mensajes
  message-templates/               -- Plantillas de mensajes
  document-templates/              -- Plantillas de documentos
  auto-tasks/                      -- Config de tareas automáticas
  processes/                       -- Config de procesos
  organizations/                   -- Config de organizaciones
  consecutives/                    -- Config de consecutivos
  servers/                         -- Config de servidores
  property-values/                 -- Config de valores de propiedad
```

---

## 6. Consumption-Units (CU)

### 6.1 Requisitos

- R-CU-001: Saldo de unidades de consumo (`ConsumptionUnitBalanceDTO`)
- R-CU-002: Inicialización del sistema con 1 MB de saldo
- R-CU-003: Incremento diario automático de 1 MB
- R-CU-004: Compra de unidades (MB o GB) que se suman al saldo
- R-CU-005: Descuento horario por tamaño de archivos subidos
- R-CU-006: Consulta de saldo actual e histórico de movimientos
- R-CU-007: **Retraso progresivo por saldo negativo** — delay N segundos donde N = |MB negativos|

### 6.2 Casos de Uso Backend

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| UC-CU-001 | Inicialización del saldo | Sistema | ✅ |
| UC-CU-002 | Incremento diario (+1 MB) | Sistema (scheduler) | ✅ |
| UC-CU-003 | Compra de unidades (MB/GB) | Administrador | ✅ |
| UC-CU-004 | Descuento horario por cargas | Sistema (scheduler) | ✅ |
| UC-CU-005 | Consulta de saldo y movimientos | Administrador | ✅ |
| UC-CU-006 | Retraso progresivo por saldo negativo | Sistema (interceptor HTTP) | ✅ |

### 6.3 Diseño

**Paquete:** `d3.consumption_units`

**Componentes:**
- `ConsumptionUnitBalanceSvc`
- `ConsumptionUnitMovementSvc`
- `ConsumptionUnitSchedulerSvc` (tareas programadas)
- `ConsumptionUnitDelayInterceptor` (delay por saldo negativo)

**DTOs:**
- `ConsumptionUnitBalanceDTO`: saldo_bytes, tenant, estado, fecha
- `ConsumptionUnitMovementDTO`: tipo (INICIAL/INCREMENTO_DIARIO/COMPRA/DESCUENTO_CARGA), cantidad_bytes

**Tablas:** `consumption_unit_balance_cucb`, `consumption_unit_movement_cucm`

**Retraso (Opción A recomendada):** `HandlerInterceptor.preHandle()` que consulta balance, calcula delay y ejecuta `Thread.sleep()`. Excluye healthchecks, auth, y endpoints propios.

---

## 7. Document (DOC)

### 7.1 Requisitos

#### Documentos / Expedientes
- RF-DOC-001: Crear y editar documento (`PedidoVentaDTO`) vía `POST /rest/guardarDocumento`
- RF-DOC-002: Devolver documento completo en `POST /rest/consultarDocumento`
- RF-DOC-003: Listar documentos filtrados vía `POST /rest/listarDocumentos` y `POST /document/getDocuments`
- RF-DOC-004: Cambiar estado del documento vía `POST /rest/changeState`
- RF-DOC-005: Validar reglas previas a creación vía `POST /rest/validateBeforeNew`
- RF-DOC-006: Aceptar subida de archivos (`MultipartFile`) en `POST /rest/upload`
- RF-DOC-007: Listar plantillas por perfil en `GET /template/getTemplates/{profile}`
- RF-DOC-008: Resolver datos base/dependientes de un campo en `POST /rest/consultarDatosBase`
- RF-DOC-009: Consultar inventario por producto en `GET /document/getInventory/{id}`
- RF-DOC-010: Exponer trazabilidad del documento

#### Transición de estado
- R-DT-001: Transicionar estado de un documento
- R-DT-002: Consultar relaciones/traza de un documento gestor
- R-DT-003: Consultar campos de traza por transacción

#### Log de transacciones
- R-DTX-001: Auditoría de transacciones por documento
- R-DTX-002: Registro de errores de transacción
- R-DTX-003: Log de traza

### 7.2 Casos de Uso Backend

| ID | Nombre | Actor | Estado | Endpoint |
|----|--------|-------|--------|----------|
| CU-DOC-001 | Crear / editar documento | Usuario autenticado | ✅ | `POST /rest/guardarDocumento` |
| CU-DOC-002 | Consultar documento completo | Usuario autenticado | ✅ | `POST /rest/consultarDocumento` |
| CU-DOC-003 | Listar documentos con filtros | Usuario autenticado | ✅ | `POST /rest/listarDocumentos`, `POST /document/getDocuments` |
| CU-DOC-004 | Cambiar estado del documento | Usuario autenticado | ✅ | `POST /rest/changeState` |
| CU-DOC-005 | Validar antes de crear | Sistema | ✅ | `POST /rest/validateBeforeNew` |
| CU-DOC-006 | Subir archivo adjunto | Usuario autenticado | ✅ | `POST /rest/upload` |
| CU-DOC-007 | Gestionar plantillas y campos | Usuario / Admin | ✅ | `GET /template/getTemplates/{profile}` |
| CU-DOC-008 | Consultar datos base de un campo | Sistema | ✅ | `POST /rest/consultarDatosBase` |
| CU-DOC-009 | Consultar inventario por producto | Usuario autenticado | ✅ | `GET /document/getInventory/{id}` |
| CU-DOC-010 | Trazabilidad del documento | Usuario autenticado | ✅ | `POST /template/getTrace`, `GET /template/getTraceFields/{documentId}/{transaction}` |

### 7.3 Transición de Estado

| ID | Nombre | Actor | Estado | Endpoint |
|----|--------|-------|--------|----------|
| CU-DT-001 | Cambiar estado | Usuario/Sistema | ✅ | `POST rest/changeState` |
| CU-DT-002 | Traza de relaciones | Usuario autenticado | ✅ | `POST template/getTrace` |
| CU-DT-003 | Campos de traza | Usuario autenticado | ✅ | `GET template/getTraceFields/{documentId}/{transaction}` |

### 7.4 Log de Transacciones

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-DTX-001 | Registrar log de transacción | Sistema | ✅ |
| CU-DTX-002 | Registrar error de transacción | Sistema | ✅ |
| CU-DTX-003 | Consultar transacciones de un documento | Sistema | ✅ |

### 7.5 Diseño

**Paquetes:**
- `d3.document_execution` (ejecución de documentos)
- `d3.document_transition` (transición de estado)
- `d3.document_transaction` (log de transacciones)
- `d3.process_form` (formularios dinámicos)

**Controladores:** `APIController` (`/rest`), `DocumentController` (`/document`), `TemplateController` (`/template`)

**DTOs:**
- Documentos: `PedidoVentaDTO`, `PedidoVentaFilterDTO`, `PedidoVentaAjusteDTO`
- Transición: `DocumentoRelacionGestorFilterDTO`, `DocumentoRelacionGestorDTO`, `PedidoVentaCaracteristicaDTO`
- Log: `DocumentoTransaccionDTO`, `TransaccionLogDTO`, `TransaccionErrorDTO`

**Flujo de guardado:** `llaveTabla == null` → crear; si no → actualizar. Header `non-duplicate` para idempotencia.

### 7.6 Frontend

```
d3_front/src/app/document/
  form/                          -- Motor de formularios dinámicos
    controls/                    -- 18 tipos de control:
      archivo/  base/  binario/  configuracion/  croquis/  detalle/
      disponibilidad/  fecha/  gps/  gps-map/  informative/  numero/
      proceso/  product/  producto-lista/  seccion/  texto/  vinculo/
    form.component.ts
  model/                         -- Modelos de dominio
  service/                       -- Servicios API
  cruds/                         -- Componente CRUD list
  trazability/                   -- Trazabilidad
  document-transition.service.ts
```

---

## 8. FE - Facturación Electrónica

### 8.1 Requisitos

- R-FE-001: Firmar XML de facturación y notas electrónicas con certificado digital
- R-FE-002: Generar el Código Único (CUF/E) según normativa DIAN
- R-FE-003: Soporte de carga vía ZIP

### 8.2 Casos de Uso Backend

| ID | Nombre | Actor | Estado | Endpoint |
|----|--------|-------|--------|----------|
| CU-FE-001 | Firmar documento (XML) | Sistema | ✅ | `POST fe/sign` |
| CU-FE-002 | Firmar documento desde ZIP | Sistema | ✅ | `POST fe/signWithZip` |
| CU-FE-003 | Firmar nota electrónica (NE) | Sistema | ✅ | `POST fe/signNE` |
| CU-FE-004 | Firmar NE desde ZIP | Sistema | ✅ | `POST fe/signNEWithZip` |
| CU-FE-005 | Generar CU/CUFE | Sistema | ✅ | `POST fe/generateCU` |

### 8.3 Diseño

**Paquete:** `d3.fe`

**Componentes:**
- `FEController` (`/fe`): `sign`, `signWithZip`, `signNE`, `signNEWithZip`, `generateCU`
- `SignerService`: lógica de firma
- `DianSoapSecurityHeader`: cabecera SOAP para WS DIAN
- `FEResponse`: respuesta de firma

---

## 9. Inventory (INV)

### 9.1 Requisitos

- R-INV-001: Catálogo de productos (`ProductoDTO`) y filtros
- R-INV-002: Movimientos de inventario (`ProductoInventarioDTO`)
- R-INV-003: Trazabilidad y descuentos/deducciones

### 9.2 Casos de Uso Backend

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-INV-001 | Gestionar productos | Sistema | ✅ |
| CU-INV-002 | Gestión de inventario de producto | Sistema | ✅ |
| CU-INV-003 | Trazabilidad de producto en inventario | Sistema | ✅ |
| CU-INV-004 | Deducciones de producto | Sistema | ✅ |

### 9.3 Diseño

**Paquete:** `d3.inventory` (módulo de servicios, sin controlador REST propio)

**Componentes:** `ProductoSvc`, `ProductoInventarioSvc`, `TrazabilidadProductoInventarioSvc`, `DeduccionProductoSvc`, `ProductoInventarioDescuentoSvc`

**DTOs:** `ProductoDTO`, `ProductoInventarioDTO`, `TrazabilidadProductoInventarioDTO`, `DeduccionProductoDTO`, `ProductoInventarioDescuentoDTO`

---

## 10. Layout (LAY)

### 10.1 Requisitos

- R-LAY-001: Shell de la aplicación con header, sidebar y contenido principal
- R-LAY-002: Navegación lateral (menú colapsable) con rutas lazy-loaded
- R-LAY-003: Dashboard con indicadores y tarjetas resumen
- R-LAY-004: Gestión de usuario (avatar, cerrar sesión, configuración)
- R-LAY-005: Atajos de teclado y accesos directos

### 10.2 Frontend (Sin Backend)

```
d3_front/src/app/layout/
  layout.component.ts         -- Shell principal (mat-sidenav + router-outlet)
  dashboard/                  -- Tarjetas resumen e indicadores
  user/                       -- Avatar, cerrar sesión
  simple-nav/                 -- Sidebar de navegación
  navigation/                 -- Configuración de menú
  shortcuts/                  -- Atajos de teclado
  change-picture/             -- Cambio de imagen de perfil
  core/config/                -- Configuración del layout
```

---

## 11. Mail (MAIL)

### 11.1 Requisitos

- R-MAIL-001: Envío de correos transaccionales (`MensajeDTO`)
- R-MAIL-002: Plantillas de correo (`MensajePlantillaCorreoDTO`)
- R-MAIL-003: Recuperación de contraseña por correo

### 11.2 Casos de Uso Backend

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-MAIL-001 | Enviar mensaje de correo | Sistema | ✅ |
| CU-MAIL-002 | Recuperar contraseña por correo | Sistema | ✅ |
| CU-MAIL-003 | Plantillas de correo | Sistema | ✅ |
| CU-MAIL-004 | Cola de envío | Sistema | ✅ |

### 11.3 Diseño

**Paquete:** `d3.mail` (módulo de servicios, sin controlador REST propio)

**Componentes:** `MailGenerateMessageService`, `MailSendMessageService`, `MailRecoverPasswordService`, `MailUserSendMessage`, `MailReleaseMessageQueueService`, `MailSendMessageToAdminService`, `MensajePlantillaCorreoSvc`, `MensajeSvc`

---

## 12. MassiveLoad (MAS)

### 12.1 Requisitos

- R-MAS-001: Procesar ítems de carga masiva de forma incremental
- R-MAS-002: Ejecutar una carga completa desde un archivo (`upload` → `validate` → `execute`)
- R-MAS-003: Administrar ítems y maestros de carga (CRUD)

### 12.2 Casos de Uso Backend

| ID | Nombre | Actor | Estado | Endpoint |
|----|--------|-------|--------|----------|
| CU-MAS-001 | Sincronizar ítem de carga masiva | Sistema/Scheduler | ✅ | (interno) |
| CU-MAS-002 | Ejecutar carga masiva | Usuario autenticado | ✅ | (orquestado) |
| CU-MAS-003 | Gestionar ítems de carga | Usuario autenticado | ✅ | (CRUD) |
| CU-MAS-004 | Gestionar maestros de carga | Usuario autenticado | ✅ | (CRUD) |
| CU-MAS-005 | Descargar archivo base (plantilla) | Usuario autenticado | 🔧 | `POST massiveload/template` (pendiente) |
| CU-MAS-006 | Cargar el archivo | Usuario autenticado | ✅ | `POST massiveload/upload` |
| CU-MAS-007 | Procesar el archivo | Usuario autenticado | ✅ | `POST massiveload/validate/{loadId}`, `POST massiveload/execute/{loadId}` |
| CU-MAS-008 | Consultar historial de cargas | Usuario autenticado | ✅ | `GET massiveload/{loadId}`, `GET massiveload/{loadId}/items` |

### 12.3 Casos de Uso Frontend

#### CU-MAS-005 — Descargar plantilla
1. Dropdown con formato: `xlsx` (default), `xml`, `json`
2. Usuario escoge formato y presiona "Descargar plantilla"
3. Se abre/descarga la URL del archivo

#### CU-MAS-006 — Cargar archivo
1. Usuario selecciona archivo y hace clic en "Cargar"
2. SPA envía archivo/referencia al backend
3. Muestra confirmación y `loadId` resultante

#### CU-MAS-007 — Procesar archivo
1. Usuario hace clic en "Procesar" sobre la carga
2. SPA invoca validación y luego ejecución por `loadId`
3. Muestra estado/resultado

### 12.4 Diseño

**Paquete:** `d3.massiveload`

**Controlador único:** `MassiveController` (`massiveload`)

**Servicios:** `MassiveLoadOrchestratorService`, `MassiveCRUDItemService`, `MassiveCRUDMasterService`, `MassiveFileParserService`, `MassiveValidationService`, `MassiveDocumentBuilderService`

**DTOs:** `MassiveMasterRequest`, `MassiveMasterDTO`, `MassiveItemDTO`, `MasivaItemRequest`, `MassiveMasterFilter`, `MassiveItemFilter`

---

## 13. Money (MONEY)

### 13.1 Requisitos

- R-MON-001: Catálogo de cuentas (`CuentaDTO`)
- R-MON-002: Movimientos contables de caja (`MovimientoDTO`)
- R-MON-003: Turnos (`TurnoDTO`)

### 13.2 Casos de Uso Backend

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-MON-001 | Gestionar cuentas | Sistema | ✅ |
| CU-MON-002 | Registrar movimientos | Sistema | ✅ |
| CU-MON-003 | Gestionar turnos | Sistema | ✅ |

### 13.3 Diseño

**Paquete:** `d3.money` (módulo de servicios, sin controlador REST propio)

**Componentes:** `CuentaSvc`, `MovimientoSvc`, `TurnoSvc`

---

## 14. Multitenancy (MT)

### 14.1 Requisitos

- R-MT-001: Aislamiento de datos por tenant vía `TenantRoutingDataSource`
- R-MT-002: Registro/descubrimiento de tenants (`TenantRegistry`, `DatabaseTenantRegistry`)
- R-MT-003: Creación y prueba de nuevos tenants

### 14.2 Casos de Uso Backend

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-MT-001 | Resolución de tenant por request (catálogo JDBC) | Sistema | ✅ |
| CU-MT-002 | Crear/registrar tenant | Admin | 🔧 (backlog INF-NEW-001) |
| CU-MT-003 | Iterar sobre tenants (jobs) | Sistema | ✅ |

### 14.3 Diseño

**Paquete:** `d3.multitenancy` (infraestructura transversal)

**Componentes:**
- `TenantFilter`, `TenantContext`: resolución por request
- `TenantRoutingDataSource`, `TenantDataSourceFactory`, `TenantDataSourceConfiguration`: enrutamiento JDBC
- `DatabaseTenantRegistry`, `DatabaseTenantMetadataProvider`, `TenantMetadataProvider`: registro/metadata
- `TenantIteratorService`: iteración por tenant

**Regla clave:** El tenant NO viaja en el request; se deriva de la sesión/API key.

---

## 15. Notification (NOT)

### 15.1 Requisitos

- R-NOT-001: Bandeja de actividades del usuario autenticado
- R-NOT-002: Marcar actividad como leída
- R-NOT-003: Reasignar (transferir) una actividad a otro usuario
- R-NOT-004: Listar usuarios elegibles para transferencia

### 15.2 Casos de Uso Backend

| ID | Nombre | Actor | Estado | Endpoint |
|----|--------|-------|--------|----------|
| CU-NOT-001 | Listar actividades/notificaciones del usuario | Usuario autenticado | ✅ | `GET notification/getNotifications` |
| CU-NOT-002 | Marcar actividad como leída | Usuario autenticado | ✅ | `POST notification/readActivity` |
| CU-NOT-003 | Transferir (reasignar) actividad | Usuario autenticado | ✅ | `POST notification/transfer` |
| CU-NOT-004 | Listar usuarios para transferir | Usuario autenticado | ✅ | `POST notification/userToTransfer` (requiere `documento` en `ActividadDTO`) |

### 15.3 Diseño

**Paquetes:** `d3.notification`, `d3.logisticpymes`

**Componentes:**
- `NotificationController` (`/notification`)
- `ActividadSvc`: `listUserActivities`, `readActivity`, `guardar`
- `UsuarioSvc`: `getUsersState`

**DTOs:** `ActividadDTO` (`llaveTabla`, `documento`), `UsuarioDTO`

### 15.4 Frontend

```
d3_front/src/app/notification/
  notification.service.ts
  notification-center.service.ts
  notification.types.ts
  notification-button/          -- Botón de notificaciones
  transfer-form/                -- Formulario de transferencia
```

---

## 16. Process (PRC)

### 16.1 Requisitos

- R-PRC-001: Definir procesos, estados y transiciones (manuales y automáticas)
- R-PRC-002: Copiar definiciones de proceso

### 16.2 Casos de Uso Backend

| ID | Nombre | Actor | Estado | Endpoint |
|----|--------|-------|--------|----------|
| CU-PRC-001 | Copiar proceso | Usuario autenticado | ✅ | `POST /process_designer/copy` |
| CU-PRC-002 | Gestionar procesos/estados/transiciones | Usuario autenticado | 🔧 | (pendiente documentar CRUD) |

### 16.3 Diseño

**Paquete:** `d3.process_designer`

**Componentes:**
- `ProcessDesignerController` (`/process_designer`): `copy`
- `ProcesoSvc`, `ProcesoEstadoSvc`, `ProcesoTransicionSvc`, `ProcesoTransicionAutomaticaSvc`, `ProcessCopy`

**DTOs:** `ProcesoDTO`, `ProcesoEstadoDTO`, `ProcesoTransicionDTO`, `ProcesoTransicionAutomaticaDTO`

---

## 17. Report (REPORT)

### 17.1 Requisitos

#### Generación de reportes
- R-REP-001: Generar reporte Jasper en formatos PDF, Excel (XLS) y HTML desde plantilla JRXML
- R-REP-002: Generar reporte CSV ejecutando SQL nativo (`REPORT_QUERY`) con sustitución de parámetros `$P{param}`
- R-REP-003: Soporte para subreportes dinámicos (`P_SUBREPORT_*`), encabezados y pies de página
- R-REP-004: Auto-impresión PDF vía JavaScript
- R-REP-005: Configuración de tipo de exportación por defecto por reporte
- R-REP-006: Conexión a base de datos externa configurable por reporte

#### Gestión de definiciones de reporte (CRUD)
- R-REP-020: CRUD de `ReporteBaseDTO` (código, nombre, plantilla, público, propiedades)
- R-REP-021: Validación de unicidad de código y nombre en activo
- R-REP-022: Listar reportes disponibles por documento/plantilla
- R-REP-023: Listar reportes para menú lateral

#### Ejecución y auditoría
- R-REP-030: Registro de ejecución en `ReporteEjecucionDTO`
- R-REP-031: Restricción de una sola ejecución por documento (`REP_PRINT_ONE`)
- R-REP-032: Almacenamiento automático de archivo generado en storage
- R-REP-033: Histórico de ejecuciones si documento tiene `historico > 0`
- R-REP-034: Consulta y filtrado de historial

#### Acceso y seguridad
- R-REP-040: Autenticación por token JWT/sesión
- R-REP-041: Acceso a reportes públicos (`publico=true`) sin token
- R-REP-042: Validación de permisos vía `PropiedadSvc`

### 17.2 Casos de Uso Backend

| ID | Nombre | Actor | Estado | Endpoint |
|----|--------|-------|--------|----------|
| CU-REP-001 | Generar reporte Jasper (PDF/Excel/HTML) | Usuario autenticado | 🔧 | `GET /ReporteServlet` (legacy), `GET /api/reports/generate` (REST) |
| CU-REP-002 | Ejecutar reporte SQL directo (CSV) | Usuario autenticado | 🔧 | (mismo flujo CU-REP-001 con `REPORT_QUERY`) |
| CU-REP-003 | Gestionar definiciones de reporte (CRUD) | Administrador | 🔧 | REST `/api/reportes/*` |
| CU-REP-004 | Listar reportes disponibles por documento | Usuario autenticado | 🔧 | (usado por frontend) |
| CU-REP-005 | Consultar historial de ejecuciones | Usuario autenticado | 🔧 | REST |
| CU-REP-006 | Acceso a reporte público sin autenticación | Usuario anónimo | 🔧 | (mismo endpoint sin token) |

### 17.3 Diseño

**Paquete:** `d3.report`

**Componentes principales:**
- `ReporteServlet` (legacy) / `ReportRestController` (objetivo)
- `ReporteBaseSvc`: servicio principal, orquesta generación
- `GeneradorReportes`: conexión JasperReports + cache
- `ReportesUtil`: exportación PDF/Excel/HTML
- `ReportGenerateFromSql`: SQL directo → CSV
- `JasperReportCache`: cache de reportes compilados

**Propiedades clave del reporte:**

| Propiedad | Descripción |
|-----------|-------------|
| `REPORTE_JRXML` | JRXML principal (body) |
| `REPORTE_EXCEL` | JRXML específico Excel |
| `REPORTE_ENCABEZADO` / `REPORTE_ENCABEZADO_EXCEL` | Subreporte encabezado |
| `REPORTE_PIE_PAGINA` | Subreporte pie de página |
| `P_SUBREPORT_*` | Subreportes dinámicos |
| `REPORT_QUERY` | SQL nativo para CSV |
| `REP_TYPE_EXPORT` | Default: `PDF`, `XLS`, `HTML`, `CSV` |
| `CONNECTION_STRING_DB` | DS externo (`url;;user;;pass`) |
| `REP_PRINT_ONE` | Solo 1 ejecución por documento |
| `REP_EXCLUDE_STORAGE_FILE` | No guardar en storage |

### 17.4 Diseño Report Viewer

- Viewer multi-tenant con PDF/HTML embebido en navegador
- `ReportViewerController` (`/api/reports`)
- `ReportViewerComponent` (Angular standalone con `ng2-pdf-viewer`)
- Header `X-Tenant-ID` interceptor
- Cache Caffeine para vistas

### 17.5 Migración Servlet → REST

**4 fases:**
1. Backend paralelo: crear `ReportRestController` con misma lógica
2. Frontend feature flag: `environment.useReportRestApi`
3. Cutover: activar flag en producción
4. Limpieza: eliminar `ReporteServlet`

**Estrategia archivos grandes:** <10MB binary inline, >10MB redirect 302 a storage

---

## 18. Shared (SHR)

### 18.1 Requisitos

- R-SHR-001: DTOs compartidos: `BasicDTO`, `BasicFilterDTO`, `BasicParamDTO`
- R-SHR-002: Servicios utilitarios: `CopierService`, `ErrorHandlerService`, `FileHandlerService`, `PdfService`
- R-SHR-003: Interceptores HTTP: `TokenInterceptor`, `ErrorInterceptor`
- R-SHR-004: Componentes reutilizables: `app-dropdown`, `bpm-diagram`, `bpm-leaf-diagram`, `visor-pdf-dialog`

### 18.2 Diseño

**Regla:** `shared` no puede depender de ningún dominio específico. Los dominios pueden importar de `shared`. Si un objeto se usa en ≤2 dominios, va en ese dominio.

**Backend:** `d3.shared` (domain/application/infrastructure)

**Frontend:** `src/app/shared/` (components/, services/, models/, guards/, interceptors/, utils/)

---

## 19. Tariff (TARIFF)

### 19.1 Requisitos

- R-TAR-001: Catálogo de tarifas (`TarifaDTO`)
- R-TAR-002: Agrupación en tarifarios (`TarifarioDTO`)

### 19.2 Casos de Uso Backend

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-TAR-001 | Gestionar tarifas | Sistema | ✅ |
| CU-TAR-002 | Gestionar tarifarios | Sistema | ✅ |

### 19.3 Diseño

**Paquete:** `d3.tariff` (módulo de servicios, sin controlador REST propio)

**Componentes:** `TarifarioService`, `TarifaSvc`

---

## 20. Tasks (TASK)

### 20.1 Requisitos

- RF-TASK-001: Listar tareas del usuario autenticado en `GET /task/`
- RF-TASK-002: Devolver una tarea por id en `GET /task/{id}?id={key}`
- RF-TASK-003: Crear tarea en `POST /task/create` (`TaskRequest`)
- RF-TASK-004: Actualizar tarea en `POST /task/update`
- RF-TASK-005: Eliminar tarea en `POST /task/delete/{id}`
- RF-TASK-006: Toda operación requiere token válido

### 20.2 Casos de Uso Backend

| ID | Nombre | Actor | Estado | Endpoint |
|----|--------|-------|--------|----------|
| CU-TASK-001 | Listar tareas del usuario | Usuario autenticado | ✅ | `GET /task/` |
| CU-TASK-002 | Ver tarea por id | Usuario autenticado | ✅ | `GET /task/{id}?id={key}` |
| CU-TASK-003 | Crear tarea | Usuario autenticado | ✅ | `POST /task/create` |
| CU-TASK-004 | Actualizar tarea | Usuario autenticado | ✅ | `POST /task/update` |
| CU-TASK-005 | Eliminar tarea | Usuario autenticado | ✅ | `POST /task/delete/{id}` |

### 20.3 Diseño

**Paquetes:** `d3.task.domain`, `d3.task.application`, `d3.task.infrastructure`

**Componentes:** `TaskRest` (infrastructure), `TaskCreateService`, `TaskUpdateService`, `TaskDeleteService`, `TaskGetByUserService`, `TaskService` (base)

**DTOs:** `TaskDTO`, `TaskFilterDTO`, `TaskRequest`
- Campos: `title` (obligatorio en creación), `notes`, `dueDate`, `priority` (default 1), `order` (default 0), `completed`

**Formato fechas:** `yyyy-MM-dd@HH:mm:ss.SSSZ` (timezone `America/Bogota`)

**Nota:** `GET /task/{id}` toma `id` de query param (`@RequestParam`), no del path.

### 20.4 Frontend

```
d3_front/src/app/task/
  tasks.component.ts
  tasks.service.ts
  tasks.types.ts
```

---

## 21. Upload (UPLOAD)

### 21.1 Requisitos

- R-UP-001: Servir archivos subidos por ruta compuesta (visibilidad/tipo/año/mes/día/nombre)
- R-UP-002: Gestionar la carga de archivos (`CargaArchivoDTO`)

### 21.2 Casos de Uso Backend

| ID | Nombre | Actor | Estado | Endpoint |
|----|--------|-------|--------|----------|
| CU-UP-001 | Servir/descargar archivo por ruta | Usuario autenticado | ✅ | `GET /files/{visibility}/{type}/{year}/{month}/{day}/{filename}` |
| CU-UP-002 | Cargar archivo | Sistema | 🔧 | (consumido internamente por `/rest/upload`) |

### 21.3 Diseño

**Paquete:** `d3.upload`

**Componentes:**
- `FileController` (`/files`): sirve archivos
- `CargaArchivoSvc`, `UploadSvc`: lógica de carga

**Nota:** El endpoint de carga real está en `documents` domain (`POST /rest/upload`). Este dominio solo provee almacenamiento y descarga.

---

## 22. Users (USR)

### 22.1 Requisitos

- R-USR-001: Catálogo de usuarios filtrable por rol
- R-USR-002: Consulta de usuario por id y por documento
- R-USR-003: Propiedades (configuración) asignadas por usuario

### 22.2 Casos de Uso Backend

| ID | Nombre | Actor | Estado | Endpoint |
|----|--------|-------|--------|----------|
| CU-USR-001 | Listar usuarios por rol/filtro | Usuario autenticado | ✅ | `POST user/getUsers` |
| CU-USR-002 | Obtener usuario por id | Usuario autenticado | ✅ | `GET user/{userId}` |
| CU-USR-003 | Obtener usuario por documento | Usuario autenticado | ✅ | `GET user/document/{documentId}` |
| CU-USR-004 | Propiedades asignadas a un usuario | Usuario autenticado | ✅ | `GET user/properties/{userId}` |

### 22.3 Diseño

**Paquetes:** `d3.logisticpymes` (UsuarioDTO, UsuarioSvc), `d3.property` (PropertyGetWithCacheService)

**Componentes:**
- `UserController` (`/user`)
- `UsuarioSvc`: `listarRol`, `consultaXId`, `getUserByDocument`
- `PropertyGetWithCacheService`: `getToUser`

### 22.4 Frontend

```
d3_front/src/app/users/
  persons.component.ts
  contact.services.ts
  detail_persons/
```

---

## 23. WebService (WS)

### 23.1 Requisitos

- R-WS-001: Definir y copiar web services externos
- R-WS-002: Ejecutar web services con preparación de parámetros (`WebServiceCallPrepare`)

### 23.2 Casos de Uso Backend

| ID | Nombre | Actor | Estado | Endpoint |
|----|--------|-------|--------|----------|
| CU-WS-001 | Copiar definición de web service | Usuario autenticado | ✅ | `POST /webservice/copy` |
| CU-WS-002 | Ejecutar web service | Sistema | 🔧 | (pendiente documentar endpoint) |

### 23.3 Diseño

**Paquete:** `d3.webservice`

**Componentes:**
- `WebServiceController` (`/webservice`): `copy`
- `WebServiceSvc`, `WebServiceEjecucionSvc`, `WebServiceExecuteAPI`, `WebServiceCopyAPI`
- `WebClientConfig`: cliente para invocación externa

**DTOs:** `WebServiceDTO`, `WebServiceEjecucionDTO`, `WebServiceFilterDTO`, `WebServiceEjecucionFilterDTO`

---

## 24. Diagrama de Dependencias

```
                    ┌─────────────────┐
                    │  MULTITENANCY   │
                    │  (Infraestr.)   │
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │  AUTHENTICATION │
                    │  (JWT/Session)  │
                    └────────┬────────┘
                             │
              ┌──────────────┼──────────────┐
              │              │              │
     ┌────────▼───────┐ ┌───▼────────┐ ┌──▼──────────┐
     │ AUTHORIZATION  │ │   USERS    │ │   SHARED    │
     │ (Roles/2FA)   │ │ (Catálogo) │ │ (DTOs/Svcs) │
     └────────┬───────┘ └───┬────────┘ └──┬──────────┘
              │              │              │
              └──────────────┼──────────────┘
                             │
              ┌──────────────┼──────────────┐
              │              │              │
     ┌────────▼───────┐ ┌───▼────────┐ ┌──▼──────────┐
     │ CONFIGURATION  │ │   PROCESS  │ │    MAIL     │
     │ (Props/Forms)  │ │ (Workflow) │ │ (Email)     │
     └────────┬───────┘ └───┬────────┘ └──┬──────────┘
              │              │              │
              └──────────────┼──────────────┘
                             │
              ┌──────────────┼──────────────┐
              │              │              │
     ┌────────▼───────┐ ┌───▼────────┐ ┌──▼──────────┐
     │   DOCUMENT     │ │ INVENTORY  │ │   TARIFF    │
     │ (CORE DOMAIN)  │ │ (Products) │ │ (Pricing)   │
     └───┬────┬───┬───┘ └────────────┘ └─────────────┘
         │    │   │
    ┌────┘    │   └────────┐
    │         │            │
┌───▼───┐ ┌──▼──────┐ ┌───▼──────┐
│  FE   │ │ACCOUNTING│ │  REPORT  │
│(XML)  │ │(Vouchers)│ │(Jasper)  │
└───────┘ └─────────┘ └──────────┘

    ┌──────────────────────────────────┐
    │        CROSS-CUTTING             │
    │  NOTIFICATION ──── MAIL          │
    │  CONSUMPTION-UNITS ──── UPLOAD   │
    │  MASSIVELOAD ──── DOCUMENT       │
    │  TASKS (independent)             │
    │  ASSISTANT (frontend only)       │
    │  LAYOUT (frontend only)          │
    └──────────────────────────────────┘
```

---

## 25. Convenciones Arquitectónicas

| Aspecto | Convención |
|---------|-----------|
| **Paquetes Backend** | `d3.*` root con capas: domain/application/infrastructure |
| **Controller Base** | `@RestController` con prefijo de dominio |
| **Multi-tenancy** | `TenantFilter` → `TenantContext` (ThreadLocal), derivado de sesión/API key |
| **Autenticación** | `Authorization: Bearer <JWT>` (preferido) o campo `securityToken` (legacy) |
| **API Externa** | Header `x-api-key` |
| **Error Format** | `SharedApiErrorResponse` |
| **ORM** | MyBatis 3.0.5 con XML mappers por dominio |
| **Frontend Framework** | Angular 22.1.0 + Tailwind CSS + FuseAdmin template |
| **Frontend State** | RxJS + Angular Signals (zoneless) |
| **Testing Frontend** | Vitest |
| **Testing Backend** | JUnit (Spring Boot Test) |
| **Lenguaje** | Código en español (nombres de métodos, mensajes, comentarios) |

---

> **Documento generado el:** 2026-08-30
> **Fuentes:** `sdd/specs/domains/*/specs.md`, `sdd/specs/domains/*/use-cases-back.md`, `sdd/specs/domains/*/use-cases-front.md`
