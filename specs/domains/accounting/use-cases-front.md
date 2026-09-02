# Casos de uso — Contabilidad (ACC) (FRONTEND)

> Capa frontend (d3_front). Pasos de UI aquí. Los contratos/endpoints van en use-cases-back.md.

Dominio: `accounting` (comprobantes y plan contable). Componentes:
- `AccountComponent` (`accounting-view/`): vista principal con catálogos, cuentas, balance y comprobantes.
- `ManualFormComponent` (`manual-form/`): formulario para crear/editar comprobantes manuales.

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| F-ACC-001 | Seleccionar catálogo contable | Usuario | ✅ |
| F-ACC-002 | Ver árbol de cuentas del catálogo | Usuario | ✅ |
| F-ACC-003 | Ver balance de cuentas (saldos) | Usuario | ✅ |
| F-ACC-004 | Listar comprobantes recientes | Usuario | ✅ |
| F-ACC-005 | Crear comprobante manual | Usuario | ✅ |
| F-ACC-006 | Editar comprobante manual | Usuario | ✅ |
| F-ACC-007 | Eliminar comprobante manual | Usuario | ✅ |
| F-ACC-008 | Verificar balance débito/crédito | Usuario | ✅ |
| F-ACC-009 | Agregar líneas de asiento contable | Usuario | ✅ |
| F-ACC-010 | Agregar auxiliares a líneas | Usuario | ✅ |
| F-ACC-011 | Filtrar cuentas operativas | Usuario | ✅ |
| F-ACC-012 | Imprimir comprobante (reporte) | Usuario | ✅ |

---

## F-ACC-001 — Seleccionar catálogo contable
1. `AccountComponent.ngOnInit()` carga catálogos vía `GET acc/plan/catalog`.
2. Si solo hay un catálogo, se selecciona automáticamente.
3. Al seleccionar catálogo (`selectCatalog()`):
   - Cierra drawer (en móvil).
   - Carga cuentas vía `GET acc/plan/account/{catalog}`.
   - Carga balance vía `GET acc/plan/balance/{catalog}`.
   - Carga comprobantes vía `GET acc/voucher/{catalog}`.

## F-ACC-002 — Ver árbol de cuentas del catálogo
1. `getAccounts()` obtiene lista plana de cuentas.
2. Construye árbol jerárquico buscando nodos padre (`searchParentNode()`).
3. Renderiza en `MatTreeFlatDataSource` con `FlatTreeControl`.
4. Columnas: Código (con expand/collapse), Nombre, Status (PLANNING/OPERATING).

## F-ACC-003 — Ver balance de cuentas (saldos)
1. `getBalance()` obtiene `ResultMapDTO[]` vía `GET acc/plan/balance/{catalog}`.
2. Renderiza en tarjetas con: nombre cuenta, periodo, valor actual, tendencia.
3. Incluye dropdown para filtrar por unidad de tiempo (minutos/horas/días/meses/años).

## F-ACC-004 — Listar comprobantes recientes
1. `getVouchers()` obtiene `ManualDTO[]` vía `GET acc/voucher/{catalog}`.
2. Renderiza en `mat-table` con columnas: ID, Fecha, Nota, Valor, Estado, Acciones.
3. Estado se muestra como badge: "Procesado" (A) o "Pendiente" (I).

## F-ACC-005 — Crear comprobante manual
1. `openManualForm()` abre `ManualFormComponent` como diálogo modal.
2. Formulario con header (catálogo, tipo, fecha, concepto) y array de records.
3. Al enviar (`send()`):
   - Valida que crédito = débito.
   - Valida que concepto y fecha estén presentes.
   - Llama `POST acc/voucher/manual` con `Voucher` (header + records).
   - Cierra diálogo y recarga balance/comprobantes.

## F-ACC-006 — Editar comprobante manual
1. `editVouchers()` abre `ManualFormComponent` con `data.key` del comprobante.
2. Carga comprobante vía `GET acc/voucher/one/{voucherId}`.
3. Rellena formulario con datos existentes (header + records + auxiliares).
4. Al enviar: `PUT acc/voucher/manual` con `Voucher` actualizado.

## F-ACC-007 — Eliminar comprobante manual
1. `deleteVouchers()` muestra SweetAlert de confirmación.
2. Al confirmar: `DELETE acc/voucher/manual/{voucherId}`.
3. Recarga lista de comprobantes.

## F-ACC-008 — Verificar balance débito/crédito
1. En `ManualFormComponent`, cada línea tiene controles `positive` (débito) y `negative` (crédito).
2. Al modificar valores, se actualizan acumuladores `debitValue` y `creditValue`.
3. `differenceValue = debitValue - creditValue` se muestra en tiempo real.
4. Si `differenceValue ≠ 0`, se muestra advertencia visual.
5. Validación en `send()`: si crédito ≠ débito, muestra notificación y aborta.

## F-ACC-009 — Agregar líneas de asiento contable
1. `recordsArray` es `FormArray` en el formulario.
2. Al cambiar valores de una línea (con debounce 1s), se agrega nueva línea vacía automáticamente.
3. Cada línea tiene: cuenta (autocomplete), débito, crédito, notas.

## F-ACC-010 — Agregar auxiliares a líneas
1. Checkbox "Auxiliares" activa sección de auxiliares por línea.
2. Cada auxiliar tiene: tipo, código, nombre.
3. Se guarda como `ManualAccountAuxiliarDTO[]` en `references` de la línea.

## F-ACC-011 — Filtrar cuentas operativas
1. `filterAccount()` filtra cuentas donde `type === 'O'` (operativas).
2. Autocomplete permite buscar por código o nombre.
3. Solo muestra cuentas del catálogo seleccionado.

## F-ACC-012 — Imprimir comprobante (reporte)
1. `getReports()` obtiene reportes del template del catálogo.
2. `printReport()` itera reportes y llama `showReport()`.
3. `FormReportService.openReport()` genera vista previa/imprimir.
