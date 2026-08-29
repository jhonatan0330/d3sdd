# Backlog consolidado — Tareas y nuevos casos de uso (SDD)

Archivo único con **todas las tareas de implementación** y nuevas funcionalidades del
ecosistema D3. Diseñado para que una IA pueda procesarlo sin consultar múltiples archivos.

Leyenda: `[ ]` pendiente · `[x]` hecho · `[~]` en curso · 🔴 alta · 🟡 media · 🟢 baja

---

## ARCHITECTURE

> Tareas pendientes de estandarización y decisiones arquitectónicas globales.
> Ver [`ARCHITECTURE.md`](ARCHITECTURE.md) para los estándares actuales.

- [x] **ARCH-001** 🔴 Documentar estándar de nombres de paquetes `d3.*` en `ARCHITECTURE.md` (ya realizado, verificar cobertura). Ver [ARCH-001](backlog-strategies/ARCH-001-package-rename.md).
- [ ] **ARCH-002** 🔴 Definir estándar de endpoints REST: métodos HTTP, formato de rutas, convenciones de Response.
- [ ] **ARCH-003** 🔴 Estandarizar formato de DTOs: sufijos (`DTO`, `FilterDTO`, `Request`), campos comunes (`llaveTabla`, `estado`, `tenant`).
- [ ] **ARCH-004** 🔴 Formalizar convención de multi-tenancy: tenant resuelto desde sesión, nunca en request.
- [ ] **ARCH-005** 🟡 Definir estándar de manejo de errores: formato `SharedApiErrorResponse`, códigos comunes.
- [ ] **ARCH-006** 🟡 Documentar convenciones MyBatis: nombres de tablas (`<prefijo>_<nombre>`), PKs (`_llave`), tenants (`_tenant`).
- [ ] **ARCH-007** 🟡 Formalizar proceso de cambios en el contrato API (breaking vs non-breaking).
- [ ] **ARCH-008** 🟢 Definir estándar de autenticación: JWT claims mínimos, API key, header `Authorization`.
- [ ] **ARCH-009** 🟢 Documentar convenciones de frontend Angular: estructura de servicios, componentes, models.
- [ ] **ARCH-010** 🟢 Estandarizar formato de fechas: `yyyy-MM-dd@HH:mm:ss.SSSZ`, timezone `America/Bogota`.

---

## AUTHENTICATION

### Nuevos casos de uso

- [ ] **AUTH-NEW-001** 🔴 Logout explícito remoto (`POST /main/cerrarSesion`) — cierra la
  sesión en `usuariosesion_ussp`. (Relacionado T-AUTH-008.)
- [ ] **AUTH-NEW-002** 🔴 Refresh token / renovación de JWT antes de expirar.
- [ ] **AUTH-NEW-003** 🟡 "Ingresar con Google" en la SPA (botón + flujo). (Relacionado T-AUTH-016.)
- [ ] **AUTH-NEW-004** 🟡 MFA / segundo factor para usuarios admin.
- [ ] **AUTH-NEW-005** 🟢 Bloqueo por intentos fallidos de login.

### Tareas de implementación

- [x] **T-AUTH-001** Endpoint `POST /main/autenticarUsuarioAutenticacion` (login opaco).
- [x] **T-AUTH-002** Endpoint `POST /main/checkToken` con validación por `securityToken`.
- [x] **T-AUTH-003** Endpoint `POST /main/cambiarClave` (+ `cambiarClaveOtherSystem`).
- [x] **T-AUTH-004** Endpoint `POST /main/solicitarNuevaClave`.
- [x] **T-AUTH-005** Endpoint `GET /main/obtenerPrincipalOrganizacion`.
- [~] **T-AUTH-006** Migración a JWT HS256 (`JwtService`, claims, `jti`). Ver `specs.md` D1.
  - [ ] Generación de JWT en `autenticar()` y flujo Google.
  - [ ] Validación dual JWT/opaco en `UsuarioSesionSvc`.
  - [ ] `closeAllSession` al cambiar clave resolviendo `jti`.
