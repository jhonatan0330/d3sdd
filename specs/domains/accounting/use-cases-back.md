# Casos de uso — Contabilidad (ACC) (BACKEND)

> Capa backend (d3brain). Contratos/endpoints aquí. Los pasos de UI (si los hubiera) van en use-cases-front.md.

Dominio: `accounting` (comprobantes y plan contable). Contrato:
[`contract.md`](../../contract.md) §9.

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-ACC-001 | Listar comprobantes por catálogo | Usuario autenticado | ✅ |
| CU-ACC-002 | Ver comprobante | Usuario autenticado | ✅ |
| CU-ACC-003 | Crear comprobante manual | Usuario autenticado | ✅ |
| CU-ACC-004 | Eliminar comprobante manual | Usuario autenticado | ✅ |
| CU-ACC-005 | Generar comprobante desde documento | Sistema | ✅ |
| CU-ACC-006 | Obtener id de comprobante por documento | Sistema | ✅ |
| CU-ACC-007 | Gestionar rangos de comprobante | Usuario autenticado | ✅ |
| CU-ACC-008 | Consultar plan contable (catálogos/cuentas/saldos) | Usuario autenticado | ✅ |
| CU-ACC-009 | Actualizar comprobante manual | Usuario autenticado | ✅ |
| CU-ACC-010 | API externa de comprobantes (envío) | Sistema externo | ✅ |
| CU-ACC-011 | Verificar API externa (ping/ok) | Sistema externo | ✅ |
| CU-ACC-012 | Procesar pila de comprobantes (calcular saldos) | Sistema | ✅ |

---

## CU-ACC-001 — Listar comprobantes por catálogo
`GET acc/voucher/{catalog}` → `List<VoucherDTO>`.

**Pasos:**
1. Validar que `catalogId` no sea nulo
2. Obtener el catálogo por ID
3. Validar que el catálogo exista
4. Crear filtro con catálogo, código del catálogo y estado activo
5. Retornar lista de comprobantes filtrados

## CU-ACC-002 — Ver comprobante
`GET acc/voucher/one/{voucherId}` → `Voucher`.

**Pasos:**
1. Crear objeto Voucher vacío
2. Obtener el encabezado del comprobante por ID
3. Obtener las líneas (registros + auxiliares) del comprobante
4. Retornar el objeto Voucher completo

## CU-ACC-003 — Crear comprobante manual
`POST acc/voucher/manual` con `Voucher` → `SharedIdResponse`.
**Transaccional:** Sí (`@Transactional`)

**Pasos:**
1. Obtener y validar el catálogo (requerido, existente, fecha dentro del período)
2. Validar información del encabezado y registros:
   - Voucher, encabezado y registros no nulos
   - Cada registro debe tener cuenta o valor
   - Cuentas auxiliares deben tener tipo
   - Validar tipo de documento
   - Para tipo comprobante: valor total = suma de positivos, negativos = positivos
3. Configurar y validar cuentas:
   - Cuenta debe existir, pertenecer al catálogo y estar activa
   - Auxiliares deben existir, pertenecer al catálogo y estar activos
4. Generar consecutivo si no existe
5. Guardar el encabezado del comprobante
6. Obtener el comprobante guardado por ID
7. Guardar registros y auxiliares
8. Crear registro en el stack de comprobantes
9. Validar marco temporal
10. Retornar ID y código del comprobante

## CU-ACC-004 — Eliminar comprobante manual
`DELETE acc/voucher/manual/{voucherId}` → `SharedIdResponse`.
**Transaccional:** Sí (`@Transactional`)

**Pasos:**
1. Obtener comprobante por ID
2. Validar que exista
3. Establecer fecha de eliminación
4. Cambiar estado a inactivo
5. Actualizar comprobante
6. Buscar stack del comprobante
7. Si no existe stack, crear uno con acción inactiva
8. Si existe stack, marcarlo como inactivo
9. Retornar ID y código

## CU-ACC-005 — Generar comprobante desde documento
`POST acc/voucher/generate-voucher` con `VoucherPrepareRequest` → `SharedIdResponse`.
**Transaccional:** Sí (`@Transactional`)

**Pasos:**
1. Obtener tipo de comprobante por servicio
2. Verificar que no exista comprobante activo para el documento y tipo
3. Si existe comprobante activo, retornar error
4. Buscar servicio web activo para el documento
5. Si existe servicio web, aplicar programación de ejecución
6. Si no existe servicio, obtener documento
7. Preparar API para ejecución del servicio
8. Retornar resultado

## CU-ACC-006 — Obtener id de comprobante por documento
`POST acc/voucher/document` con `VoucherPrepareRequest` → `SharedIdResponse`.
**Transaccional:** Sí (`@Transactional`)

