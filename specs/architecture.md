# Arquitectura — Estándares globales del ecosistema D3

Este archivo define los **estándares transversales** que aplican a todos los dominios
del ecosistema D3 (back `d3brain` + front `d3_front`). Toda decisión de implementación
debe respetar estos lineamientos.

## Tabla de contenido

1. [Convención de paquetes (backend)](#1-convención-de-paquetes-backend)
2. [Estructura de endpoints REST](#2-estrutura-de-endpoints-rest)
3. [Formato de DTOs](#3-formato-de-dtos)
4. [Multi-tenancy](#4-multi-tenancy)
5. [Manejo de errores](#5-manejo-de-errores)
6. [Base de datos (MyBatis)](#6-base-de-datos-mybatis)
7. [Contrato API](#7-contrato-api)
8. [Seguridad](#8-seguridad)
9. [Frontend (Angular)](#9-frontend-angular)
10. [Tareas de arquitectura pendientes](#10-tareas-de-arquitectura-pendientes)

---

## 1. Convención de paquetes (backend)

Todos los módulos usan la raíz `d3.<dominio>` (ver [ARCH-001](backlog-strategies/ARCH-001-package-rename.md)):

```
d3.<dominio>/
  domain/           # DTOs, filtros, entidades (@Alias MyBatis)
  application/      # Servicios de negocio
  infrastructure/   # Controllers, Mappers MyBatis, configuración
```

| Dominio | Paquete |
|---------|---------|
| authentication | `d3.authentication` |
| documents | `d3.document_execution`, `d3.document_transition`, `d3.document_transaction` |
| tasks | `d3.task` |
| accounting | `d3.accounting_voucher`, `d3.accounting_plan`, `d3.accounting_api` |
| massive | `d3.massiveload` |
| notifications | `d3.notification` |
| config-forms | `d3.property`, `d3.configuration_file`, `d3.process_form` |
| persons | `d3.logisticpymes` |
| authorization | `d3.authorization` |
| fe | `d3.fe` |
| upload | `d3.upload` |
| webservice | `d3.webservice` |
| process-designer | `d3.process_designer` |
| multitenancy | `d3.multitenancy` |
| inventory | `d3.inventory` |
| money | `d3.money` |
| tariff | `d3.tariff` |
| report | `d3.report` |
| mail | `d3.mail` |
| homologate | `d3.homologate` |
| consumption-units | `d3.consumption_units` |

**Regla:** No crear paquetes fuera de esta estructura. Si un módulo requiere subdominios,
usar `d3.<dominio>.<subdominio>`.

---

## 2. Estructura de endpoints REST

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

## 3. Formato de DTOs

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

## 4. Multi-tenancy

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

## 5. Manejo de errores

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

## 6. Base de datos (MyBatis)

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

## 7. Contrato API

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

## 8. Seguridad

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

## 9. Frontend (Angular)

### Estructura de servicios

```typescript
// services/<dominio>.service.ts
@Injectable({ providedIn: 'root' })
export class <Dominio>Service {
  // Métodos que consumen la API
}
```

### Convenciones

- Servicios en `src/app/services/`
- Componentes en `src/app/components/<dominio>/`
- DTOs en `src/app/models/` (nombres `sw42.<dominio>` para compatibilidad legacy)

### Autenticación

- Token almacenado en `localStorage` (`JWT_TOKEN`)
- Headers enviados vía `HttpInterceptor`
- Refresh automático al expirar (pendiente: ver T-AUTH-015)

---

## 10. Tareas de arquitectura pendientes

Ver [**ARCHITECTURE** en `backlog.md`](backlog.md#architecture) para ítems pendientes de estandarización.