- [~] **T-AUTH-007** Endpoint `POST /main/googleAuthenticate` + `GoogleAuthenticationService`
  (GoogleIdTokenVerifier). Ver `specs.md` D2.
  - [ ] Verificación `aud`/`iss`/`exp`.
  - [ ] Búsqueda por `cusr_correo` y validación estado.
- [ ] **T-AUTH-008** Endpoint `POST /main/cerrarSesion` (logout explícito que invalide sesión remota).
- [ ] **T-AUTH-009** Política de expiración/refresh de JWT (definir `jwt.expiration` por defecto).
- [x] **T-AUTH-010** `LoginService.signin()` → `/main/autenticarUsuarioAutenticacion`.
- [x] **T-AUTH-011** `LoginService.checkTokenIsValid()` → `/main/checkToken` + re-hidratación.
- [x] **T-AUTH-012** `LoginService.changePwd()` / `changePwdOther` → `/main/cambiarClave`.
- [x] **T-AUTH-013** `LoginService.recoverPassword()` → `/main/solicitarNuevaClave`.
- [x] **T-AUTH-014** `LoginService.signout()` limpia token local y navega a `/sign-in`.
- [ ] **T-AUTH-015** Adaptar el front para leer JWT (parsear `exp`) y refrescar/redirigir al expirar.
- [ ] **T-AUTH-016** Botón/opción "Ingresar con Google" que llama `/main/googleAuthenticate`.
- [ ] **T-AUTH-017** Migrar envío de `securityToken` en DTOs a header `Authorization` (legacy cleanup).
- [ ] **T-AUTH-018** Llamar a `POST /main/cerrarSesion` en `signout()` (cuando T-AUTH-008 exista).

---

## DOCUMENTS

### Nuevos casos de uso

- [ ] **DOC-NEW-001** 🔴 Endpoint de consulta paginada de documentos estandarizado para la SPA
  (`/document/getDocuments` ya existe — documentar contrato y casos de uso).
- [ ] **DOC-NEW-002** 🟡 Historial de cambios de estado (auditoría de `changeState`).
- [ ] **DOC-NEW-003** 🟡 Subida de archivos con validación de tipo/tamaño en backend
  (`/rest/upload` exists — spec formal).
- [ ] **DOC-NEW-004** 🟢 Plantillas de documento dinámicas (motor `neuron`) — documentar el
  contrato de `obtenerCampos`.

### Tareas de implementación

- [x] **T-DOC-001** `POST /rest/guardarDocumento` (save/update + `non-duplicate`).
- [x] **T-DOC-002** `POST /rest/consultarDocumento` y `POST /document/getDocument`.
- [x] **T-DOC-003** `POST /rest/listarDocumentos` y `POST /document/getDocuments`.
- [x] **T-DOC-004** `POST /rest/changeState` (transición de estado).
- [x] **T-DOC-005** `POST /rest/validateBeforeNew`.
- [x] **T-DOC-006** `POST /rest/upload` y `POST /document/upload` (MultipartFile).
- [x] **T-DOC-007** `GET /template/getTemplates/{profile}` (ADMIN/READER/usuario).
- [x] **T-DOC-008** `POST /rest/obtenerCampos` y `GET /template/getFields`.
- [x] **T-DOC-009** `POST /rest/consultarDatosBase` y `POST /template/getFieldData`.
- [x] **T-DOC-010** `GET /document/getInventory/{id}`.
- [x] **T-DOC-011** `POST /template/getTrace` y `GET /template/getTraceFields/{documentId}/{transaction}`.
- [ ] **T-DOC-012** (DOC-NEW-003) Migrar `/document/getDocument` y `/document/saveDocument`
  para recibir token en header en vez de body.
