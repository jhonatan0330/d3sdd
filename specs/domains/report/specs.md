# Specs — Reportes (REPORT)

## Requisitos

### Generación de reportes
- R-REP-001: Generar reporte Jasper en formatos PDF, Excel (XLS) y HTML desde plantilla JRXML.
- R-REP-002: Generar reporte CSV ejecutando SQL nativo (`REPORT_QUERY`) con sustitución de parámetros `$P{param}`.
- R-REP-003: Soporte para subreportes dinámicos (`P_SUBREPORT_*`), encabezados (`REPORTE_ENCABEZADO`, `REPORTE_ENCABEZADO_EXCEL`) y pies de página (`REPORTE_PIE_PAGINA`).
- R-REP-004: Auto-impresión PDF vía JavaScript (`P_AUTO_PRINT`, `P_DIALOG_PRINT`, `P_PRINTER_NAME`, `PDF_JAVASCRIPT`).
- R-REP-005: Configuración de tipo de exportación por defecto por reporte (`REP_TYPE_EXPORT`).
- R-REP-006: Conexión a base de datos externa configurable por reporte (`CONNECTION_STRING_DB`).

### Parámetros y contexto
- R-REP-010: Parámetros desde request HTTP (convertidos a mayúsculas) + parámetros de características del documento (`llenarParametros`).
- R-REP-011: Parámetros desde propiedades del reporte (`parametrosPropiedades`: JRXML, subreportes, imágenes, configs).
- R-REP-012: Parámetros de fecha parseados automáticamente (`D3Utils.verificarFechaHora`).
- R-REP-013: Parámetros múltiples (`P_MULTIPLE` como array `;`).

### Gestión de definiciones de reporte (CRUD)
- R-REP-020: CRUD de `ReporteBaseDTO` (código, nombre, plantilla, público, propiedades).
- R-REP-021: Validación de unicidad de código y nombre en activo.
- R-REP-022: Listar reportes disponibles por documento/plantilla con propiedades cacheadas.
- R-REP-023: Listar reportes para menú lateral (`listarMenu`).

### Ejecución y auditoría
- R-REP-030: Registro de ejecución en `ReporteEjecucionDTO` (inicio, fin, error, usuario, documento, URL storage).
- R-REP-031: Restricción de una sola ejecución por documento (`REP_PRINT_ONE`).
- R-REP-032: Almacenamiento automático de archivo generado en storage (`UploadSvc`) salvo `REP_EXCLUDE_STORAGE_FILE`.
- R-REP-033: Histórico de ejecuciones si documento tiene `historico > 0` (`saveWithHistoric`).
- R-REP-034: Consulta y filtrado de historial (`ReporteEjecucionFilterDTO`).

### Acceso y seguridad
- R-REP-040: Autenticación por token JWT/sesión (`P_TOKEN` header o parámetro).
- R-REP-041: Acceso a reportes públicos (`publico=true`) sin token (usuario sistema).
- R-REP-042: Validación de permisos vía `PropiedadSvc.validarFuncionConsultandoPropiedad`.

### No funcionales
- NF-REP-001: Cache de reportes compilados (`JasperReportCache`) para evitar recompilar JRXML.
- NF-REP-002: Expone vía servlet (`ReporteServlet`) y API REST (`/api/getReport`).
- NF-REP-003: Manejo de `OutOfMemoryError` con mensaje amigable y notificación a administrador (`MailSendMessageToAdminService`).
- NF-REP-004: Transaccionalidad en operaciones de escritura (`@Transactional` propagation REQUIRED).
- NF-REP-005: Multi-tenancy via `DataSource` por tenant (inyectado en `ReporteBaseSvc`).

## Diseño

### Paquetes (ARCH-001, realizado)
- `d3.report`
  - `domain`: DTOs y filtros (`@Alias` MyBatis)
  - `application`: Servicios y utilidades de generación
  - `infrastructure`: Mappers MyBatis

### Componentes

