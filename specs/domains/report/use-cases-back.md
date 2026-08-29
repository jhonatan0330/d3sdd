# Casos de uso — Reportes (REPORT) (BACKEND)

> Capa backend (d3brain). Contratos/endpoints aquí. Los pasos de UI (si los hubiera) van en use-cases-front.md.

Dominio: `report`. Paquete `d3.report`. Generación de reportes (servlet + servicios).

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-REP-001 | Generar reporte Jasper (PDF/Excel/HTML) | Usuario autenticado | 🔧 |
| CU-REP-002 | Ejecutar reporte SQL directo (CSV) | Usuario autenticado | 🔧 |
| CU-REP-003 | Gestionar definiciones de reporte (CRUD) | Administrador | 🔧 |
| CU-REP-004 | Listar reportes disponibles por documento | Usuario autenticado | 🔧 |
| CU-REP-005 | Consultar historial de ejecuciones | Usuario autenticado | 🔧 |
| CU-REP-006 | Acceso a reporte público sin autenticación | Usuario anónimo | 🔧 |

---

## CU-REP-001: Generar reporte Jasper (PDF/Excel/HTML)

**Descripción**: Genera reportes basados en plantillas JRXML compiladas con JasperReports. Soporta subreportes, encabezados y pies de página configurables.

**Actores**: Usuario autenticado (token JWT o sesión)

**Entrada**:
- `nombre` / `n`: Código o llave del reporte base (`ReporteBaseDTO`)
- `P_KEY`: Llave del documento asociado (opcional, para parámetros contextuales)
- Parámetros dinámicos: Cualquier parámetro de request se pasa a Jasper (convertido a mayúsculas)
- `P_JASPERTIPO`: Formato de salida (`PDF`, `XLS`, `HTML`); por defecto `PDF`
- `P_TOKEN`: Token de autenticación

**Flujo**:
1. `ReporteServlet.doGet()` recibe petición → valida reporte con `ReporteBaseSvc.validateReport()`
2. `ReporteBaseSvc.generarReporte()` orquesta la generación:
   - Crea registro `ReporteEjecucionDTO` (inicio, reporte, documento, usuario)
   - Valida `REP_PRINT_ONE`: si configurado, solo permite una ejecución por documento
   - Obtiene parámetros: `llenarParametros()` (características del documento) + `parametrosPropiedades()` (propiedades del reporte: JRXML, subreportes, imágenes, etc.)
   - Instancia `GeneradorReportes` con `DataSource` o connection string externa (`CONNECTION_STRING_DB`)
   - Según `tipoReporte`:
     - **PDF**: `exportarReportePDF()` → soporta auto-impresión (JS), impresión silenciosa, impresora específica
     - **XLS**: `exportarReporteExcel()` → config: collapse rows, detect cell type, wrap text
     - **HTML**: `exportarReporteHTML()`
     - **CSV**: `ReportGenerateFromSql.call()` → ejecuta SQL directo, exporta CSV con BOM UTF-8
   - Guarda archivo en storage (`UploadSvc`) si no tiene `REP_EXCLUDE_STORAGE_FILE`
   - Persiste `ReporteEjecucionDTO` con `saveWithHistoric()` (histórico si documento lo requiere)
3. Retorna `ReportDTO` con contenido binario, nombre y metadata de ejecución
4. `ReporteServlet.downloadFile()` escribe respuesta con `Content-Disposition inline` y `Content-Type` según extensión

**Salida**: Archivo binario (PDF/XLS/HTML/CSV) con headers de descarga inline

**Errores**: `ServerException` por reporte no encontrado, sin permisos, OOM, JRXML faltante, conexión BD

**Componentes**: `ReporteServlet`, `ReporteBaseSvc`, `GeneradorReportes`, `ReportesUtil`, `JasperReportCache`, `ReporteEjecucionSvc`, `UploadSvc`

---

## CU-REP-002: Ejecutar reporte SQL directo (CSV)

**Descripción**: Ejecuta query SQL nativa definida en propiedad `REPORT_QUERY` del reporte, sustituye parámetros `$P{param}` y exporta CSV.

**Actores**: Usuario autenticado

**Entrada**: Igual que CU-REP-001 + propiedad `REPORT_QUERY` en reporte base

**Flujo**: En `ReporteBaseSvc.generarReporte()`, si `tipoReporte=CSV` o hay `REPORT_QUERY` sin `REP_TYPE_EXPORT` → llama `ReportGenerateFromSql.call()` que:
1. Reemplaza `$P{param}` en SQL por valores del mapa de parámetros
2. Ejecuta query con `Statement.executeQuery()`
3. Genera CSV con cabecera (nombres de columnas), filas con valores escapados (`"` para strings), separador `;`
4. Retorna bytes con BOM UTF-8 (`\uFEFF`)

**Salida**: CSV (text/csv)

**Componentes**: `ReportGenerateFromSql`, `GeneradorReportes`

---

## CU-REP-003: Gestionar definiciones de reporte (CRUD)

**Descripción**: CRUD completo de `ReporteBaseDTO` (definiciones de reporte maestro).

**Actores**: Administrador