- [ ] **T-DOC-013** (DOC-NEW-002) Auditoría/historial de cambios de estado (`changeState`).
- [x] **T-DOC-101** `ApiService` cubre: `listarDocumentos`, `consultarDocumento`,
  `validateBeforeNew`, `guardarDocumento`, `saveByMassive`, `consultarDatosBase`, `ajustarEstado`,
  `uploadFile`, `obtenerCampos`, `relacionesPropiedad`, `validarTipoProcesoCarga`,
  `consultarInventario`, `getMessageInFiledProccess`. (ver `api.service.ts`)
- [x] **T-DOC-102** `template.service.ts` consume `getTemplates`, `getFields`.
- [ ] **T-DOC-103** Unificar consumo de `/document/getDocument`/`saveDocument` al estándar de
  header `Authorization` (cuando T-DOC-012 exista).
- [ ] **T-DOC-104** Pantalla/estado de trazabilidad usando `getTrace`/`getTraceFields`.
- [ ] **T-DOC-015** Indicador de idempotencia: enviar `non-duplicate` en guardar (ya lo hace
  `guardarDocumento` en `api.service.ts`; verificar en todas las rutas de guardado).

---

## TASKS

### Nuevos casos de uso

- [ ] **TASK-NEW-001** 🟡 Crear/asignar tareas desde la SPA (`TaskRest` existe — spec de casos de uso).
- [ ] **TASK-NEW-002** 🟢 Notificaciones de vencimiento de tarea.

### Tareas de implementación

- [x] **T-TASK-001** `GET /task/` → `TaskGetByUserService`.
- [x] **T-TASK-002** `GET /task/{id}?id=` → `TaskService.getById`.
- [x] **T-TASK-003** `POST /task/create` → `TaskCreateService`.
- [x] **T-TASK-004** `POST /task/update` → `TaskUpdateService`.
- [x] **T-TASK-005** `POST /task/delete/{id}` → `TaskDeleteService`.
- [ ] **T-TASK-006** (TASK-NEW-001) Migrar `GET /task/{id}` a `@PathVariable` id.
- [ ] **T-TASK-007** (TASK-NEW-002) Notificaciones de vencimiento de tarea.
- [x] **T-TASK-101** `TasksService.getTasks()` → `GET /task/`.
- [x] **T-TASK-102** `TasksService.getTaskById()` → `GET /task/{id}`.
- [x] **T-TASK-103** `TasksService.createTask()` → `POST /task/create`.
- [x] **T-TASK-104** `TasksService.updateTask()` → `POST /task/update`.
- [x] **T-TASK-105** `TasksService.deleteTask()` → `POST /task/delete/{id}`.
- [ ] **T-TASK-106** (backlog) Pantalla de edición de tarea con `dueDate`/`priority` (hoy
  `createTask` solo envía `title`; ampliar el formulario).

---

## ACCOUNTING

### Nuevos casos de uso

- [ ] **ACC-NEW-001** 🟡 Preparar/emitir comprobantes (`VoucherController`, `d3.accounting_voucher`) — casos de uso y contrato.
- [ ] **ACC-NEW-002** 🟢 Plan contable y consulta de cuentas (`PlanAccountingController`, `d3.accounting_plan`).

### Tareas de implementación

- [ ] AC-ACC-1: `d3.accounting_voucher` (ya realizado); verificar `VoucherController`.
- [ ] AC-ACC-2: `d3.accounting_plan` (ya realizado); verificar `PlanAccountingController`.
- [ ] AC-ACC-3: `d3.accounting_api` (ya realizado); verificar `AccountApiController`.
- [ ] AC-ACC-4: Documentar DTOs (`VoucherDTO`, `AccountDTO`) en `openapi.yaml`.
- [ ] AC-ACC-5: Validar cobertura en Angular (contabilidad aún sin servicio front dedicado).
- [ ] AC-ACC-6: Agregar pruebas de integración para rangos y generación por documento.