#### Capa de presentación (actual: Servlet → objetivo: REST Controller)
- **Actual**: `ReporteServlet` (`d3.report`): `HttpServlet` endpoint GET `/ReporteServlet`. Inyecta `ReporteBaseSvc`. Maneja request/response, parsea parámetros, llama `generarReporte()`, escribe binary response con `Content-Disposition inline` y `Content-Type` por extensión.
- **Objetivo**: `ReportRestController` (`d3.report.infrastructure`): `@RestController @RequestMapping("/api/reports")`. Endpoint `GET /generate` que retorna `ResponseEntity<byte[]>` con headers `Content-Disposition`, `Content-Type`. Delegación a `ReporteBaseSvc.generarReporte()` idéntica al servlet.

#### Endpoints REST propuestos

| Método | Ruta | Descripción |
|--------|------|-------------|
| GET | `/api/reports/generate` | Genera y descarga reporte (reemplaza `/ReporteServlet`) |
| GET | `/api/reports/{id}/generate` | Genera reporte por ID (alternativa RESTful) |
| POST | `/api/reports/generate` | Genera reporte con body JSON (parámetros complejos) |

#### Parámetros de request (query params)
- `nombre` / `n`: Código o llave del reporte base (`ReporteBaseDTO`)
- `P_KEY`: Llave del documento asociado (opcional)
- `P_TOKEN`: Token de autenticación (JWT o sesión)
- `P_JASPERTIPO`: Formato salida (`PDF`, `XLS`, `HTML`, `CSV`)
- Parámetros dinámicos: Cualquier otro query param → se pasa a Jasper (mayúsculas)
- `P_MULTIPLE`: Array separado por `;`

#### Capa de aplicación (servicios)
- `ReporteBaseSvc` (`d3.report.application`): Servicio principal. Extiende `BasicSvc<ReporteBaseDTO, ReporteBaseFilterDTO>`. Orquesta generación completa:
  - `validateReport()`: busca por ID o código, carga propiedades cacheadas
  - `generarReporte()`: flujo principal (ver CU-REP-001)
  - `llenarParametros()`: extrae características de `PedidoVentaCaracteristicaSvc`
  - `parametrosPropiedades()`: resuelve propiedades del reporte (JRXML, subreportes, imágenes) vía `PropertyGetWithCacheService`
  - `listarDisponiblesDocumento()`, `listarMenu()`, `getByCode()`, `validateUnique()`
  - Inyección `@Lazy` de 11 dependencias (mappers, servicios dominio, cache, datasource)

- `ReporteEjecucionSvc` (`d3.report.application`): Extiende `BasicSvc<ReporteEjecucionDTO, ReporteEjecucionFilterDTO>`. CRUD + `saveWithHistoric()` para auditoría con tabla histórica opcional.

- `GeneradorReportes` (`d3.report.application`): Wrapper de conexión JDBC + `JasperReportCache`. Dos constructores:
  - `DataSource` + cache + `reportKey` (tenant actual)
  - `connectionString` externa (`url;;user;;pass`) → SQL Server driver
  - Métodos: `generarReportePDF()`, `generarReporteExcel()`, `generarReporteHTML()`, `closeConnection()`

- `ReportesUtil` (`d3.report.application`): Static utils de exportación JasperReports:
  - `exportarReportePDF()`: `JRPdfExporter` + config auto-print/printer
  - `exportarReporteExcel()`: `JRXlsExporter` + config collapse/detect/wrap
  - `exportarReporteHTML()`: `HtmlExporter`
  - `replaceReport()`, `replaceHeader()`, `replaceFooter()`: manipulación JRXML dinámica para inyectar subreportes/encabezados/pies
  - Manejo `OutOfMemoryError` → `ServerException` con notificación admin

- `ReportGenerateFromSql` (`d3.report.application`): Ejecución SQL nativa → CSV. `call(sql, params, conexion)`: reemplaza `$P{param}`, ejecuta `Statement`, genera CSV `;` separated con BOM UTF-8.

- `JasperReportCache` (`d3.report.application`): Cache de `JasperReport` compilados. Key: `jrxmlName + reportKey`. Evita recompilar en cada request.

