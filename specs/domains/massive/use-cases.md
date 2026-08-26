# Casos de uso — Carga Masiva (MAS)

Dominio: `massive`. Paquete `d3.massiveload`. Contrato: §9.

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

---

## CU-MAS-001 — Sincronizar ítem
`POST massiveload/sincronizeCargaMasivaItem` (legacy) / orquestado por
`MassiveLoadOrchestratorService`.

## CU-MAS-002 — Ejecutar carga masiva
Flujo orquestado por `MassiveRest` (ver CU-MAS-006..008): `upload` → `validate` → `execute`.

## CU-MAS-003 / CU-MAS-004
El módulo `d3.massiveload` expone la carga vía `MassiveRest` y delega en
`MassiveLoadOrchestratorService`, `MassiveCRUDItemService`, `MassiveCRUDMasterService`,
`MassiveFileParserService`, `MassiveValidationService`, `MassiveDocumentBuilderService`.
DTOs: `MassiveMasterRequest`, `MassiveMasterDTO`, `MassiveItemDTO`, `MasivaItemRequest`,
`MassiveMasterFilter`, `MassiveItemFilter`.

> Los controladores `MassiveItemController`/`MassiveMasterController` ya no existen; el
> único expositor HTTP es `MassiveRest` (`massiveload`).

---

## CU-MAS-005 — Descargar archivo base (plantilla)

- **Actor:** Usuario autenticado
- **Precondiciones:** usuario con permiso de carga masiva.
- **Postcondiciones:** el usuario obtiene el archivo base para llenar.

### Pasos — Frontend
1. El usuario hace clic en "Descargar plantilla".
2. La SPA descarga el archivo base.
3. El navegador guarda la plantilla.

### Pasos — Backend
1. **Endpoint por crear** (backlog MAS-NEW-002): entregar la plantilla
   (p.ej. `GET massiveload/template` o vía `/files`).
2. Devuelve el archivo (`application/octet-stream`).

### Contrato
- Endpoint: `GET massiveload/template` (❑ por crear) · Auth: `Authorization`.

## CU-MAS-006 — Cargar el archivo

- **Actor:** Usuario autenticado
- **Precondiciones:** el usuario tiene el archivo base lleno.
- **Postcondiciones:** el archivo queda registrado como carga (`loadId`).

### Pasos — Frontend
1. El usuario selecciona el archivo y hace clic en "Cargar".
2. La SPA envía el archivo/referencia al backend.
3. Muestra confirmación y el `loadId` resultante.

### Pasos — Backend
1. `MassiveRest.upload` (`POST massiveload/upload`) recibe `MassiveMasterRequest`.
2. `MassiveFileParserService`/`MassiveCRUDMasterService` registran la carga.
3. Responde `MassiveMasterRequest` (con `loadId`).

### Contrato
- Endpoint: `POST massiveload/upload` (`MassiveMasterRequest`) · Auth: `Authorization`.

## CU-MAS-007 — Procesar el archivo

- **Actor:** Usuario autenticado (o scheduler)
- **Precondiciones:** existe una carga registrada (CU-MAS-006, con `loadId`).
- **Postcondiciones:** los ítems del archivo fueron validados y procesados.

### Pasos — Frontend
1. El usuario hace clic en "Procesar" sobre la carga.
2. La SPA invoca validación y luego ejecución por `loadId`.
3. Muestra estado/resultado del procesamiento.

### Pasos — Backend
1. `MassiveRest.validate` (`POST massiveload/validate/{loadId}`) valida vía
   `MassiveValidationService`.
2. `MassiveRest.execute` (`POST massiveload/execute/{loadId}`) procesa vía
   `MassiveLoadOrchestratorService` / `MassiveDocumentBuilderService`.
3. Actualiza estado y responde `MassiveMasterRequest`.

### Contrato
- Endpoints: `POST massiveload/validate/{loadId}` y `POST massiveload/execute/{loadId}`
  (`MassiveMasterRequest`) · Auth: `Authorization`.

## CU-MAS-008 — Consultar historial de cargas

- **Actor:** Usuario autenticado
- **Precondiciones:** existen cargas previas.
- **Postcondiciones:** se lista el historial de cargas y sus ítems.

### Pasos — Frontend
1. El usuario abre la vista "Historial de cargas".
2. La SPA consulta la carga por `loadId`.
3. Renderiza detalle y tabla de ítems.

### Pasos — Backend
1. `MassiveRest.getLoad` (`GET massiveload/{loadId}`) devuelve `MassiveMasterRequest`.
2. `MassiveRest.getItems` (`GET massiveload/{loadId}/items`) devuelve
   `List<MasivaItemRequest>`.
3. Filtra por tenant/sesión.

### Contrato
- Endpoints: `GET massiveload/{loadId}` y `GET massiveload/{loadId}/items` · Auth: `Authorization`.