---

## MASSIVE

### Nuevos casos de uso

- [ ] **MAS-NEW-001** 🟡 Carga masiva de documentos con validación y reporte de errores
  (`MassiveRest` — spec).
- [ ] **MAS-NEW-002** 🔴 Ajustar carga masiva en el back y crear la interfaz en el front.
  Falta documentarlo y crear los casos de uso primero (ver `domains/massive/`).

### Tareas de implementación

- [x] AC-MAS-1: `d3.massiveload` realizado; expositor HTTP único `MassiveController` (`massiveload`).
- [x] AC-MAS-2: Centralizado en `MassiveController` (no hay `MassiveItemController`/`MassiveMasterController`).
- [ ] AC-MAS-3: Leer DTOs de carga y documentar en `openapi.yaml`.
- [x] AC-MAS-4: Firma real aclarada — `upload(MultipartFile, template)` → `validate` → `execute`.
- [ ] AC-MAS-5B: Backend — endpoint de descarga de plantilla base (CU-MAS-005).
- [ ] AC-MAS-5F: Frontend — botón "Descargar plantilla" que consume el endpoint.
- [x] AC-MAS-6B: Backend — `MassiveController.upload` registra la carga (CU-MAS-006).
- [ ] AC-MAS-6F: Frontend — componente de selección/subida de archivo.
- [x] AC-MAS-7B: Backend — `MassiveController.validate`/`execute` procesan la carga (CU-MAS-007).
- [ ] AC-MAS-7F: Frontend — acción "Procesar" que invoca los endpoints por `loadId`.
- [x] AC-MAS-8B: Backend — listado de historial (`MassiveController.getLoad`/`getItems`) (CU-MAS-008).
- [ ] AC-MAS-8F: Frontend — vista/tabla de historial de cargas.

---

## NOTIFICATIONS

### Nuevos casos de uso

- [ ] **NOT-NEW-001** 🟡 Centro de notificaciones en SPA (`notification-center.service.ts`
  existe — definir contrato de suscripción/lectura).

### Tareas de implementación

- [ ] AC-NOT-1: Crear `d3.notification` y migrar `NotificationController`.
- [ ] AC-NOT-2: Documentar `ActividadDTO` en `openapi.yaml`.
- [ ] AC-NOT-3: Verificar cobertura en Angular (bandeja de notificaciones).
- [ ] AC-NOT-4: `UsuarioDTO` ya en `d3.logisticpymes` (verificar).

---

## CONFIG-FORMS

### Nuevos casos de uso

- [ ] **CFG-NEW-001** 🟢 Gestión de organizaciones, procesos y plantillas (varios `*Service`
  en `configuration-forms`) — spec de casos de uso por subdominio.
- [ ] **CFG-NEW-002** 🔴 Terminar la migración de la configuración del sistema "flex" a Angular
  (pasar todo a Angular).

### Tareas de implementación

- [ ] AC-CFG-1: `d3.property`, `d3.configuration_file`, `d3.process_form` (ya realizado); verificar controladores.
- [ ] AC-CFG-2: Documentar `PropiedadDTO`/`FileVO` en `openapi.yaml`.
- [ ] AC-CFG-3: Leer módulo `process_form` y documentar formularios dinámicos.
- [ ] AC-CFG-4: Verificar cobertura en Angular (configuración/propiedades).

---

## PERSONS

### Tareas de implementación

- [ ] AC-PER-1: `d3.logisticpymes` (ya realizado); verificar `UserController`.
- [ ] AC-PER-2: Documentar `UsuarioDTO` en `openapi.yaml`.
- [ ] AC-PER-3: Mapear `PropertyGetWithCacheService` a `d3.property`.
- [ ] AC-PER-4: Verificar servicio de usuarios en Angular.

---

## AUTHORIZATION

### Tareas de implementación

