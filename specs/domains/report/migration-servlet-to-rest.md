# Migración: Servlet → REST Controller (Reportes)

## Contexto actual

**Backend**: `ReporteServlet` (`d3.report.ReporteServlet`) — `HttpServlet` mapeado a `/ReporteServlet`
**Frontend**: `FormReportService.buildReportUrl()` construye URL `/reporte?nombre=X&P_KEY=Y&P_TOKEN=Z` y abre en `window.open('_blank')`

## Cambios Backend

### 1. Nuevo `ReportRestController` (`d3.report.infrastructure`)

```java
@RestController
@RequestMapping("/api/reports")
@RequiredArgsConstructor(onConstructor_ = @Lazy)
public class ReportRestController {

    private final ReporteBaseSvc reporteBaseService;

    @GetMapping("/generate")
    public ResponseEntity<byte[]> generate(
            @RequestParam(required = false, name = "nombre") String nombre,
            @RequestParam(required = false, name = "n") String n,
            @RequestParam(required = false, name = "P_KEY") String key,
            @RequestParam(required = false, name = "P_TOKEN") String token,
            @RequestParam(required = false, name = "P_JASPERTIPO") String tipoReporte,
            @RequestParam Map<String, String> allParams,
            HttpServletRequest request) {

        // Lógica idéntica a ReporteServlet.doGet()
        // Parse params, llamar reporteBaseService.generarReporte()
        // Retornar ResponseEntity<byte[]> con headers
    }
}
```

### 2. Mantenimiento compatibilidad (opcional)
- Mantener `ReporteServlet` vivo durante transición
- O registrar `ReporteServlet` como bean Spring y mapear también a `/api/reports/generate` via `ServletRegistrationBean`

### 3. Diferencias clave Servlet vs REST Controller

| Aspecto | Servlet | REST Controller |
|---------|---------|-----------------|
| Return type | `void` (escribe en `HttpServletResponse`) | `ResponseEntity<byte[]>` |
| Params | `request.getParameterNames()` enumeration | `@RequestParam` + `Map<String,String>` |
| Content-Type | `response.setContentType()` manual | `HttpHeaders.CONTENT_TYPE` en `ResponseEntity` |
| Error handling | `PrintWriter` XML manual | `@ExceptionHandler` + `ResponseEntity` |
| Testing | MockMvc + `MockHttpServletRequest` | MockMvc + `get("/api/reports/generate")` |

### 4. Registro de servlet (si se mantiene compatibilidad)
```java
@Bean
public ServletRegistrationBean<ReporteServlet> reporteServletRegistration(ReporteServlet servlet) {
    return new ServletRegistrationBean<>(servlet, "/ReporteServlet");
}
```

## Cambios Frontend

### 1. `FormReportService.buildReportUrl()` — Nueva URL

```typescript
// ANTES
let url = serverUrl + '/reporte?nombre=' + reporte.llaveTabla + '&P_KEY=' + pKey + '&P_TOKEN=' + token;

// DESPUÉS
let url = serverUrl + '/api/reports/generate?nombre=' + reporte.llaveTabla + '&P_KEY=' + pKey + '&P_TOKEN=' + token;
```

### 2. `FormReportService.openReport()` — Sin cambios
Sigue usando `window.open(url, '_blank')` — el navegador maneja la descarga binary igual.

### 3. Consideraciones CORS
- Si frontend y backend mismo origen → sin cambios
- Si dominios distintos → configurar `WebMvcConfigurer.addCorsMappings()` para `/api/reports/**`

### 4. Autenticación
- Token sigue pasando por `P_TOKEN` query param (compatibilidad)
- Futuro: migrar a header `Authorization: Bearer <jwt>` + `@RequestHeader`

## Plan de migración por fases

### Fase 1: Backend (paralelo)
- [ ] Crear `ReportRestController` en `d3.report.infrastructure`
- [ ] Migrar lógica de `doGet()` a `@GetMapping("/generate")`
- [ ] Usar `ResponseEntity<byte[]>` con `HttpHeaders`
- [ ] Añadir `@ExceptionHandler(ServerException.class)` para errores consistentes
- [ ] Test unitario + integración (MockMvc)

