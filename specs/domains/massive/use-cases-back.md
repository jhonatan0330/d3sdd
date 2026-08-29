# Casos de uso — Carga Masiva (MAS) (BACKEND)

Dominio: `massive`. Paquete `d3.massiveload`. Contrato: §9.

> Capa backend (d3brain). Contratos/endpoints aquí. Los pasos de UI van en
> `use-cases-front.md`.

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
Procesamiento incremental de ítems orquestado por
`MassiveLoadOrchestratorService` / `MassiveCRUDItemService` (sin endpoint
HTTP legacy `sincronizeCargaMasivaItem`; el flujo real es `upload` → `validate` → `execute`).

## CU-MAS-002 — Ejecutar carga masiva
Flujo orquestado por `MassiveController` (ver CU-MAS-006..008): `upload` → `validate` → `execute`.

## CU-MAS-003 / CU-MAS-004
El módulo `d3.massiveload` expone la carga vía `MassiveController` y delega en
`MassiveLoadOrchestratorService`, `MassiveCRUDItemService`, `MassiveCRUDMasterService`,
`MassiveFileParserService`, `MassiveValidationService`, `MassiveDocumentBuilderService`.
DTOs: `MassiveMasterRequest`, `MassiveMasterDTO`, `MassiveItemDTO`, `MasivaItemRequest`,
`MassiveMasterFilter`, `MassiveItemFilter`.

> El único expositor HTTP es `MassiveController` (`massiveload`). No existen
> `MassiveItemController`/`MassiveMasterController`.

---

## CU-MAS-005 — Descargar archivo base (plantilla)

- **Actor:** Usuario autenticado
- **Precondiciones:** usuario con permiso de carga masiva; existe la plantilla (`templateId`).
- **Postcondiciones:** el usuario descarga un archivo base (xlsx/xml/json) para llenar.

### Pasos — Backend
1. El sistema recibe el `id` de la plantilla y el tipo a exportar, y genera un archivo del
   tipo solicitado.
   1.1 Cuando es `xlsx`, la primera fila debe tener los nombres de los campos de la plantilla
   (normalizados).
2. Almacena el archivo con el servicio de upload (`d3.upload`).
3. Devuelve la `url` del archivo.

### Contrato
- Endpoint: `POST massiveload/template` (❑ por crear — backlog MAS-NEW-002)
  request: `{ templateId, format }` · response: `{ url }` (luego servida por `GET /files/...`)
- Auth: `Authorization`.

## CU-MAS-006 — Cargar el archivo

- **Actor:** Usuario autenticado
- **Precondiciones:** el usuario tiene el archivo base lleno.
- **Postcondiciones:** el archivo queda registrado como carga (`loadId`).

### Pasos — Backend
1. `MassiveController.upload` (`POST massiveload/upload`) recibe `MassiveMasterRequest`.
2. `MassiveFileParserService`/`MassiveCRUDMasterService` registran la carga.
3. Responde `MassiveMasterRequest` (con `loadId`).

### Contrato
- Endpoint: `POST massiveload/upload` (`MassiveMasterRequest`) · Auth: `Authorization`.

## CU-MAS-007 — Procesar el archivo

- **Actor:** Usuario autenticado (o scheduler)
- **Precondiciones:** existe una carga registrada (CU-MAS-006, con `loadId`).
- **Postcondiciones:** los ítems del archivo fueron validados y procesados.

### Pasos — Backend
1. `MassiveController.validate` (`POST massiveload/validate/{loadId}`) valida vía
   `MassiveValidationService`.
2. `MassiveController.execute` (`POST massiveload/execute/{loadId}`) procesa vía
   `MassiveLoadOrchestratorService` / `MassiveDocumentBuilderService`.
3. Actualiza estado y responde `MassiveMasterRequest`.

### Contrato
- Endpoints: `POST massiveload/validate/{loadId}` y `POST massiveload/execute/{loadId}`
  (`MassiveMasterRequest`) · Auth: `Authorization`.

## CU-MAS-008 — Consultar historial de cargas

- **Actor:** Usuario autenticado
- **Precondiciones:** existen cargas previas.
- **Postcondiciones:** se lista el historial de cargas y sus ítems.

### Pasos — Backend
1. `MassiveController.getLoad` (`GET massiveload/{loadId}`) devuelve `MassiveMasterRequest`.
2. `MassiveController.getItems` (`GET massiveload/{loadId}/items`) devuelve
   `List<MasivaItemRequest>`.
3. Filtra por tenant/sesión.

### Contrato
- Endpoints: `GET massiveload/{loadId}` y `GET massiveload/{loadId}/items` · Auth: `Authorization`.