- [ ] AC-AUT-1: `d3.authorization` y `d3.authentication` (ya realizado); verificar servicios.
- [ ] AC-AUT-2: Documentar `RolAccesoDTO` en `openapi.yaml`.
- [ ] AC-AUT-3: Verificar cobertura de roles en Angular (guardas de ruta).
- [ ] AC-AUT-4: Unificar 2FA con flujo de login (`authentication`).

---

## DOCUMENT-TRANSITION

### Tareas de implementación

- [ ] AC-DT-1: `d3.document_transition`/`d3.document_execution` (ya realizado); verificar controladores.
- [ ] AC-DT-2: Documentar `PedidoVentaAjusteDTO` en `openapi.yaml`.
- [ ] AC-DT-3: Verificar transición de estado en Angular (flujo de documentos).
- [ ] AC-DT-4: Consolidar `changeState` y traza en un solo módulo de documentos.

---

## FE

### Tareas de implementación

- [ ] AC-FE-1: Documentar DTOs/respuesta de firma en `openapi.yaml` (§17).
- [ ] AC-FE-2: Especificar contrato del WS DIAN consumido.
- [ ] AC-FE-3: Verificar cobertura en Angular (facturación).

---

## UPLOAD

### Tareas de implementación

- [ ] AC-UP-1: Documentar `CargaArchivoDTO` en `openapi.yaml`.
- [ ] AC-UP-2: Unificar contrato de carga con `documents` (evitar duplicar `/rest/upload`).

---

## WEBSERVICE

### Tareas de implementación

- [ ] AC-WS-1: Documentar endpoint de ejecución en `openapi.yaml`.
- [ ] AC-WS-2: Documentar `WebServiceDTO` / `WebServiceEjecucionDTO`.

---

## PROCESS-DESIGNER

### Tareas de implementación

- [ ] AC-PD-1: Documentar CRUD de procesos en `openapi.yaml`.
- [ ] AC-PD-2: Relacionar con `document-transition` (transiciones de documentos).

---

## MULTITENANCY

### Nuevos casos de uso

- [ ] **INF-NEW-001** 🟡 Probar la creación de tenants (multi-tenancy por catálogo JDBC).

### Tareas de implementación

- [ ] AC-MT-1: Especificar creación de tenant (backlog INF-NEW-001).
- [ ] AC-MT-2: Documentar `TenantDTO` y contrato de administración de tenants.

---

## INVENTORY

### Tareas de implementación

- [ ] AC-INV-1: Definir si el inventario expone endpoints o solo es servicio interno.
- [ ] AC-INV-2: Documentar `ProductoDTO` en `openapi.yaml`.

---

## MONEY

### Tareas de implementación

- [ ] AC-MON-1: Determinar exposición HTTP (¿vía `accounting`?).
- [ ] AC-MON-2: Documentar `CuentaDTO` / `MovimientoDTO` en `openapi.yaml`.

---

## TARIFF

### Tareas de implementación

- [ ] AC-TAR-1: Documentar `TarifaDTO` en `openapi.yaml`.
- [ ] AC-TAR-2: Relacionar con cálculo de "Units" de consumo (backlog CONS-NEW-001).

---

## REPORT

### Tareas de implementación

**Documentación y contratos**
- [ ] AC-REP-1: Documentar contrato del servlet de reportes y `/api/getRequest` (parámetros, respuesta, códigos error).
- [ ] AC-REP-2: Documentar `ReporteEjecucionDTO`, `ReporteBaseDTO`, `ReportDTO` en `openapi.yaml`.
- [ ] AC-REP-3: Documentar propiedades de configuración de reporte (tabla en design.md) en wiki/Confluence.