### Fase 2: Frontend (feature flag)
- [ ] Añadir config `environment.useReportRestApi = true/false`
- [ ] `FormReportService.buildReportUrl()` usa flag para elegir path
- [ ] Probar en staging con flag ON

### Fase 3: Cutover
- [ ] Activar flag en producción
- [ ] Monitorear logs / métricas 48h
- [ ] Deprecation warning en `ReporteServlet` (log cada uso)

### Fase 4: Limpieza
- [ ] Eliminar `ReporteServlet` y su registro
- [ ] Eliminar flag feature
- [ ] Actualizar documentación OpenAPI (`openapi.yaml`)

## Testing checklist

| Escenario | Backend | Frontend |
|-----------|---------|----------|
| PDF básico | ✅ | ✅ |
| Excel con subreportes | ✅ | ✅ |
| HTML | ✅ | ✅ |
| CSV (SQL nativo) | ✅ | ✅ |
| Reporte público (sin token) | ✅ | ✅ |
| REP_PRINT_ONE (segunda ejecución falla) | ✅ | ✅ |
| Parámetros fecha (parseo) | ✅ | ✅ |
| P_MULTIPLE array | ✅ | ✅ |
| Error OOM → mensaje amigable | ✅ | ✅ |
| Archivo grande (>50MB) streaming | ✅ | ⚠️ ver nota |

## Nota: Archivos grandes — Estrategia híbrida Binary / Redirect URL

**Problema**: `ResponseEntity<byte[]>` carga todo en memoria (OOM risk >50MB).
**Storage actual**: FTP/local accesible públicamente (URL directa).

### Solución: Redirect 302 para archivos grandes

```java
@GetMapping("/generate")
public ResponseEntity<?> generate(...) {
    ReportDTO resultado = reporteBaseService.generarReporte(...);
    String url = resultado.getData().getUrl(); // ya la guarda UploadSvc
    String fileName = resultado.getName() + "." + tipoReporte;
    long contentLength = resultado.getContent().length;

    // Threshold configurable (ej. 10MB)
    if (url != null && contentLength > 10_000_000) {
        // Redirect 302 al storage público — browser descarga directo
        return ResponseEntity.status(HttpStatus.FOUND)
            .header(HttpHeaders.LOCATION, url)
            .header(HttpHeaders.CONTENT_DISPOSITION, "attachment; filename=" + fileName)
            .build();
    }

    // Archivos pequeños: binary inline (comportamiento actual)
    return ResponseEntity.ok()
        .header(HttpHeaders.CONTENT_DISPOSITION, "inline; filename=" + fileName)
        .contentType(MediaType.parseMediaType(contentType))
        .body(resultado.getContent());
}
```

**Frontend sin cambios** — `window.open(url, '_blank')` sigue al redirect 302 automáticamente.

### Ventajas
- Backend libera memoria rápido (no buffer >threshold)
- Storage sirve static files eficientemente (FTP/HTTP local)
- Reintentos/continuación de descarga nativa del browser
- CDN-ready si migras storage después
- `ReporteEjecucionDTO.url` ya persiste la URL → auditoría completa

### Threshold recomendado
| Tamaño | Estrategia |
|--------|------------|
| < 10MB | Binary inline (actual) |
| 10MB - 100MB | Redirect 302 a storage |
| > 100MB | Considerar chunked upload / streaming JasperReports (`JRVirtualizer`) |

### Testing checklist actualizado

| Escenario | Backend | Frontend |
|-----------|---------|----------|
| PDF < 10MB | ✅ Binary inline | ✅ `window.open` |
| PDF > 10MB | ✅ Redirect 302 | ✅ `window.open` sigue redirect |
| Excel con subreportes | ✅ | ✅ |
| HTML | ✅ | ✅ |
| CSV (SQL nativo) | ✅ | ✅ |
| Error OOM → mensaje amigable | ✅ | ✅ |

## Referencias
- `sdd/specs/domains/report/design.md` — Diseño actualizado con endpoints REST
- `sdd/specs/domains/report/tasks.md` — Tareas AC-REP-20 (migrar servlet)
- `d3brain/src/main/java/d3/report/ReporteServlet.java` — Código actual
- `d3_front/src/app/modules/full/neuron/service/form-report.service.ts` — Cliente actual