**Operaciones**:
- **Crear**: `guardar()` → valida unicidad código/nombre (`validateUnique()`), persiste, crea propiedad `REPORTE_JRXML` vacía
- **Leer**: `consultaXId()`, `getByCode()`, `listarConsulta()`, `listarMenu()` (para menú lateral)
- **Actualizar**: `actualizar()` → valida unicidad, actualiza propiedad nombre
- **Activar/Inactivar**: `activar()`/`inactivar()` (estado `A`/`I`)
- **Listar por documento**: `listarDisponiblesDocumento()` → filtros por plantilla + estado activo, enriquece con propiedades cacheadas

**DTO**: `ReporteBaseDTO` (extiende `BasicDTO`): `codigo`, `nombre`, `plantilla`, `publico`, `propiedades` (lista `PropiedadDTO`)

**Componentes**: `ReporteBaseSvc`, `ReporteBaseMapper`, `PropiedadSvc`, `PropertyGetWithCacheService`

---

## CU-REP-004: Listar reportes disponibles por documento

**Descripción**: Obtiene reportes activos asociados a una plantilla/documento para mostrar en UI.

**Actores**: Usuario autenticado

**Entrada**: `documento` (llave de plantilla)

**Salida**: `List<ReporteBaseDTO>` con propiedades cacheadas (`cachePropertyService.obtenerPropiedades()`)

**Endpoint**: No expuesto directamente en servlet; usado por frontend vía API REST

**Componentes**: `ReporteBaseSvc.listarDisponiblesDocumento()`

---

## CU-REP-005: Consultar historial de ejecuciones

**Descripción**: Consulta y filtra ejecuciones previas de reportes (`ReporteEjecucionDTO`).

**Actores**: Usuario autenticado

**DTO**: `ReporteEjecucionDTO`: `reporte`, `documento`, `fechaInicio`, `fechaFin`, `error`, `usuario`, `url` (archivo en storage)

**Operaciones** (heredadas de `BasicSvc` vía `ReporteEjecucionSvc`):
- `consultaXId(llave)`, `consultaUnica(filtro)`, `listarConsulta(filtro)`, `contarResultados(filtro)`
- `saveWithHistoric()`: guarda ejecución; si `historico>0` inserta en tabla histórica

**Filtros**: `ReporteEjecucionFilterDTO`: `llaveTabla`, `documento`, `reporte`, `usuario`, rangos de fecha

**Componentes**: `ReporteEjecucionSvc`, `ReporteEjecucionMapper`

---

## CU-REP-006: Acceso a reporte público sin autenticación

**Descripción**: Reportes marcados `publico=true` en `ReporteBaseDTO` pueden ejecutarse sin token válido.

**Flujo**: En `generarReporte()`, si `usuario==null` y `reporte.getPublico()` → usa `autenticacionService.getUserSystemKey()` (usuario sistema)

**Restricción**: Solo aplica a reportes con flag `publico`; resto requiere token válido

---

## Endpoints expuestos

| Método | Ruta | Descripción | Estado |
|--------|------|-------------|--------|
| GET | `/ReporteServlet` | Genera y descarga reporte (legacy) | 🔄 Deprecado |
| GET | `/api/reports/generate` | Genera y descarga reporte (REST) | ✅ Objetivo |
| GET | `/api/reports/{id}/generate` | Genera reporte por ID (RESTful) | 📋 Futuro |
| POST | `/api/reports/generate` | Genera reporte con body JSON | 📋 Futuro |
| REST | `/api/reportes/*` | CRUD definiciones, listados, historial (CU-REP-003, CU-REP-004, CU-REP-005) | ✅ Existente |

> **Migración en curso**: Ver `migration-servlet-to-rest.md` para plan detallado. Frontend (`FormReportService`) debe actualizar URL base de `/reporte` a `/api/reports/generate`.

---

## Configuración de reporte (propiedades clave)

| Propiedad | Uso |
|-----------|-----|
| `REPORTE_JRXML` | Cuerpo principal del reporte (JRXML) |
| `REPORTE_EXCEL` | JRXML específico para exportación Excel |
| `REPORTE_ENCABEZADO` / `REPORTE_ENCABEZADO_EXCEL` | Subreporte de encabezado (PDF/Excel) |
| `REPORTE_PIE_PAGINA` | Subreporte de pie de página |
| `P_SUBREPORT_*` | Subreportes dinámicos múltiples |
| `REPORT_QUERY` | SQL nativo para exportación CSV |
| `REP_TYPE_EXPORT` | Tipo por defecto (`PDF`, `XLS`, `HTML`, `CSV`) |
| `CONNECTION_STRING_DB` | DS externo (formato `url;;user;;pass`) |
| `REP_PRINT_ONE` | Solo una ejecución por documento |
| `REP_EXCLUDE_STORAGE_FILE` | No guardar archivo en storage |

---

## Cache de reportes (`JasperReportCache`)

- Cachea `JasperReport` compilados por key (nombre JRXML + `reportKey` = llave de reporte base)
- Evita recompilar JRXML en cada ejecución
- Usado en `ReportesUtil` y `GeneradorReportes` vía `pCache.getReport(jrxml, reportKey)`

---

## Manejo de errores y OOM

- `OutOfMemoryError` capturado en `ReportesUtil` → lanza `ServerException` con mensaje específico + notifica a admin vía `MailSendMessageToAdminService`
- Errores en generación → persiste `ReporteEjecucionDTO` con `error` y `fechaFin` antes de relanzar excepción