**Testing**
- [ ] AC-REP-10: Test unitario `ReporteBaseSvc.generarReporte()` con mocks (PDF, XLS, HTML, CSV).
- [ ] AC-REP-11: Test unitario `ReportesUtil.exportarReporte*()` con JRXML simples.
- [ ] AC-REP-12: Test unitario `ReportGenerateFromSql.call()` con SQL parametrizado.
- [ ] AC-REP-13: Test integración `ReporteServlet.doGet()` end-to-end (MockMvc + testcontainers).
- [ ] AC-REP-14: Test `ReporteEjecucionSvc.saveWithHistoric()` con y sin histórico.
- [ ] AC-REP-15: Test validación `REP_PRINT_ONE` (segunda ejecución falla).
- [ ] AC-REP-16: Test reporte público (`publico=true`) sin token.

**Refactoring y mejoras**
- [ ] AC-REP-20: Migrar `ReporteServlet` a `@RestController` + `@GetMapping("/api/reports/generate")` (eliminar servlet legacy).
  - [ ] AC-REP-20a: Crear `ReportRestController` en `d3.report.infrastructure`
  - [ ] AC-REP-20b: Migrar lógica `doGet()` → `@GetMapping("/generate")` con `ResponseEntity<byte[]>`
  - [ ] AC-REP-20c: Añadir `@ExceptionHandler` para `ServerException` → JSON error response
  - [ ] AC-REP-20d: Test integración MockMvc (`GET /api/reports/generate`)
  - [ ] AC-REP-20e: Mantener `ReporteServlet` registrado (compatibilidad) durante transición
- [ ] AC-REP-20f: Frontend: Actualizar `FormReportService.buildReportUrl()` → `/api/reports/generate`
- [ ] AC-REP-20g: Feature flag `environment.useReportRestApi` para rollout gradual
- [ ] AC-REP-20h: Cutover producción + monitoreo 48h
- [ ] AC-REP-20i: Eliminar `ReporteServlet` y flag feature
- [ ] AC-REP-21: Extraer lógica de parámetros (`llenarParametros`, `parametrosPropiedades`) a servicio separado `ReporteParametrosSvc`.
- [ ] AC-REP-22: Reemplazar manipulación JRXML por strings (`replaceReport/Header/Footer`) con API programática JasperReports.
- [ ] AC-REP-23: `GeneradorReportes`: unificar constructores, usar `DataSource` siempre (eliminar connection string parsing).
- [ ] AC-REP-24: `ReportGenerateFromSql`: usar `PreparedStatement` en vez de `Statement` + replace string (SQL injection risk).
- [ ] AC-REP-25: `JasperReportCache`: añadir métricas (hit/miss, tamaño, evicciones) y TTL configurable.

**Observabilidad**
- [ ] AC-REP-30: Log estructurado en `generarReporte()` (reporte, usuario, documento, duración, formato, tamaño).
- [ ] AC-REP-31: Métricas Prometheus: `report_generated_total{format,status}`, `report_generation_duration_seconds`.
- [ ] AC-REP-32: Alerta OOM: integrar con sistema de alertas (no solo mail a admin).

**Seguridad**
- [ ] AC-REP-40: Validar que `CONNECTION_STRING_DB` no exponga credenciales en logs/errores.
- [ ] AC-REP-41: Sanitizar parámetros en `ReportGenerateFromSql` (prevenir SQL injection en `$P{param}` replace).
- [ ] AC-REP-42: Rate limiting en `/ReporteServlet` y `/api/getReport`.

**Performance**
- [ ] AC-REP-50: Profile memory en reportes grandes (streaming `JRVirtualizer` / `JRFileVirtualizer`).
- [ ] AC-REP-51: Paginación en `listarConsulta` historial (actualmente carga todo).
- [ ] AC-REP-52: Cache de `ReporteBaseDTO` listados frecuentes (`listarDisponiblesDocumento`, `listarMenu`).

