# Casos de uso — Unidades de consumo (CONSUMPTION-UNITS) (BACKEND)

> Capa backend (d3brain). Contratos/endpoints aquí. Los pasos de UI (si los hubiera) van en use-cases-front.md.

## Actores
- **Sistema**: Procesos automáticos (inicialización, incremento diario, descuento horario).
- **Administrador**: Compra de unidades, consulta de saldo.

## Casos de uso

### UC-CU-001: Inicialización del saldo
- **Actor**: Sistema
- **Descripción**: Al crear el sistema/tenant, se crea un registro de saldo con 1 MB inicial.
- **Precondición**: No existe registro de saldo para el tenant.
- **Postcondición**: Saldo = 1 MB, estado = ACTIVO.

### UC-CU-002: Incremento diario
- **Actor**: Sistema (scheduler diario)
- **Descripción**: Cada día a medianoche, se suma 1 MB al saldo actual.
- **Precondición**: Existe saldo activo.
- **Postcondición**: Saldo incrementado en 1 MB, registro de movimiento tipo INCREMENTO_DIARIO.

### UC-CU-003: Compra de unidades
- **Actor**: Administrador
- **Descripción**: El admin compra MB o GB que se suman al saldo.
- **Entrada**: Cantidad, unidad (MB/GB), referencia de compra.
- **Postcondición**: Saldo incrementado, registro de movimiento tipo COMPRA.

### UC-CU-004: Descuento horario por cargas
- **Actor**: Sistema (scheduler horario)
- **Descripción**: Cada hora, se suman los tamaños (`size`) de `CargaArchivoDTO` de esa hora y se restan del saldo.
- **Precondición**: Existe saldo activo, hay cargas en la hora.
- **Postcondición**: Saldo decrementado, registro de movimiento tipo DESCUENTO_CARGA por cada archivo o consolidado.

### UC-CU-005: Consulta de saldo y movimientos
- **Actor**: Administrador
- **Descripción**: Consultar saldo actual y histórico de movimientos (incrementos, compras, descuentos).
- **Salida**: `ConsumptionUnitBalanceDTO` + lista de `ConsumptionUnitMovementDTO`.

### UC-CU-006: Retraso progresivo por saldo negativo
- **Actor**: Sistema (interceptor HTTP)
- **Descripción**: Antes de procesar cualquier petición HTTP (salvo exclusiones), se consulta el saldo actual. Si es negativo, se calcula `delaySeconds = min(|saldo| en MB, MAX_DELAY)` y se bloquea el hilo esa cantidad de segundos.
- **Precondición**: Tenant con saldo negativo (< 0 bytes).
- **Postcondición**: La petición continúa tras el delay; respuesta HTTP incluye header `X-Consumption-Delay-Seconds` informativo.
- **Exclusiones**: Healthchecks, endpoints de auth, endpoints del propio dominio, login público API.
- **Límite**: `MAX_DELAY_SECONDS` configurable (default 60) para evitar timeouts de infraestructura (load balancer, gateway, browser).
