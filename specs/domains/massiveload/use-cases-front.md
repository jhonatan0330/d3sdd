# Casos de uso — Carga Masiva (MAS) (FRONTEND)

Dominio: `massiveload`. Paquete `d3.massiveload`. Contrato: §9.

> Capa frontend (d3_front). Solo pasos de UI/componentes. Los contratos/endpoints
> van en `use-cases-back.md`.

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-MAS-001 | Sincronizar ítem de carga masiva | Sistema/Scheduler | ✅ |
| CU-MAS-002 | Ejecutar carga masiva | Usuario autenticado | ✅ |
| CU-MAS-003 | Gestionar ítems de carga | Usuario autenticado | ✅ |
| CU-MAS-004 | Gestionar maestros de carga | Usuario autenticado | ✅ |
| CU-MAS-005 | Descargar archivo base (plantilla) | Usuario autenticado | 🔧 (endpoint por crear) |
| CU-MAS-006 | Cargar el archivo | Usuario autenticado | ✅ |
| CU-MAS-007 | Procesar el archivo | Usuario autenticado | ✅ |
| CU-MAS-008 | Consultar historial de cargas | Usuario autenticado | ✅ |

> CU-MAS-001..004 no tienen pasos de UI documentados de forma separada; el flujo de
> interfaz se deriva de CU-MAS-006..008.

---

## CU-MAS-005 — Descargar archivo base (plantilla)

- **Actor:** Usuario autenticado
- **Precondiciones:** usuario con permiso de carga masiva; existe la plantilla (`templateId`).
- **Postcondiciones:** el usuario descarga un archivo base (xlsx/xml/json) para llenar.

### Pasos — Frontend
1. Junto al botón "Descargar plantilla" hay un dropdown con el formato a descargar:
   `xlsx` (por defecto), `xml`, `json`.
2. El usuario escoge el formato y presiona el botón.
3. Al recibir la respuesta del servidor, se abre/descarga la URL del archivo para que el
   usuario lo modifique.

## CU-MAS-006 — Cargar el archivo

- **Actor:** Usuario autenticado
- **Precondiciones:** el usuario tiene el archivo base lleno.
- **Postcondiciones:** el archivo queda registrado como carga (`loadId`).

### Pasos — Frontend
1. El usuario selecciona el archivo y hace clic en "Cargar".
2. La SPA envía el archivo/referencia al backend.
3. Muestra confirmación y el `loadId` resultante.

## CU-MAS-007 — Procesar el archivo

- **Actor:** Usuario autenticado (o scheduler)
- **Precondiciones:** existe una carga registrada (CU-MAS-006, con `loadId`).
- **Postcondiciones:** los ítems del archivo fueron validados y procesados.

### Pasos — Frontend
1. El usuario hace clic en "Procesar" sobre la carga.
2. La SPA invoca validación y luego ejecución por `loadId`.
3. Muestra estado/resultado del procesamiento.

## CU-MAS-008 — Consultar historial de cargas

- **Actor:** Usuario autenticado
- **Precondiciones:** existen cargas previas.
- **Postcondiciones:** se lista el historial de cargas y sus ítems.

### Pasos — Frontend
1. El usuario abre la vista "Historial de cargas".
2. La SPA consulta la carga por `loadId`.
3. Renderiza detalle y tabla de ítems.