**Visor de Reportes Multi-Tenant (nuevo)**
- [ ] AC-REP-100: Añadir `tenantId` a `ReporteEjecucionDTO` + persistencia
- [ ] AC-REP-101: Crear `ReportViewerController` con endpoints `/view`, `/content`, `/history`, `/metadata`
- [ ] AC-REP-102: Validación explícita tenant en controller (defensa en profundidad)
- [ ] AC-REP-103: Streaming response para archivos grandes (`StreamingResponseBody`)
- [ ] AC-REP-104: Cache de vistas (`ReportViewCache` con Caffeine)
- [ ] AC-REP-105: Frontend: `TenantInterceptor` (header `X-Tenant-ID` automático)
- [ ] AC-REP-106: Frontend: `ReportViewerService` + `ReportViewerComponent` (PDF.js / HTML)
- [ ] AC-REP-107: Frontend: `ReportHistoryComponent` con paginación + modal visor
- [ ] AC-REP-108: Test integración multi-tenant (2 tenants, aislamiento datos)
- [ ] AC-REP-109: Documentar en OpenAPI (`openapi.yaml`) endpoints visor

**Deuda técnica**
- [ ] AC-REP-90: `ReporteBaseSvc` tiene 11 dependencias inyectadas → considerar facade o dividir responsabilidades.
- [ ] AC-REP-91: `ReportesUtil` static methods → difícil de testear; migrar a servicio con inyección.
- [ ] AC-REP-92: `D3Utils.verificarFechaHora` y `formatDateMassiveFile` en servlet → mover a util compartido.
- [ ] AC-REP-93: `ReporteServlet.downloadFile()`: `Content-Type` hardcoded → usar `MimeTypeUtils` o `FileTypeMap`.
- [ ] AC-REP-94: Excepción genérica `Exception` en `generarReporte()` → tipos específicos (`ReportGenerationException`, `ReportNotFoundException`).

---

## MAIL

### Tareas de implementación

- [ ] AC-MAIL-1: Relacionar con `notifications` (envío de actividades por correo).
- [ ] AC-MAIL-2: Documentar `MensajeDTO` en `openapi.yaml`.

---

## HOMOLOGATE

### Tareas de implementación

- [ ] AC-HOMO-1: Documentar contrato de homologación (¿expuesto vía `configuration`?).
- [ ] AC-HOMO-2: Relacionar con `accounting` y `tariff`.

---

## DOCUMENT-TRANSACTION

### Tareas de implementación

- [ ] AC-DTX-1: Relacionar con `document-transition` (auditoría de cambios de estado).
- [ ] AC-DTX-2: Documentar `TransaccionLogDTO` en `openapi.yaml`.

---

## ASSISTANT (front-only)

### Tareas de implementación

- [x] **T-ASSISTANT-001** `AssistantService` con `mensajes` (signal), `interpretar()`,
  `ejecutar()`, `filtrarTemplates()`, `abrirDocumento()`, `abrirTemplateDirect()`.
- [x] **T-ASSISTANT-002** `AssistantButtonComponent` (botón flotante) + apertura con tecla F9.
- [x] **T-ASSISTANT-003** `AssistantDialogComponent` (chat UI) — **migrado a panel** (ver D1).
- [x] **T-ASSISTANT-004** Búsqueda `@código` → `ApiService.listarDocumentos` →
  `POST /document/getDocuments`.
- [x] **T-ASSISTANT-005** Navegación `/texto` → filtro de `TemplateService.template()` →
  `Router.navigate(['/list/list', id])`.
- [x] **T-ASSISTANT-006** **Migrar `MatDialog` → panel lateral derecho** (ver `design.md` D1):
  - [x] Reemplazar `MatDialog`/`MatDialogRef` por `AssistantPanelComponent` (panel `fixed
    right-0`) anclado a la derecha en `app.component.html` / shell.
  - [x] Renombrar `AssistantService.isOpenDialog()` → `isOpenPanel()` y ajustar triggers
    (`togglePanel()`/`openPanel()`/`closePanel()`).
  - [x] Manejar cierre con **Esc** y F9 (toggle) sin bloquear el resto de la app.
  - [x] Responsive: ancho fijo (`md:w-[400px]`) en desktop, full-width en móvil.
  - [x] Accesibilidad: foco al input al abrir (`ngAfterViewInit`) y retorno de foco al
    cerrar (`AssistantService.triggerElement`).
