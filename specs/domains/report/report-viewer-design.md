# Diseño: Visor de Reportes Multi-Tenant

## Requisitos

1. **Visualización in-browser** (no solo descarga) — PDF/HTML embebido
2. **Multi-tenancy** — Cada request resuelve tenant via `X-Tenant-ID` header
3. **Seguridad** — Solo reportes propios del tenant (aislamiento datos)
4. **Performance** — Streaming para archivos grandes, cache de vistas
5. **Historial** — Lista de reportes generados con preview

---

## Arquitectura Multi-Tenant en Reportes

### Flujo actual (generación)
```
Request → TenantFilter (X-Tenant-ID) → TenantContext.set()
    ↓
ReporteBaseSvc.generarReporte() → DataSource del tenant (TenantRoutingDataSource)
    ↓
JasperReports usa conexión del tenant → datos aislados
```

### Para el visor: mismo patrón
El `TenantFilter` ya intercepta **todas** las requests (salvo `/static/`, `/error`). El visor solo debe:
- Enviar `X-Tenant-ID` header en cada llamada
- El backend ya usa `TenantContext.getCurrentTenant()` para routing DataSource

---

## Endpoints propuestos

| Método | Ruta | Descripción | Tenant |
|--------|------|-------------|--------|
| GET | `/api/reports/{executionId}/view` | Vista HTML/PDF embebido | ✅ Header |
| GET | `/api/reports/{executionId}/content` | Binary raw (para `<embed>`/`<iframe>`) | ✅ Header |
| GET | `/api/reports/history` | Lista paginada ejecuciones del tenant | ✅ Header |
| GET | `/api/reports/{executionId}/metadata` | Info reporte (nombre, fecha, tamaño, tipo) | ✅ Header |
| DELETE | `/api/reports/{executionId}` | Borrar ejecución + archivo storage | ✅ Header |

### Parámetros de vista
- `format=html|pdf|auto` — Forzar formato vista
- `page=N` — Página inicial (PDF.js)
- `zoom=page-width|page-fit|N` — Zoom inicial

---

## Componentes Backend

### 1. `ReportViewerController` (`d3.report.infrastructure`)

```java
@RestController
@RequestMapping("/api/reports")
@RequiredArgsConstructor(onConstructor_ = @Lazy)
public class ReportViewerController {

    private final ReporteEjecucionSvc ejecucionService;
    private final ReporteBaseSvc reporteBaseService;
    private final UploadSvc uploadService; // para signed URLs si storage privado

    @GetMapping("/{executionId}/view")
    public ResponseEntity<Resource> view(
            @PathVariable String executionId,
            @RequestParam(required = false) String format, // html, pdf
            HttpServletRequest request) {

        // 1. Tenant ya resuelto por TenantFilter → TenantContext
        // 2. Buscar ejecución (filtra implícitamente por tenant via DataSource)
        ReporteEjecucionDTO ejecucion = ejecucionService.consultaXId(executionId);
        if (ejecucion == null) return ResponseEntity.notFound().build();

        // 3. Validar pertenencia a tenant actual (defensa en profundidad)
        if (!ejecucion.getTenantId().equals(TenantContext.getCurrentTenant())) {
            return ResponseEntity.status(HttpStatus.FORBIDDEN).build();
        }

        // 4. Obtener contenido
        byte[] content = getContent(ejecucion); // de storage o regenerar

        // 5. Determinar MediaType
        MediaType mediaType = resolveMediaType(ejecucion, format);

        // 6. Streaming Response (evita cargar todo en memoria)
        return ResponseEntity.ok()
            .header(HttpHeaders.CONTENT_DISPOSITION, "inline; filename=" + ejecucion.getUrl())
            .contentType(mediaType)
            .body(new ByteArrayResource(content)); // o InputStreamResource para streaming
    }

    @GetMapping("/history")
    public Page<ReporteEjecucionDTO> history(
            @RequestParam(defaultValue = "0") int page,
            @RequestParam(defaultValue = "20") int size,
            @RequestParam(required = false) String reporte,
            @RequestParam(required = false) String documento,
            @RequestParam(required = false) String estado) {
        // TenantFilter ya establece contexto
        // ReporterEjecucionMapper usa DataSource del tenant → aislamiento automático
    }
}
```

### 2. `ReporteEjecucionDTO` — Añadir `tenantId`