#### Capa de infraestructura (persistencia)
- `ReporteBaseMapper` (`d3.report.infrastructure`): `@D3SqlConnMapper` + XML. CRUD `ReporteBaseDTO`, `listarMenu()`, `getFullToSynchronize()`.
- `ReporteEjecucionMapper` (`d3.report.infrastructure`): `@D3SqlConnMapper` + XML. CRUD `ReporteEjecucionDTO` + `insertarHistorico()`.

### DTOs (domain)

| DTO | Paquete | Descripción |
|-----|---------|-------------|
| `ReporteBaseDTO` | `d3.report.domain` | Definición maestra: `codigo`, `nombre`, `plantilla`, `publico`, `propiedades:List<PropiedadDTO>` |
| `ReporteBaseFilterDTO` | `d3.report.domain` | Filtros: `llaveTabla`, `codigo`, `nombre`, `plantilla`, `estado` |
| `ReporteEjecucionDTO` | `d3.report.domain` | Auditoría: `reporte`, `documento`, `fechaInicio`, `fechaFin`, `error`, `usuario`, `url` |
| `ReporteEjecucionFilterDTO` | `d3.report.domain` | Filtros: `llaveTabla`, `documento`, `reporte`, `usuario`, fechas |
| `ReportDTO` | `d3.report.domain` | Resultado generación: `content:byte[]`, `name:String`, `data:ReporteEjecucionDTO` |

### Flujo de datos (generación)

```
HTTP GET /ReporteServlet?nombre=X&P_KEY=Y&params...
    ↓
ReporteServlet.doGet()
    ↓ parse params (uppercase, dates, P_MULTIPLE)
    ↓
ReporteBaseSvc.validateReport(nombre, token)
    ↓ load props from cache (PropertyGetWithCacheService)
    ↓
ReporteBaseSvc.generarReporte(reporte, key, params, token)
    ↓ create ReporteEjecucionDTO (inicio)
    ↓ validate REP_PRINT_ONE
    ↓ llenarParametros(key) + parametrosPropiedades(reporte, usuario)
    ↓
GeneradorReportes (DataSource o connectionString)
    ↓
ReportesUtil.exportarReporte{PDF|Excel|HTML}()  OR  ReportGenerateFromSql.call()
    ↓ uses JasperReportCache.getReport(jrxml, reportKey)
    ↓
byte[] resultado
    ↓
UploadSvc.uploadFile() → url (si no REP_EXCLUDE_STORAGE_FILE)
    ↓
ReporteEjecucionSvc.saveWithHistoric(ejecucion, historic)
    ↓
ReportDTO(content, name, ejecucion)
    ↓
ReporteServlet.downloadFile() → HTTP response (binary + headers)
```

### Configuración clave (propiedades `PropiedadDTO` en reporte)

| Key | Tipo | Descripción |
|-----|------|-------------|
| `REPORTE_JRXML` | String | JRXML principal (body) |
| `REPORTE_EXCEL` | String | JRXML específico Excel |
| `REPORTE_ENCABEZADO` | String | Subreporte encabezado PDF |
| `REPORTE_ENCABEZADO_EXCEL` | String | Subreporte encabezado Excel |
| `REPORTE_PIE_PAGINA` | String | Subreporte pie de página |
| `P_SUBREPORT_*` | String | Subreportes dinámicos (múltiples) |
| `REPORT_QUERY` | String | SQL nativo para CSV |
| `REP_TYPE_EXPORT` | String | Default: `PDF`, `XLS`, `HTML`, `CSV` |
| `CONNECTION_STRING_DB` | String | `url;;user;;pass` para DB externa |
| `REP_PRINT_ONE` | Boolean | Solo 1 ejecución por documento |
| `REP_EXCLUDE_STORAGE_FILE` | Boolean | No guardar en storage |
| `REPORTE_IMAGEN` | String | Imagen base64 (prefijo `data:,`) |

### Dependencias externas
- **JasperReports** 6.x: `net.sf.jasperreports:*` (engine, exporters)
- **MyBatis** 3.0.5: mappers + XML
- **Spring** JDBC: `DataSource`, `@Transactional`
- **Storage**: `UploadSvc` (archivos generados)
- **Mail**: `MailSendMessageToAdminService` (alertas OOM)