**Pasos:**
1. Crear filtro de tipo con el servicio del request
2. Obtener el tipo de comprobante
3. Validar que exista el tipo
4. Crear filtro de comprobante con documento, tipo y estado activo
5. Obtener el comprobante
6. Validar que exista el comprobante
7. Retornar ID y código del comprobante

## CU-ACC-007 — Rangos de comprobante
`POST acc/voucher/range-clear-voucher` y `POST acc/voucher/range-create-voucher`
con `VoucherRangeRequest` → gestionan rangos.
**Transaccional:** Sí (`@Transactional`)

**Operación clear (eliminar):**
1. Consultar documentos a eliminar por plantilla y fechas
2. Si no hay documentos, retornar null
3. Para cada documento, eliminar comprobantes asociados e inactivar servicios web

**Operación create (recrear):**
1. Buscar propiedades de API_SERVICE con TEMPLATE_VOUCHER
2. Validar que la plantilla tenga referencia TEMPLATE_VOUCHER
3. Consultar documentos a recrear por plantilla y fechas
4. Si no hay documentos, retornar null
5. Para cada propiedad y documento, programar ejecución
6. Retornar ID de plantilla

## CU-ACC-008 — Plan contable
`GET acc/plan/catalog` (catálogos), `GET acc/plan/catalog/{id}`,
`GET acc/plan/account/{catalog}?filter=`, `GET acc/plan/account/{catalog}/{id}`,
`GET acc/plan/balance/{catalog}` (saldos).

**Consulta de catálogos activos:**
1. Crear filtro con estado activo
2. Retornar lista de catálogos

**Consulta de cuentas activas:**
1. Crear filtro con catálogo, texto de filtro, estado activo, límite 3000
2. Retornar lista de cuentas

**Consulta de balance:**
1. Retornar balance por catálogo usando servicio de mapa de resultados

## CU-ACC-009 — Actualizar comprobante manual
`PUT acc/voucher/manual` con `Voucher` → `SharedIdResponse`.
**Transaccional:** Sí (`@Transactional`)

**Pasos:**
1. Obtener y validar el catálogo (requerido, existente, fecha dentro del período)
2. Validar información del encabezado y registros
3. Configurar y validar cuentas (existencia, pertenencia al catálogo, estado activo)
4. Actualizar el encabezado del comprobante
5. Obtener el comprobante actualizado por ID
6. Guardar registros y auxiliares
7. Crear registro en el stack de comprobantes
8. Retornar ID y código del comprobante

## CU-ACC-010 — API externa de comprobantes (envío)
`POST acc/api/voucher` con `VoucherRequest` (header `x-api-key`) → `SharedIdResponse`.

**Pasos:**
1. Validar el item:
   - Catálogo y concepto requeridos
   - Catálogo debe existir y estar activo
   - Líneas requeridas
   - Tipo de documento válido
   - Cada línea debe tener cuenta operativa (no auxiliar ni grupo)
2. Crear encabezado de comprobante con datos del request
3. Para cada línea del request:
   - Crear registro de cuenta con débito/crédito
   - Procesar referencias/auxiliares
   - Buscar cuenta auxiliar por documento
   - Si no existe, crear cuenta auxiliar
   - Crear registro auxiliar
4. Llamar a VoucherCreateService para crear el comprobante
5. Retornar ID y código del comprobante

## CU-ACC-011 — Verificar API externa (ping/ok)
`GET acc/api/ok` con `x-api-key` → `"OK"`.
`GET acc/api/ping` → `"PING ******* ACUMULADOR (N) *** fecha"`.

**Verificación ok:**
1. Validar que la API key sea válida
2. Retornar "OK"

**Ping acumulador:**
1. Procesar comprobantes en cola (llamar a StackAccountProccessService)
2. Retornar cantidad procesada y fecha

## CU-ACC-012 — Procesar pila de comprobantes (calcular saldos)
Procesamiento interno (`StackAccountProccessService.call()`).
**Transaccional:** Sí (`@Transactional` en VoucherCalculateService)

**Pasos:**
1. Obtener stack de comprobantes disponibles (`stackAvailable`)
2. Si no hay comprobantes, retornar "0"
3. Marcar todos como completados con fecha de ejecución
4. Actualizar cada stack
5. Para cada comprobante, ejecutar `VoucherCalculateService.call()`:
   - Obtener comprobante completo con líneas
   - Para cada línea del comprobante:
     - Guardar mapa para cuenta principal
     - Si tiene auxiliares, guardar mapa para cada auxiliar
   - Para cada mapa acumulado:
     - Obtener timeframe
     - Calcular factor (1 o -1 para anulación)
     - Actualizar positivos/negativos según operación de cuenta (PLUS/MINUS)
     - Actualizar valor, balance siguiente, cantidad
     - Si la cuenta tiene padre (grupo), recursar con el padre
6. Establecer fecha de finalización
7. Actualizar stack
8. Retornar cantidad procesada

