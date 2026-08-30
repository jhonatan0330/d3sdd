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
se crean carpetas con los mismos nombres de dominio del backend. Cada carpeta contiene
los componentes, servicios y modelos de ese dominio.

```
src/app/
  <dominio>/           # Componentes, servicios, modelos del dominio
    components/        # Componentes Angular
    services/          # Servicios que consumen la API
    models/            # DTOs, interfaces
  layout/              # Interfaz gráfica general (header, sidebar, footer, navegación)
  shared/              # Objetos transversales (ver sección 3)
```

### Responsabilidades por carpeta

| Carpeta | Contenido |
|---------|-----------|
| `<dominio>/` | Componentes, servicios y modelos específicos del dominio |
| `layout/` | Componentes de interfaz: header, sidebar, footer, navegación principal |
| `shared/` | Utilidades, componentes reutilizables, interceptors, guards, modelos comunes |

### Convenciones

- Servicios en `<dominio>/services/<dominio>.service.ts`
- Componentes en `<dominio>/components/`
- Modelos/DTOs en `<dominio>/models/` (nombres `sw42.<dominio>` para compatibilidad legacy)

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

### Convenciones de rutas

| Elemento | Convención | Ejemplo |
|----------|------------|---------|
| Recurso principal | `/<dominio>` | `/task`, `/user`, `/acc/voucher` |
| Sub-recursos | `/<recurso>/{id}` | `/task/{id}` |
| Acciones especiales | `/<recurso>/{accion}` | `/task/create`, `/rest/changeState` |

### Métodos HTTP

| Operación | Método | Ruta | Body |
|-----------|--------|------|------|
| Listar | `GET` o `POST` | `/<recurso>` | Filtros (si son complejos) |
| Obtener por ID | `GET` | `/<recurso>/{id}` | No |
| Crear | `POST` | `/<recurso>/create` | DTO |
| Actualizar | `POST` | `/<recurso>/update` | DTO |
| Eliminar | `POST` | `/<recurso>/delete/{id}` | No |

**Nota:** El proyecto usa `POST` para operaciones de lectura con filtros complejos
(decisión legacy; mantener por compatibilidad).

### Autenticación

Todos los endpoints requieren header `Authorization: Bearer <token>` excepto:
- `/main/autenticarUsuarioAutenticacion` (login)
- `/main/checkToken` (validación de token)
- `/main/solicitarNuevaClave` (recuperación)
- Endpoints marcados como públicos en el contrato

---

## 5. Formato de DTOs

### Nomenclatura

| Tipo | Sufijo | Ejemplo |
|------|--------|---------|
| DTO de respuesta | `DTO` | `UsuarioDTO`, `PedidoVentaDTO` |
| DTO de filtro | `FilterDTO` | `UsuarioFilterDTO`, `PedidoVentaFilterDTO` |
| DTO de request | `Request` o `DTO` | `TaskRequest`, `VoucherPrepareRequest` |
| Respuesta con ID | `SharedIdResponse` | `SharedIdResponse` |
| Respuesta con error | `SharedApiErrorResponse` | `SharedApiErrorResponse` |

### Convenciones de campos

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `llaveTabla` | `String` | ID primario (siempre String en DTOs) |
| `estado` | `String` | `A` = activo, `I` = inactivo |
| `tenant` | `String` | Tenant (resuelto desde sesión, no en request) |

### Serialización de fechas

- Formato: `yyyy-MM-dd@HH:mm:ss.SSSZ`
- Timezone: `America/Bogota`
- Ejemplo: `2024-01-15@14:30:00.000-0500`

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

### Formato de respuesta de error

```json
{
  "code": "ERROR_CODE",
  "message": "Mensaje descriptivo para el usuario",
  "details": [...]
}
```

### Códigos de error comunes

| Código | HTTP | Descripción |
|--------|------|-------------|
| `UNAUTHORIZED` | 401 | Token inválido o expirado |
| `FORBIDDEN` | 403 | Sin permisos para la operación |
| `NOT_FOUND` | 404 | Recurso no encontrado |
| `VALIDATION_ERROR` | 400 | Error de validación de datos |
| `INTERNAL_ERROR` | 500 | Error interno del servidor |

### Excepciones personalizadas

```java
// Usar excepciones específicas, no Exception genérica
throw new ServerException("ERROR_CODE", "mensaje");

// En controladores:
@ExceptionHandler(ServerException.class)
public ResponseEntity<SharedApiErrorResponse> handleServerException(ServerException e) {
    // ...
}
```

---

## 8. Base de datos (MyBatis)

### Convenciones de tablas

| Elemento | Convención | Ejemplo |
|----------|------------|---------|
| Tabla | `<prefijo>_<nombre>` | `usuario_usrp`, `consumption_unit_balance_cucb` |
| PK | `<prefijo>_llave` | `usrp_llave`, `ccuc_llave` |
| FK | `<prefijo>_<tabla_ref>_llave` | `ccum_balance_llave` |
| Tenant | `<prefijo>_tenant` | `ccuc_tenant` |

### Mappers

- Ubicación: `d3.<dominio>.infrastructure`
- Anotación: `@D3SqlConnMapper`
- XML: `resources/mappers/<dominio>/<Mapper>.xml`

### Transacciones

```java
@Transactional(rollbackFor = Exception.class)
public void operacionCritica() {
    // ...
}
```

---

## 9. Contrato API

### Fuentes de verdad

1. **Human-readable:** `contract.md`
2. **Máquina-legible:** `openapi.yaml`

### Proceso de cambios

1. Documentar el cambio en `api-contract.md`
2. Actualizar `openapi.yaml`
3. Marcar como **breaking** o **no-breaking**
4. Implementar en backend y frontend

### Versionado

- No hay versionado formal aún
- Los cambios breaking se listan en `api-contract.md` > "Cambios y versionado"

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