```java
// En domain/ReporteEjecucionDTO.java
private String tenantId; // se setea al guardar desde TenantContext.getCurrentTenant()
```

### 3. `ReporteEjecucionMapper` — Filtrado por tenant automático

El `TenantRoutingDataSource` ya enruta al schema/BD correcto. Los queries MyBatis **no necesitan `WHERE tenant_id = ?`** si cada tenant tiene su BD/schema separado.

> **Verificar**: ¿Es multi-schema (un BD, schemas por tenant) o multi-database (BD por tenant)?
> - Si multi-schema: queries necesitan `AND tenant_id = ?` o vistas
> - Si multi-database: routing DataSource aísla completamente

---

## Componentes Frontend

### 1. `ReportViewerService` (`d3_front/src/app/shared/services/report-viewer.service.ts`)

```typescript
@Injectable({ providedIn: 'root' })
export class ReportViewerService {
  private http = inject(HttpClient);
  private env = inject(Environment);

  // Header X-Tenant-ID se añade via interceptor global (ver abajo)
  
  view(executionId: string, format?: 'html' | 'pdf'): Observable<Blob> {
    return this.http.get(`${this.apiUrl}/reports/${executionId}/view`, {
      params: { format },
      responseType: 'blob',
      headers: new HttpHeaders().set('Accept', 'application/pdf, text/html')
    });
  }

  history(params: ReportHistoryParams): Observable<Page<ReporteEjecucionDTO>> {
    return this.http.get<Page<ReporteEjecucionDTO>>(`${this.apiUrl}/reports/history`, { params });
  }

  metadata(executionId: string): Observable<ReportMetadataDTO> {
    return this.http.get<ReportMetadataDTO>(`${this.apiUrl}/reports/${executionId}/metadata`);
  }
}
```

### 2. Interceptor global para `X-Tenant-ID`

```typescript
// d3_front/src/app/core/interceptors/tenant.interceptor.ts
@Injectable()
export class TenantInterceptor implements HttpInterceptor {
  constructor(private auth: AuthService) {} // o TenantService

  intercept(req: HttpRequest<unknown>, next: HttpHandler): Observable<HttpEvent<unknown>> {
    const tenantId = this.auth.getCurrentTenantId(); // de JWT claim o estado app
    if (!tenantId) return next.handle(req); // o error

    const cloned = req.clone({
      setHeaders: { 'X-Tenant-ID': tenantId }
    });
    return next.handle(cloned);
  }
}

// En app.config.ts
provideHttpClient(withInterceptors([tenantInterceptor]))
```

### 3. Componente `ReportViewerComponent` (standalone)

```typescript
// d3_front/src/app/shared/components/report-viewer/report-viewer.component.ts
@Component({
  selector: 'app-report-viewer',
  standalone: true,
  imports: [CommonModule],
  template: `
    @if (loading()) {
      <div class="loading-bar"><div class="progress"></div></div>
    }
    @if (error()) {
      <div class="alert alert-error">{{ error() }}</div>
    }
    @if (blobUrl()) {
      @if (isPdf()) {
        <pdf-viewer [src]="blobUrl()" [page]="page()" [zoom]="zoom()" />
      } @else {
        <div class="html-viewer" [innerHTML]="htmlContent()"></div>
      }
    }
  `,
  styles: [`
    :host { display: block; height: 100%; }
    pdf-viewer { height: 100%; width: 100%; }
    .html-viewer { height: 100%; overflow: auto; padding: 1rem; }
  `]
})
export class ReportViewerComponent {
  executionId = input.required<string>();
  format = input<'html' | 'pdf' | 'auto'>('auto');
  
  loading = signal(true);
  error = signal<string | null>(null);
  blobUrl = signal<string | null>(null);
  isPdf = signal(true);
  page = signal(1);
  zoom = signal('page-width');
  htmlContent = signal<SafeHtml>('');

  private viewerSvc = inject(ReportViewerService);
  private sanitizer = inject(DomSanitizer);

  ngOnInit() {
    this.load();
  }

  private load() {
    this.viewerSvc.view(this.executionId(), this.format()).subscribe({
      next: (blob) => {
        const type = blob.type;
        this.isPdf.set(type === 'application/pdf');
        const url = URL.createObjectURL(blob);
        this.blobUrl.set(url);
        if (!this.isPdf()) {
          const reader = new FileReader();
          reader.onload = () => this.htmlContent.set(this.sanitizer.bypassSecurityTrustHtml(reader.result as string));
          reader.readAsText(blob);
        }
        this.loading.set(false);
      },
      error: (err) => {
        this.error.set(err.error?.message || 'Error cargando reporte');
        this.loading.set(false);
      }
    });
  }

  ngOnDestroy() {
    if (this.blobUrl()) URL.revokeObjectURL(this.blobUrl()!);
  }
}
```

