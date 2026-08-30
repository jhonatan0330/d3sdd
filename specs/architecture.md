# Arquitectura — Estándares globales del ecosistema D3

Este archivo define los **estándares transversales** que aplican a todos los dominios
del ecosistema D3 (back `d3brain` + front `d3_front`). Toda decisión de implementación
debe respetar estos lineamientos.

## Tabla de contenido

1. [Estructura de paquetes (backend)](#1-estructura-de-paquetes-backend)
2. [Estructura de carpetas (frontend)](#2-estrutura-de-carpetas-frontend)
3. [Carpeta shared (ambos proyectos)](#3-carpeta-shared-ambos-proyectos)
4. [Estructura de endpoints REST](#4-estructura-de-endpoints-rest)
5. [Formato de DTOs](#5-formato-de-dtos)
6. [Multi-tenancy](#6-multi-tenancy)
7. [Manejo de errores](#7-manejo-de-errores)
8. [Base de datos (MyBatis)](#8-base-de-datos-mybatis)
9. [Contrato API](#9-contrato-api)
10. [Seguridad](#10-seguridad)
11. [Sincronización frontend ↔ contract](#11-sincronización-frontend--contract)
12. [Tareas de arquitectura pendientes](#12-tareas-de-arquitectura-pendientes)

---

## 1. Estructura de paquetes (backend)

**Decisión de arquitectura:** Cada dominio del negocio se mapea a un paquete Java con la
misma raíz `d3.<dominio>`. Dentro de cada paquete existen 3 sub-paquetes con responsabilidades
claras (ver [ARCH-001](backlog-strategies/ARCH-001-package-rename.md)):

```
d3.<dominio>/
  domain/           # DTOs, filtros, entidades (@Alias MyBatis), Request/Response
  application/      # Lógica de negocio (servicios)
  infrastructure/   # Controllers, Mappers MyBatis, configuración, conexiones a BD/servicios externos
```

### Responsabilidades por paquete

| Paquete | Contenido | Ejemplo |
|---------|-----------|---------|
| `domain` | DTOs de respuesta, DTOs de filtro, Request, Response, entidades MyBatis (`@Alias`) | `UsuarioDTO`, `TaskRequest`, `RolAccesoDTO` |
| `application` | Servicios de negocio, lógica de dominio, orquestación | `DocumentService`, `TaskService` |
| `infrastructure` | Controllers REST, Mappers MyBatis, configuración, clientes de servicios externos | `DocumentController`, `DocumentMapper` |

### Paquete shared (transversal)

Además, ambos proyectos (back y front) comparten una carpeta `shared/` para objetos
transversales (ver [sección 3](#3-carpeta-shared-ambos-proyectos)).

| Dominio | Paquete(s) |
|---------|------------|
| authentication | `d3.authentication` |
| document | `d3.document_execution`, `d3.document_transition`, `d3.document_transaction` |
| tasks | `d3.task` |
| accounting | `d3.accounting_voucher`, `d3.accounting_plan`, `d3.accounting_api` |
| massiveload | `d3.massiveload` |
| notification | `d3.notification` |
| configuration | `d3.property`, `d3.configuration_file`, `d3.process_form`, `d3.homologate` |
| users | `d3.logisticpymes` |
| authorization | `d3.authorization` |
| fe | `d3.fe` |
| upload | `d3.upload` |
| webservice | `d3.webservice` |
| process | `d3.process_designer` |
| multitenancy | `d3.multitenancy` |
| inventory | `d3.inventory` |
| money | `d3.money` |
| tariff | `d3.tariff` |
| report | `d3.report` |
| mail | `d3.mail` |
| shared | `d3.shared` |
| consumption-units | `d3.consumption_units` |

**Regla:** No crear paquetes fuera de esta estructura. Si un módulo requiere subdominios,
usar `d3.<dominio>.<subdominio>`.

---

## 2. Estructura de carpetas (frontend)

**Decisión de arquitectura:** En el proyecto Angular (`d3_front`), dentro de `src/app/`
se crean carpetas con los mismos nombres de dominio del backend. En la raíz de cada
dominio **solo** van los archivos de types y el service del contrato. Los componentes
viven organizados en subcarpetas.

### Regla de organización por dominio

```
src/app/
  <dominio>/
    <dominio>.types.ts    ← Interfaces/DTOs del contract (solo en raíz)
    <dominio>.service.ts  ← Service que consume la API (solo en raíz)
    componente-a/         ← Componente Angular (carpeta dedicada)
      componente-a.component.ts
      componente-a.component.html
    componente-b/
      componente-b.component.ts
      componente-b.component.html
  layout/                 # Interfaz gráfica general (header, sidebar, footer)
  shared/                 # Objetos transversales (ver sección 3)
```

### Estructura detallada

```
src/app/
  <dominio>/
    <dominio>.types.ts              # Interfaces del contract (TaskDTO, TaskRequest, etc.)
    <dominio>.service.ts            # Service con métodos HTTP (paths del contract)
    <componente-nombre>/            # Cada componente en su propia carpeta
      <componente-nombre>.component.ts
      <componente-nombre>.component.html
      <componente-nombre>.component.scss
    <sub-modulo>/                   # Sub-agrupaciones si existen
      <componente-nombre>/
        <componente-nombre>.component.ts
        <componente-nombre>.component.html
  layout/
    layout.component.ts
    dashboard/
    simple-nav/
    user/
    navigation/
    shortcuts/
    change-picture/
  shared/
    components/
    services/
    models/
    guards/
    interceptors/
    utils/
```

### Convenciones

| Elemento | Convención | Ejemplo |
|----------|------------|---------|
| Types (raíz) | `<dominio>.types.ts` | `task.types.ts`, `document.types.ts` |
| Service (raíz) | `<dominio>.service.ts` | `task.service.ts`, `notification.service.ts` |
| Componentes | `<nombre>/` como carpeta propia | `task-list/task-list.component.ts` |
| Sub-componentes | Agrupación lógica en sub-carpetas | `form/controls/fecha/` |

### Reglas

- **Raíz del dominio:** Solo `*.types.ts` y `*.service.ts`. No colocar componentes sueltos en la raíz.
- **Componentes:** Cada componente Angular vive en su propia carpeta con su nombre.
- **Services:** Siempre en la raíz del dominio, no en subcarpetas.
- **Types:** Siempre en la raíz del dominio, no en subcarpetas.
- **Archivos auxiliares** (helpers, utils específicos del dominio): van en la raíz del dominio junto al service.

### Ejemplo real: dominio `task`

```
src/app/task/
  task.types.ts              # TaskDTO, TaskFilterDTO, TaskRequest (interfaces del contract)
  task.service.ts            # TaskService: getTasks(), createTask(), updateTask(), deleteTask()
  task-list/                 # Componente de lista
    task-list.component.ts
    task-list.component.html
    task-list.component.scss
  task-form/                 # Componente de formulario
    task-form.component.ts
    task-form.component.html
    task-form.component.scss
```

### Ejemplo real: dominio `document`

```
src/app/document/
  document.types.ts          # PedidoVentaDTO, PedidoVentaFilterDTO, etc.
  document.service.ts        # DocumentService: getDocuments(), saveDocument(), etc.
  form/                      # Motor de formularios dinámicos
    form.component.ts
    form.component.html
    controls/                # Controles del formularios dinámicos
      archivo/
      base/
      fecha/
      gps/
      ...
  cruds/                     # Componente CRUD list
    cruds2.component.ts
  trazability/               # Trazabilidad
    trazability.component.ts
```

### Responsabilidades por carpeta

| Carpeta | Contenido |
|---------|-----------|
| `<dominio>/` (raíz) | `*.types.ts` (interfaces del contract) + `*.service.ts` (service HTTP) |
| `<dominio>/<componente>/` | Componentes Angular organizados por nombre |
| `layout/` | Componentes de interfaz: header, sidebar, footer, navegación principal |
| `shared/` | Utilidades, componentes reutilizables, interceptors, guards, modelos comunes |

---

## 3. Carpeta shared (ambos proyectos)

**Decisión de arquitectura:** Tanto el backend (`d3brain`) como el frontend (`d3_front`)
cuentan con una carpeta `shared/` para objetos transversales que se usan en múltiples
dominios.

### Backend (`d3brain`)

```
d3.shared/
  domain/        # DTOs compartidos, excepciones, constantes
  application/   # Servicios utilitarios (fechas, validaciones, logging)
  infrastructure/ # Configuración base, utilidades de BD
```

### Frontend (`d3_front`)

```
src/app/shared/
  components/    # Componentes reutilizables (botones, modales, tablas)
  services/      # Servicios comunes (auth, notifications, storage)
  models/        # Modelos compartidos (pagination, response, error)
  guards/        # Guards de rutas (auth, roles)
  interceptors/  # Interceptors HTTP (token, error handling)
  utils/         # Utilidades (fechas, validaciones, formateo)
```

### Reglas de uso

- **No** crear dependencias circulares entre dominios vía `shared`
- `shared` **no** puede depende de ningún dominio específico
- Los dominios **pueden** importar de `shared`
- Si un objeto se usa en ≤2 dominios, va en `shared`; si es solo 1, va en ese dominio

---

## 4. Estructura de endpoints REST

**Decisión de arquitectura (ARCH-002):** Todos los endpoints del ecosistema D3 deben
seguir estas convenciones para garantizar consistencia y predecibilidad.

### Convenciones de rutas

| Elemento | Convención | Ejemplo |
|----------|------------|---------|
| Recurso principal | `/<dominio>` | `/task`, `/user`, `/acc/voucher` |
| Sub-recursos | `/<recurso>/{id}` | `/task/{id}` |
| Acciones especiales | `/<recurso>/<accion>` | `/task/create`, `/document/changeState` |
| API externa | `/api/<dominio>/<recurso>` | `/api/config/consecutives/list` |

**Reglas de rutas:**
- Usar **kebab-case** para rutas compuestas: `/web-services`, `/change-state`
- Usar **camelCase** para compatibilidad legacy: `/changeState`, `/guardarDocumento`
- Los dominios nuevos usan kebab-case; legacy se mantiene por compatibilidad
- Nunca terminar con `/` (barra final)

### Métodos HTTP

| Operación | Método | Ruta | Body | Respuesta |
|-----------|--------|------|------|-----------|
| Listar (filtros simples) | `GET` | `/<recurso>` | No | `DTO[]` |
| Listar (filtros complejos) | `POST` | `/<recurso>/list` | `FilterDTO` | `DTO[]` |
| Obtener por ID | `GET` | `/<recurso>/{id}` | No | `DTO` |
| Crear | `POST` | `/<recurso>/create` | `Request` | `SharedIdResponse` |
| Actualizar | `POST` | `/<recurso>/update` | `Request` | `SharedIdResponse` |
| Eliminar | `POST` | `/<recurso>/delete/{id}` | No | `SharedIdResponse` |
| Operación especial | `POST` | `/<recurso>/<accion>` | `Request` | `DTO` o `SharedIdResponse` |

> **Nota legacy:** El proyecto usa `POST` para operaciones de lectura con filtros complejos
> por compatibilidad con el frontend actual. Nuevos endpoints prefieren `GET` cuando sea posible.

### Autenticación

Todos los endpoints requieren header `Authorization: Bearer <token>` excepto los
explícitamente marcados como públicos en `contract.md`.

**Excepciones conocidas (legacy):**
- `/main/autenticarUsuarioAutenticacion` (login)
- `/main/checkToken` (validación de token)
- `/main/solicitarNuevaClave` (recuperación)

### Paginación

**Request (FilterDTO):**
```json
{
  "startRow": 0,
  "endRow": 200
}
```

**Response:**
```json
{
  "data": [...],
  "total": 1234
}
```

**Regla:** `endRow - startRow` no debe exceder 200 registros por defecto.

### Cross-Origin (CORS)

```java
@CrossOrigin(origins = "*", allowedHeaders = "*")
```

Aplicar en todos los controllers. Configuración centralizada en `WebMvcConfigurer`
es preferida sobre `@CrossOrigin` por controller.

### Convención de respuestas

| Tipo | HTTP Status | Body |
|------|-------------|------|
| Éxito (query) | 200 | `DTO` o `DTO[]` |
| Éxito (create/update/delete) | 200 | `SharedIdResponse` |
| Error de cliente | 400/401/403/404 | `SharedApiErrorResponse` |
| Error de servidor | 500 | `SharedApiErrorResponse` |

---

## 5. Formato de DTOs

**Decisión de arquitectura (ARCH-003):** Todos los DTOs del ecosistema D3 deben seguir
estas convenciones para garantizar consistencia entre dominios y entre backend/frontend.

### Nomenclatura de tipos

| Tipo | Sufijo | Ejemplo | Uso |
|------|--------|---------|-----|
| DTO de respuesta | `DTO` | `UsuarioDTO`, `PedidoVentaDTO` | Respuestas GET/POST |
| DTO de filtro | `FilterDTO` | `UsuarioFilterDTO`, `PedidoVentaFilterDTO` | Filtros de listado |
| DTO de request | `Request` | `TaskRequest`, `VoucherPrepareRequest` | Cuerpo de creación/actualización |
| Respuesta con ID | `SharedIdResponse` | — | Respuesta de create/update/delete |
| Respuesta con error | `SharedApiErrorResponse` | — | Respuestas de error |
| DTO compartido | `Shared*` | `SharedDataObject`, `SharedDataObjectFilter` | Bases genéricas |

### Convenciones de campos

#### Campos de identidad

| Campo | Tipo | Descripción | Regla |
|-------|------|-------------|-------|
| `llaveTabla` | `String` | ID primario (dominios legacy) | Siempre String, nunca Number |
| `key` | `String` | ID primario (dominios nuevos) | Preferido para nuevos dominios |
| `estado` | `String` | Estado del registro: `A` = activo, `I` = inactivo | Dominios legacy |
| `state` | `String` | Estado del registro: `A` = activo, `I` = inactivo | Dominios nuevos |

> **Regla de migración:** Los dominios nuevos usan `key`/`state`. Los dominios legacy
> mantienen `llaveTabla`/`estado` hasta migración planificada. No mezclar en el mismo DTO.

#### Campos de paginación (FilterDTO)

| Campo | Tipo | Descripción | Regla |
|-------|------|-------------|-------|
| `startRow` | `Integer` | Offset de paginación (0-based) | Dominios nuevos |
| `endRow` | `Integer` | Limite de registros | Dominios nuevos |
| `paginacionRegistroInicial` | `Integer` | Offset de paginación | Dominios legacy |
| `paginacionRegistroFinal` | `Integer` | Limite de registros | Dominios legacy |
| `filter` | `String` | Búsqueda de texto libre | Dominios nuevos |
| `filtroParametro` | `String` | Búsqueda de texto libre | Dominios legacy |

#### Campos de tenant

| Campo | Tipo | Descripción | Regla |
|-------|------|-------------|-------|
| `securityToken` | `String` | Token de sesión (legacy, en body) | **Deprecated** — usar header `Authorization` |

> **Regla:** El tenant **NUNCA** viaja en DTOs. Se resuelve desde sesión (ver sección 6).

### Anotaciones obligatorios

Todo DTO debe incluir:

```java
@JsonInclude(JsonInclude.Include.NON_NULL)   // Excluir nulls en serialización
@Alias("NombreDTO")                           // Alias para MyBatis
public class NombreDTO {
    // ...
}
```

### Fechas en DTOs

```java
@JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "yyyy-MM-dd@HH:mm:ss.SSSZ",
            timezone = "America/Bogota")
private String fechaCreacion;
```

Ver sección 13 para convenciones completas de fechas.

### Ejemplo de DTO completo

```java
package d3.task.domain;

import com.fasterxml.jackson.annotation.JsonInclude;
import org.apache.ibatis.typealias.Alias;

@JsonInclude(JsonInclude.Include.NON_NULL)
@Alias("TaskDTO")
public class TaskDTO {
    private String key;           // ID primario (nuevo dominio)
    private String title;
    private String description;
    private String state;         // A/I
    private String dueDate;
    private Integer priority;
    // ... getters/setters
}
```

```java
@JsonInclude(JsonInclude.Include.NON_NULL)
@Alias("TaskFilterDTO")
public class TaskFilterDTO {
    private Integer startRow;
    private Integer endRow;
    private String filter;
    private String state;
    private String dueDateMin;
    private String dueDateMax;
    // ... getters/setters
}
```

---

## 6. Multi-tenancy

**Regla fundamental:** El tenant **NUNCA** viaja en el request. Se resuelve desde:
1. Token de sesión (`JWT` claims o `securityToken` opaco)
2. API key (`x-api-key`)

### Implementación

- `TenantContext`: almacena el tenant actual (ThreadLocal)
- `TenantRoutingDataSource`: enrutamiento a catálogo JDBC por tenant
- Los mappers MyBatis operan dentro del catálogo del tenant activo

### En endpoints

```java
// NO hacer esto:
@RequestParam String tenant

// Hacer esto (implícito desde sesión):
String tenant = TenantContext.getCurrentTenant();
```

---

## 7. Manejo de errores

**Decisión de arquitectura (ARCH-005):** Todas las respuestas de error del ecosistema D3
deben seguir un formato estándar con códigos predefinidos y mapeo correcto a HTTP status.

### Formato de respuesta de error

```json
{
  "status": 400,
  "error_code": "VALIDATION_ERROR",
  "message": "Mensaje descriptivo para el usuario",
  "detail": "Detalle técnico opcional (solo en debug)"
}
```

### Estructura SharedApiErrorResponse

```java
public class SharedApiErrorResponse {
    private int status;           // HTTP status code
    private String error_code;    // Código de error estándar
    private String message;       // Mensaje para el usuario
    private String detail;        // Detalle técnico (opcional)

    // Builder pattern
    public static SharedApiErrorResponse builder() { ... }
}
```

### Códigos de error comunes

| Código | HTTP Status | Descripción | Uso |
|--------|-------------|-------------|-----|
| `UNAUTHORIZED` | 401 | Token inválido o expirado | Sesión no válida |
| `FORBIDDEN` | 403 | Sin permisos para la operación | Autorización insuficiente |
| `NOT_FOUND` | 404 | Recurso no encontrado | ID no existe |
| `VALIDATION_ERROR` | 400 | Error de validación de datos | Campos requeridos, formatos |
| `DUPLICATE_ERROR` | 409 | Conflicto - recurso ya existe | Duplicados |
| `INTERNAL_ERROR` | 500 | Error interno del servidor | Error no controlado |

### Excepciones personalizadas

Crear excepciones específicas por tipo de error (no usar `ServerException` genérico):

```java
// Excepción base
public class D3Exception extends RuntimeException {
    private final String errorCode;
    private final int httpStatus;

    public D3Exception(String errorCode, int httpStatus, String message) {
        super(message);
        this.errorCode = errorCode;
        this.httpStatus = httpStatus;
    }
}

// Excepciones específicas
public class NotFoundException extends D3Exception {
    public NotFoundException(String message) {
        super("NOT_FOUND", 404, message);
    }
}

public class ValidationException extends D3Exception {
    public ValidationException(String message) {
        super("VALIDATION_ERROR", 400, message);
    }
}

public class UnauthorizedException extends D3Exception {
    public UnauthorizedException(String message) {
        super("UNAUTHORIZED", 401, message);
    }
}

public class ForbiddenException extends D3Exception {
    public ForbiddenException(String message) {
        super("FORBIDDEN", 403, message);
    }
}

public class DuplicateException extends D3Exception {
    public DuplicateException(String message) {
        super("DUPLICATE_ERROR", 409, message);
    }
}
```

### Manejador global de excepciones

```java
@ControllerAdvice
public class APIControllerErrorAdvice {

    @ExceptionHandler(NotFoundException.class)
    public ResponseEntity<SharedApiErrorResponse> handleNotFound(NotFoundException e) {
        return ResponseEntity.status(404).body(
            SharedApiErrorResponse.builder()
                .withStatus(404)
                .withError_code("NOT_FOUND")
                .withMessage(e.getMessage())
                .build()
        );
    }

    @ExceptionHandler(ValidationException.class)
    public ResponseEntity<SharedApiErrorResponse> handleValidation(ValidationException e) {
        return ResponseEntity.status(400).body(
            SharedApiErrorResponse.builder()
                .withStatus(400)
                .withError_code("VALIDATION_ERROR")
                .withMessage(e.getMessage())
                .build()
        );
    }

    @ExceptionHandler(UnauthorizedException.class)
    public ResponseEntity<SharedApiErrorResponse> handleUnauthorized(UnauthorizedException e) {
        return ResponseEntity.status(401).body(
            SharedApiErrorResponse.builder()
                .withStatus(401)
                .withError_code("UNAUTHORIZED")
                .withMessage(e.getMessage())
                .build()
        );
    }

    @ExceptionHandler(ForbiddenException.class)
    public ResponseEntity<SharedApiErrorResponse> handleForbidden(ForbiddenException e) {
        return ResponseEntity.status(403).body(
            SharedApiErrorResponse.builder()
                .withStatus(403)
                .withError_code("FORBIDDEN")
                .withMessage(e.getMessage())
                .build()
        );
    }

    @ExceptionHandler(DuplicateException.class)
    public ResponseEntity<SharedApiErrorResponse> handleDuplicate(DuplicateException e) {
        return ResponseEntity.status(409).body(
            SharedApiErrorResponse.builder()
                .withStatus(409)
                .withError_code("DUPLICATE_ERROR")
                .withMessage(e.getMessage())
                .build()
        );
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<SharedApiErrorResponse> handleGeneric(Exception e) {
        return ResponseEntity.status(500).body(
            SharedApiErrorResponse.builder()
                .withStatus(500)
                .withError_code("INTERNAL_ERROR")
                .withMessage("Error interno del servidor")
                .withDetail(D3Utils.maskError(e.getMessage()))
                .build()
        );
    }
}
```

### Uso en servicios

```java
// ❌ No hacer esto (ServerException genérico)
throw new ServerException("El documento no existe");

// ✅ Hacer esto (excepción específica)
throw new NotFoundException("Documento con ID " + id + " no encontrado");

// ✅ Validación
throw new ValidationException("El campo 'nombre' es requerido");

// ✅ Autorización
throw new ForbiddenException("No tiene permisos para eliminar este documento");
```

### Migración de ServerException

Los dominios legacy usan `ServerException` que retorna HTTP 500 siempre. Migrar
progresivamente a excepciones específicas:

1. Identificar usos de `ServerException` en cada dominio
2. Clasificar: ¿es 404? ¿400? ¿403? ¿500?
3. Reemplazar por la excepción correspondiente
4. Verificar que el manejador global la captura correctamente

### Reglas

| Regla | Descripción |
|-------|-------------|
| R1 | Usar excepciones específicas, nunca `Exception` genérica |
| R2 | El `message` debe ser comprensible para el usuario |
| R3 | El `detail` solo se incluye en modo debug (no producción) |
| R4 | Los errores 500 se loguean con nivel SEVERE |
| R5 | Los errores 4xx se loguean con nivel WARN |
| R6 | No exponer stack traces en respuestas HTTP |

---

## 8. Base de datos (MyBatis)

**Decisión de arquitectura (ARCH-006):** Todas las capas de persistencia del ecosistema D3
deben seguir estas convenciones de nombres, estructura de XML y patrones de mapeo.

### Convenciones de tablas

| Elemento | Convención | Ejemplo |
|----------|------------|---------|
| Tabla | `<prefijo>_<nombre>` | `usuario_usrp`, `task_tsk`, `consumption_unit_balance_cucb` |
| PK | `<prefijo>_llave` | `usrp_llave`, `ctsk_llave`, `ccuc_llave` |
| FK | `<prefijo>_<tabla_ref>_llave` | `ccum_balance_llave` |
| Tenant | `<prefijo>_tenant` | `ccuc_tenant`, `cten_llave` |
| Estado | `<prefijo>_estado` | `usrp_estado`, `tsk_estado` |
| Fecha | `<prefijo>_fecha` | `pdvp_fecha`, `tsk_created` |

### Convenciones de columnas

Prefijo de tipo + nombre descriptivo:

| Prefijo | Tipo | Ejemplo |
|---------|------|---------|
| `c` | Char/Varchar | `cusr_nombre`, `cpdv_llave` |
| `d` | Date/Timestamp | `dpdv_fecha`, `dtsk_completed` |
| `n` | Numeric/Integer | `npdv_historico`, `ntsk_priority` |
| `m` | Money/Decimal | `mpdv_consecutivo`, `mcmp_valor` |
| `b` | Boolean | `bcue_validarturno`, `bptr_documentador` |

**Mapeo a DTO:** El XML de MyBatis usa alias camelCase:
```sql
SELECT
  cusr_llave AS llaveTabla,
  cusr_nombre AS nombre,
  cusr_estado AS estado
FROM usuario_usrp
```

### Convenciones de Mappers

**Nuevos dominios (inglés):**

| Método | XML ID | Uso |
|--------|--------|-----|
| `getOne` | `getOne` | Consulta por PK |
| `getMany` | `getMany` | Listado con filtros |
| `count` | `count` | Conteo total |
| `insert` | `insert` | Inserción |
| `update` | `update` | Actualización |

**Dominios legacy (español) — mantener por compatibilidad:**

| Método | XML ID | Uso |
|--------|--------|-----|
| `consultar` | `consultar` | Consulta por PK |
| `listar` | `listar` | Listado con filtros |
| `cantidadRegistros` | `cantidadRegistros` | Conteo total |
| `insertar` | `insertar` | Inserción |
| `actualizar` | `actualizar` | Actualización |

**Regla:** Los dominios nuevos usan inglés. Los dominios legacy mantienen español.

### Anotación de Mapper

```java
@D3SqlConnMapper(value = "NombreMapper")
public interface NombreMapper extends SharedCRUDMapperMybatis<NombreDTO, NombreFilterDTO> {
    // Métodos custom si existen
}
```

Ubicación: `d3.<dominio>.infrastructure`

### Estructura XML estándar

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="d3.dominio.infrastructure.NombreMapper">

  <!-- Fragmento SELECT -->
  <sql id="selectNombreFragment">
    SELECT
      cxxt_llave AS key,
      cxxt_nombre AS nombre,
      cxxt_estado AS state
    FROM nombre_xxxp
  </sql>

  <!-- Fragmento WHERE -->
  <sql id="whereNombreFragment">
    WHERE 1=1
    <if test="state != null and state != ''">
      AND cxxt_estado = #{state}
    </if>
    <if test="filter != null and filter != ''">
      AND cxxt_nombre ILIKE '%' || #{filter} || '%'
    </if>
    <if test="fechaMin != null">
      AND cxxt_fecha >= #{fechaMin}
    </if>
    <if test="fechaMax != null">
      AND cxxt_fecha <![CDATA[ < ]]> #{fechaMax}
    </if>
  </sql>

  <!-- Fragmento paginación -->
  <sql id="paginationNombreFragment">
    LIMIT #{endRow - startRow} OFFSET #{startRow}
  </sql>

  <!-- Consulta por PK -->
  <select id="getOne" resultType="NombreDTO">
    <include refid="selectNombreFragment"/>
    WHERE cxxt_llave = #{key}
  </select>

  <!-- Listado con filtros -->
  <select id="getMany" resultType="NombreDTO">
    <include refid="selectNombreFragment"/>
    <include refid="whereNombreFragment"/>
    ORDER BY cxxt_fecha DESC
    <include refid="paginationNombreFragment"/>
  </select>

  <!-- Conteo -->
  <select id="count" resultType="int">
    SELECT COUNT(*) FROM nombre_xxxp
    <include refid="whereNombreFragment"/>
  </select>

  <!-- Inserción -->
  <insert id="insert" useGeneratedKeys="true" keyProperty="key">
    INSERT INTO nombre_xxxp (cxxt_nombre, cxxt_estado, cxxt_tenant)
    VALUES (#{nombre}, #{state}, /* tenant from context */)
  </insert>

  <!-- Actualización -->
  <update id="update">
    UPDATE nombre_xxxp
    SET cxxt_nombre = #{nombre},
        cxxt_estado = #{state}
    WHERE cxxt_llave = #{key}
  </update>

</mapper>
```

### Reglas de paginación

- Usar `LIMIT #{endRow - startRow} OFFSET #{startRow}` (PostgreSQL)
- Default: máximo 200 registros por consulta
- El `count` siempre se ejecuta separado del listado

### Reglas de fechas en XML

- Usar `CDATA[ < ]>` para comparaciones `<` (evita parseo XML)
- Rangos siempre en pares Min/Max: `fechaMin`/`fechaMax`
- Formato de fecha en BD: timestamp sin zona, timezone en aplicación

### Multi-tenancy en MyBatis

```xml
<!-- NO incluir tenant en SQL — se resuelve por TenantContext -->
<!-- El TenantFilter setea el catalog/schema antes de cada query -->
SELECT ... FROM usuario_usrp  <!-- opera en el tenant activo -->
```

### Transacciones

```java
@Transactional(rollbackFor = Exception.class)
public void operacionCritica() {
    // ...
}
```

Aplicar `@Transactional` en servicios de aplicación, no en controllers.

---

## 9. Contrato API

**Decisión de arquitectura (ARCH-007):** Formalizar el proceso de cambios en la API
para garantizar compatibilidad y previsibilidad.

### Fuentes de verdad

1. **Human-readable:** `contract.md`
2. **Máquina-legible:** `openapi.yaml`

### Clasificación de cambios

#### Breaking changes (requieren coordinación)

Un cambio es **breaking** si rompe compatibilidad con consumidores existentes:

| Tipo de cambio | Ejemplo | Impacto |
|----------------|---------|---------|
| Eliminar endpoint | `DELETE /task/{id}` | Consumidores fallan |
| Cambiar path | `/task` → `/tasks` | Consumidores fallan |
| Cambiar método HTTP | `GET` → `POST` | Consumidores fallan |
| Renombrar campo DTO | `nombre` → `name` | Frontend falla |
| Cambiar tipo de campo | `String` → `Number` | Parsing falla |
| Hacer obligatorio un campo opcional | `campo?` → `campo` | Requests fallan |
| Cambiar formato de respuesta | Agregar campo requerido | Parsing falla |

#### Non-breaking changes (compatibles)

Un cambio es **non-breaking** si no afecta consumidores existentes:

| Tipo de cambio | Ejemplo | Impacto |
|----------------|---------|---------|
| Agregar endpoint nuevo | `GET /reports` | Sin impacto |
| Agregar campo opcional a DTO | `campo?: string` | Ignorado por consumidores |
| Agregar query param opcional | `?lang=es` | Ignorado por consumidores |
| Agregar valor a enum | ` estado: "A" | "I" | "P"` | Consumidores existentes válidos |
| Agregar header opcional | `X-Custom: value` | Ignorado |
| Agregar valor de respuesta | Nueva propiedad en response | Consumidores ignoran |

### Proceso de cambio

```
┌─────────────────────────────────────────────────────────────┐
│ 1. PROPONER                                                 │
│    - Documentar cambio en contract.md § "Cambios"           │
│    - Describir impacto y justificación                      │
├─────────────────────────────────────────────────────────────┤
│ 2. CLASIFICAR                                               │
│    - Marcar como BREAKING o NON-BREAKING                    │
│    - Si es breaking: evaluar migración                      │
├─────────────────────────────────────────────────────────────┤
│ 3. ACTUALIZAR DOCUMENTACIÓN                                 │
│    - Actualizar contract.md con nuevo endpoint/campo        │
│    - Actualizar openapi.yaml con esquema                    │
│    - Si es breaking: documentar en tabla de versionado      │
├─────────────────────────────────────────────────────────────┤
│ 4. IMPLEMENTAR                                              │
│    - Backend: controller + servicio + mapper                 │
│    - Frontend: types.ts + service.ts                        │
│    - Ambos en el mismo sprint si es breaking                │
├─────────────────────────────────────────────────────────────┤
│ 5. VERIFICAR                                                │
│    - Tests de integración                                   │
│    - Verificar contract.md = código = openapi.yaml          │
├─────────────────────────────────────────────────────────────┤
│ 6. PUBLICAR                                                 │
│    - Merge a main                                           │
│    - Actualizar changelog                                   │
│    - Notificar a consumidores si es breaking                │
└─────────────────────────────────────────────────────────────┘
```

### Tabla de versionado

Registrar todos los cambios en `contract.md`:

| Fecha | Tipo | Descripción | Breaking | Consumidores afectados |
|-------|------|-------------|----------|----------------------|
| 2026-08-26 | Baseline | Creación del contract | No | Todos |
| 2026-08-29 | Refactor | Unificación de rutas por dominio | **Sí** | Frontend |

### Política de deprecation

Cuando un endpoint o campo se depreca:

1. **Agregar header** en respuesta:
   ```
   Deprecation: true
   Sunset: 2026-12-31
   ```

2. **Mantener funcional** por al menos 2 versiones o 3 meses (lo que sea mayor)

3. **Documentar** en contract.md:
   ```markdown
   > **DEPRECATED** (2026-09-01): Usar `/nuevo/endpoint` en su lugar.
   > Será eliminado el 2026-12-31.
   ```

4. **Log de uso** para identificar consumidores que aún lo usan

5. **Eliminar** solo después del período de deprecation

### Reglas

| # | Regla |
|---|-------|
| R1 | `contract.md` es la fuente de verdad |
| R2 | Todo cambio debe documentarse antes de implementar |
| R3 | Los cambios breaking requieren coordinación backend+frontend |
| R4 | No eliminar endpoints sin período de deprecation |
| R5 | `openapi.yaml` debe estar sincronizado con `contract.md` |
| R6 | Los tests deben pasar antes de marcar un cambio como completo |

---

## 10. Seguridad

### JWT

- Algoritmo: HS256
- Secret: ≥ 32 bytes (configurar en `jwt.secret`)
- Claims mínimos: `sub`, `userId`, `userName`, `org`, `tenant`, `jti`, `iat`, `exp`

### API externa (`/api/*`)

- Header requerido: `x-api-key`
- Autenticación adicional: `Authorization: Bearer <token>` en endpoints autenticados

### Datos sensibles

- **No** loguear tokens, secretos ni claves
- **No** incluir datos sensibles en claims del JWT
- **No** commitear valores reales de configuración

---

## 11. Sincronización frontend ↔ contract

**Decisión de arquitectura (ARCH-011):** Los tipos TypeScript del frontend que
representan DTOs de comunicación HTTP deben ser **espejo exacto** de los definidos en
`contract.md`. El contract.md es la fuente de verdad; cualquier desviación es un defecto.

### Reglas

| # | Regla |
|---|-------|
| R1 | Cada dominio tiene un `<dominio>.types.ts` con interfaces del contract |
| R2 | Separar tipo de respuesta (`*DTO`) del tipo de request (`*Request`) |
| R3 | Los campos deben coincidir exactamente con el contract (mismo nombre, mismo tipo) |
| R4 | Los paths en el service deben coincidir con el contract (path + método HTTP) |
| R5 | Usar `SharedIdResponse` de `shared/api-types.ts` para respuestas con ID |
| R6 | Los tipos del contract son solo para comunicación HTTP; tipos de UI se extienden |
| R7 | No agregar campos que no existan en el contract a los tipos de comunicación |

### Estructura esperada

```
src/app/<dominio>/
  <dominio>.types.ts    ← Interfaces del contract (TaskDTO, TaskRequest, etc.)
  <dominio>.service.ts  ← Métodos con tipos del contract y paths exactos
src/app/shared/
  api-types.ts          ← SharedIdResponse, SharedApiErrorResponse
```

### Mapeo de endpoints legacy

Cuando coexisten dos paths para el mismo endpoint, usar el del contract:

| Contract path | Legacy path |
|---------------|-------------|
| `/document/api/guardarDocumento` | `/document/saveDocument` |
| `/document/api/consultarDocumento` | `/document/getDocument` |
| `/document/api/upload` | `/document/upload` |

Ver [ARCH-011](backlog-strategies/ARCH-011-frontend-api-sync.md) para el ADR completo.

---

## 12. Tareas de arquitectura pendientes

Ver [**ARCHITECTURE** en `backlog.md`](backlog.md#architecture) para ítems pendientes de estandarización.

---

## 13. Formato de fechas

**Decisión de arquitectura (ARCH-010):** Todas las fechas en comunicación HTTP (DTOs)
del ecosistema D3 deben seguir este formato estándar.

### Formato de serialización

```
yyyy-MM-dd@HH:mm:ss.SSSZ
```

| Parte | Descripción | Ejemplo |
|-------|-------------|---------|
| `yyyy` | Año (4 dígitos) | `2024` |
| `MM` | Mes (2 dígitos) | `01` |
| `dd` | Día (2 dígitos) | `15` |
| `@` | Separador literal (no ISO) | `@` |
| `HH` | Hora (24h, 2 dígitos) | `14` |
| `mm` | Minutos (2 dígitos) | `30` |
| `ss` | Segundos (2 dígitos) | `00` |
| `SSS` | Milisegundos (3 dígitos) | `000` |
| `Z` | Timezone offset | `-0500` |

**Ejemplo completo:** `2024-01-15@14:30:00.000-0500`

### Timezone

- **Zona:** `America/Bogota` (UTC-5, Colombia)
- **Regla:** Todas las fechas se serializan/deserializan en esta timezone
- **Sin ajuste de DST:** Colombia no aplica horario de verano

### Implementación en DTOs (Backend Java)

```java
@JsonFormat(shape = JsonFormat.Shape.STRING,
            pattern = "yyyy-MM-dd@HH:mm:ss.SSSZ",
            timezone = "America/Bogota")
private String fechaCreacion;
```

**Regla:** Todos los campos de fecha en DTOs usan `String`, no `Date` ni `LocalDateTime`.

### Constante de display

```java
// SharedConstants.java
public static final String FORMATO_FECHA = "dd/MM/yyyy";  // Solo para visualización
```

### Patrón de filtros de fecha

Las fechas en filtros siempre vienen en pares Min/Max:

| Campo Min | Campo Max | Uso |
|-----------|-----------|-----|
| `fechaMin` | `fechaMax` | Rango de creación |
| `fechaRegistroMin` | `fechaRegistroMax` | Rango de registro |
| `dueDateMin` | `dueDateMax` | Rango de vencimiento |
| `completedMin` | `completedMax` | Rango de completado |
| `createdAtMin` | `createdAtMax` | Rango de creación (inglés) |

**Regla:** Ambos campos son opcionales. Si solo se envía `Min`, filtra desde esa fecha.
Si solo se envía `Max`, filtra hasta esa fecha.

### Uso en MyBatis XML

```xml
<if test="fechaMin != null">
  AND xxx_fecha >= #{fechaMin}
</if>
<if test="fechaMax != null">
  AND xxx_fecha <![CDATA[ < ]]> #{fechaMax}
</if>
```

> Usar `CDATA[ < ]>` para evitar problemas de parseo XML.

### Nota sobre formato `@` vs `T`

El separador `@` no es ISO 8601 (que usa `T`). Se mantiene por compatibilidad con el
frontend actual. Si se migra a `T` en el futuro, actualizar tanto backend como frontend
simultáneamente. Marcar como deuda técnica si se decide migrar.
