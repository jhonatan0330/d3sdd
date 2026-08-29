# Specs — Unidades de consumo (CONSUMPTION-UNITS)

## Requisitos

### Funcionales
- R-CU-001: Saldo de unidades de consumo (`ConsumptionUnitBalanceDTO`).
- R-CU-002: Inicialización del sistema con 1 MB de saldo.
- R-CU-003: Incremento diario automático de 1 MB.
- R-CU-004: Compra de unidades (MB o GB) que se suman al saldo.
- R-CU-005: Descuento horario por tamaño de archivos subidos (`CargaArchivoDTO.size`).
- R-CU-006: Consulta de saldo actual e histórico de movimientos.
- R-CU-007: **Retraso progresivo por saldo negativo** — Cuando el saldo sea negativo, cada petición HTTP se demora N segundos, donde N = valor absoluto de MB negativos (ej: -5 MB → 5 seg de delay). No bloquear al usuario; degradar rendimiento suavemente.

### No funcionales
- NF-CU-001: Multi-tenant (aislamiento por catálogo JDBC).
- NF-CU-002: Transaccionalidad en operaciones de compra y descuento.
- NF-CU-003: Tareas programadas (scheduler) para incremento diario y descuento horario.
- NF-CU-004: El delay por saldo negativo no debe afectar hilos de scheduler ni healthchecks.
- NF-CU-005: Máximo delay configurable (ej. 60 seg) para evitar timeouts de infraestructura.

## Diseño

### Paquetes (ARCH-001)
- `d3.consumption_units`

### Componentes
- `ConsumptionUnitBalanceSvc`
- `ConsumptionUnitMovementSvc`
- `ConsumptionUnitSchedulerSvc` (tareas programadas)
- `ConsumptionUnitInterceptor` / `ConsumptionUnitFilter` (delay por saldo negativo)
- Mappers: `ConsumptionUnitBalanceMapper`, `ConsumptionUnitMovementMapper`

### DTOs
- `ConsumptionUnitBalanceDTO`: saldo actual (en bytes), tenant, estado, fechaActualizacion.
- `ConsumptionUnitMovementDTO`: tipo (INICIAL, INCREMENTO_DIARIO, COMPRA, DESCUENTO_CARGA), cantidad (bytes), referencia, fecha.
- Filtros correspondientes.

### Tablas (sugeridas)
- `consumption_unit_balance_cucb`: `ccuc_llave` (PK), `ccuc_saldo_bytes`, `ccuc_estado`, `ccuc_fecha_actualizacion`, `ccuc_tenant`.
- `consumption_unit_movement_cucm`: `ccum_llave` (PK), `ccum_tipo`, `ccum_cantidad_bytes`, `ccum_referencia`, `ccum_fecha`, `ccum_tenant`, `ccum_balance_llave` (FK).

### Integración con Upload
- `ConsumptionUnitSchedulerSvc` usa `CargaArchivoMapper` para consultar `CargaArchivoDTO` por rango horario (`fechaInicio`/`fechaFin`) y sumar `size`.
- Descuento: `saldo -= suma(size)` de la hora.

### Transacciones
- Compra y descuento: `@Transactional` (rollbackFor = Exception.class).
- Incremento diario: transacción simple.

### Constantes
- `INITIAL_BALANCE_MB = 1`
- `DAILY_INCREMENT_MB = 1`
- Unidades: 1 MB = 1_048_576 bytes, 1 GB = 1_073_741_824 bytes.

### Retraso por saldo negativo (R-CU-007) — Opciones de diseño

#### Opción A: Spring MVC `HandlerInterceptor` (Recomendada)
- Implementar `HandlerInterceptor.preHandle()` que:
  1. Obtiene `TenantContext` actual
  2. Consulta `ConsumptionUnitBalanceSvc.getBalanceBytes()` (caché en request scope)
  3. Si `balanceBytes < 0`: calcula `delaySeconds = min(abs(balanceBytes) / MB, MAX_DELAY_SECONDS)`
  4. `Thread.sleep(delaySeconds * 1000L)`
  5. Retorna `true` para continuar
- Registrar en `WebMvcConfigurer.addInterceptors()` con `excludePathPatterns` para `/health`, `/api/ping`, `/main/checkToken`, `/consumption-units/**` (evitar recursión).
- Ventajas: No invasivo, aplica a todos los endpoints automáticamente, compatible con JWT/token opaco.

#### Opción B: Servlet `Filter` (`OncePerRequestFilter`)
- Similar a A pero a nivel Servlet (antes de Spring MVC).
- Acceso directo a `HttpServletRequest`/`HttpServletResponse`.
- Ventajas: Más temprano, funciona para recursos estáticos, error pages.
- Desventajas: Menos integración con Spring (security context, TenantContext).

#### Opción C: AOP `@Around` en controladores anotados
- `@Around("@annotation(RequiresConsumptionUnits)")` o pointcut en paquetes específicos.
- Ventajas: Control granular por endpoint.
- Desventajas: Requiere anotar controladores, no cubre endpoints nuevos automáticamente.

#### Opción D: `ResponseBodyAdvice` / `RequestBodyAdvice`
- Se ejecuta tras la lógica del controlador (post-procesamiento).
- **No sirve** porque el delay debe ser *antes* de ejecutar la lógica de negocio.

---

#### Detalles de implementación (Opción A - Interceptor)

```java
@Component
@RequiredArgsConstructor
public class ConsumptionUnitDelayInterceptor implements HandlerInterceptor {

    private final ConsumptionUnitBalanceSvc balanceSvc;
    private static final long MB = 1_048_576L;
    private static final int MAX_DELAY_SECONDS = 60;

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) {
        // Excluir paths de salud, auth, y los propios del dominio
        String path = request.getRequestURI();
        if (isExcluded(path)) return true;

        long balanceBytes = balanceSvc.getCurrentBalanceBytes(); // cacheado por request
        if (balanceBytes >= 0) return true;

        long negativeMB = Math.abs(balanceBytes) / MB;
        int delaySeconds = (int) Math.min(negativeMB, MAX_DELAY_SECONDS);
        if (delaySeconds > 0) {
            try { Thread.sleep(delaySeconds * 1000L); }
            catch (InterruptedException e) { Thread.currentThread().interrupt(); }
        }
        return true;
    }

    private boolean isExcluded(String path) {
        return path.startsWith("/health") || path.startsWith("/api/ping") ||
               path.startsWith("/main/checkToken") || path.startsWith("/consumption-units");
    }
}
```

#### Caché de saldo por request
- En `ConsumptionUnitBalanceSvc`: método `getCurrentBalanceBytes()` que usa `RequestContextHolder` o `ThreadLocal` para cachear una sola consulta por request.
- Evita N+1 queries si el interceptor se ejecuta múltiples veces (ej. forward, async).

#### Configuración
```properties
# application.properties
consumption-units.max-delay-seconds=60
consumption-units.enabled=true
```

#### Exclusiones obligatorias
- Healthchecks (`/health`, `/actuator/**`, `/api/ping`, `/api/ok`)
- Autenticación (`/main/autenticarUsuarioAutenticacion`, `/main/googleAuthenticate`, `/main/checkToken`, `/main/solicitarNuevaClave`)
- Endpoints propios del dominio (`/consumption-units/**`)
- API key pública (`/api/login`)

#### Métricas/Observabilidad
- Contador `consumption_units.delay.requests_total` (con label `delay_seconds`)
- Histograma `consumption_units.delay.duration_seconds`
- Log `WARN` cuando delay > 10s (posible abuso o necesidad de compra)
