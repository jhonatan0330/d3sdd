# Specs — Carga Masiva (MAS)

## Requisitos

### Funcionales
- R-MAS-001: Procesar ítems de carga masiva de forma incremental (vía
  `MassiveLoadOrchestratorService`/`MassiveCRUDItemService`).
- R-MAS-002: Ejecutar una carga completa desde un archivo (`upload` → `validate` → `execute`).
- R-MAS-003: Administrar ítems y maestros de carga (CRUD vía `MassiveController`
  sobre `MassiveCRUDItemService`/`MassiveCRUDMasterService`).

### No funcionales
- NF-MAS-001: Autenticación por `Authorization` (token de sesión).
- NF-MAS-002: Procesamiento idempotente por `itemId` para reintentos de scheduler.

## Diseño

### Paquetes (realizado según ARCH-001)
- `d3.massiveload`

### Componentes
- `MassiveController` (`massiveload`): único expositor HTTP. Endpoints
  `upload`, `validate/{loadId}`, `execute/{loadId}`, `GET {loadId}`,
  `GET {loadId}/items`. Delega en `MassiveLoadOrchestratorService`.
- `application`:
  - `MassiveLoadOrchestratorService`: orquesta upload/validate/execute/getLoad/getItems.
  - `MassiveCRUDItemService`, `MassiveCRUDMasterService`: CRUD de ítems y maestros.
  - `MassiveFileParserService`: parseo del archivo de carga.
  - `MassiveValidationService`: validación de ítems.
  - `MassiveDocumentBuilderService`: construcción de documentos/expedientes.
- `domain`: `MassiveMasterRequest`, `MassiveMasterDTO`, `MassiveItemDTO`,
  `MasivaItemRequest`, `MassiveMasterFilter`, `MassiveItemFilter`.
- `infrastructure`: `MassiveItemMapper`, `MassiveMasterMapper` (MyBatis).

### Notas
- No existen `MassiveItemController`/`MassiveMasterController`/separados por ruta;
  todo el HTTP pasa por `MassiveController` (`massiveload`).
- No existe método `sincronizeCargaMasiva` con dos `@RequestBody`; el flujo real es
  `upload` (MultipartFile + template) → `validate` → `execute`.