### 4. PDF Viewer — Usar `ng2-pdf-viewer` o `pdfjs-dist` directo

```bash
npm i ng2-pdf-viewer pdfjs-dist
```

```typescript
// En componente o wrapper
import { PdfViewerComponent } from 'ng2-pdf-viewer';
// <pdf-viewer [src]="blobUrl" [page]="page" [zoom]="zoom" />
```

---

## Seguridad Multi-Tenant

### Backend (defensa en profundidad)

```java
// En ReportViewerController.view()
ReporteEjecucionDTO ejecucion = ejecucionService.consultaXId(executionId);

// Validación explícita (además de routing DataSource)
String currentTenant = TenantContext.getCurrentTenant();
if (ejecucion == null || !currentTenant.equals(ejecucion.getTenantId())) {
    return ResponseEntity.status(HttpStatus.FORBIDDEN).build();
}
```

### Frontend

- TenantId viene del JWT (`tenant` claim) o contexto de app
- Interceptor añade header automáticamente
- Usuario no puede cambiar tenant en request (header ignorado si no coincide con sesión)

---

## Streaming para archivos grandes

```java
@GetMapping("/{executionId}/content")
public ResponseEntity<StreamingResponseBody> streamContent(@PathVariable String executionId) {
    ReporteEjecucionDTO ejecucion = ejecucionService.consultaXId(executionId);
    String storageUrl = ejecucion.getUrl(); // URL en storage (FTP/local público)

    // Option A: Proxy streaming desde storage (si storage no público)
    StreamingResponseBody stream = outputStream -> {
        try (InputStream in = downloadFromStorage(storageUrl)) {
            in.transferTo(outputStream);
        }
    };

    // Option B: Redirect 302 (si storage público) — ver migration-servlet-to-rest.md
    // return ResponseEntity.status(HttpStatus.FOUND).location(URI.create(storageUrl)).build();

    return ResponseEntity.ok()
        .header(HttpHeaders.CONTENT_DISPOSITION, "inline; filename=" + ejecucion.getUrl())
        .contentType(MediaType.APPLICATION_PDF)
        .body(stream);
}
```

---

## Cache de vistas

### Backend: `ReportViewCache`

```java
@Service
public class ReportViewCache {
    private final Cache<String, byte[]> cache = Caffeine.newBuilder()
        .maximumSize(500)
        .expireAfterWrite(15, TimeUnit.MINUTES)
        .build();

    public byte[] getOrGenerate(String executionId, Supplier<byte[]> generator) {
        return cache.get(executionId, k -> generator.get());
    }
}
```

Key: `executionId` (ya incluye tenant via DataSource routing).

### Frontend: Cache de blobs en memoria + `sessionStorage` para metadatos

---

## Integración con historial existente

`ReporteEjecucionSvc.listarConsulta(filtro)` ya usa `TenantRoutingDataSource` → **lista solo ejecuciones del tenant actual**.

Frontend: `ReportHistoryComponent` usa `ReportViewerService.history()` → tabla con botón "Ver" que abre modal con `<app-report-viewer>`.

---

## Migración gradual

| Fase | Backend | Frontend |
|------|---------|----------|
| 1 | `ReportViewerController` + `tenantId` en DTO | `ReportViewerService` + interceptor |
| 2 | Streaming + cache | `ReportViewerComponent` (PDF.js) |
| 3 | Historial paginado + filtros | `ReportHistoryComponent` + modal viewer |
| 4 | Signed URLs si storage privado | Preview en lista (thumbnail primera página) |

---

## Referencias

- `d3brain/src/main/java/d3/multitenancy/` — TenantContext, TenantFilter, TenantRoutingDataSource
- `d3brain/src/main/java/d3/report/application/ReporteEjecucionSvc.java` — Historial ejecuciones
- `migration-servlet-to-rest.md` — Redirect 302 para archivos grandes
- `design.md` — Componentes actuales