- [x] **T-ASSISTANT-007** Actualizar `docs/user-guide/assistant.md` para reflejar el panel
  lateral (estado anterior: modal).

---

## CONSUMPTION-UNITS

### Nuevos casos de uso

- [ ] **CONS-NEW-001** 🟡 Inicialización con 1 MB, incremento diario 1 MB, compra MB/GB, descuento horario por `CargaArchivoDTO.size` — spec completado en `domains/consumption-units/`.

### Tareas de implementación

- [ ] AC-CU-1: Crear DTOs `ConsumptionUnitBalanceDTO`, `ConsumptionUnitMovementDTO` y filtros.
- [ ] AC-CU-2: Crear mappers MyBatis + XML para tablas `consumption_unit_balance_cucb` y `consumption_unit_movement_cucm`.
- [ ] AC-CU-3: Implementar `ConsumptionUnitBalanceSvc` (consulta, actualización de saldo).
- [ ] AC-CU-4: Implementar `ConsumptionUnitMovementSvc` (registro de movimientos).
- [ ] AC-CU-5: Implementar `ConsumptionUnitSchedulerSvc` con `@Scheduled`:
  - Incremento diario (cron: `0 0 0 * * *`)
  - Descuento horario (cron: `0 0 * * * *`)
- [ ] AC-CU-6: Integrar descuento horario consultando `CargaArchivoMapper` por `fechaInicio`/`fechaFin` de la hora.
- [ ] AC-CU-7: Endpoint REST para compra de unidades (MB/GB) y consulta de saldo/movimientos.
- [ ] AC-CU-8: Documentar DTOs y endpoints en `openapi.yaml`.
- [ ] AC-CU-9: Tests de integración para inicialización, incremento, compra y descuento.
- [ ] AC-CU-10: Actualizar `specs/DOMINIOS.md` agregando el dominio `consumption-units`.
- [ ] AC-CU-11: Implementar `ConsumptionUnitDelayInterceptor` (HandlerInterceptor) con lógica de delay por saldo negativo.
- [ ] AC-CU-12: Agregar método `getCurrentBalanceBytes()` con caché por request en `ConsumptionUnitBalanceSvc`.
- [ ] AC-CU-13: Configurar interceptor en `WebMvcConfigurer` con `excludePathPatterns` para healthchecks, auth, endpoints propios.
- [ ] AC-CU-14: Propiedad configurable `consumption-units.max-delay-seconds` (default 60) y `consumption-units.enabled`.
- [ ] AC-CU-15: Header de respuesta `X-Consumption-Delay-Seconds` cuando hay delay.
- [ ] AC-CU-16: Tests de integración: saldo positivo (sin delay), saldo -1MB (1s), saldo -10MB (10s), saldo -100MB (max 60s), exclusiones funcionan.
- [ ] AC-CU-17: Métricas Micrometer: contador `consumption_units.delay.requests_total`, histograma `consumption_units.delay.duration_seconds`.

---

## Notas

- Antes de implementar cualquiera, revisar `contract.md` §16 (pendientes de contrato)
  y completar los esquemas OpenAPI faltantes (`UsuarioAutenticacionDTO`, `OrganizacionDTO`,
  `FieldResponse`, etc.).
- Todos los dominios documentados ya tienen carpeta en `specs/domains/`. Los nuevos ítems de
  este backlog generan/extienden sus `use-cases/requirements/design/tasks` al activarse.
- Verificar compilaración antes de marcar como hecho:
  - Back: `./gradlew.bat build -x test`
  - Front: `npm run build` y `npx tsc -p tsconfig.app.json --noEmit`
