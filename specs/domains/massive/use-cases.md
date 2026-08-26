# Casos de uso — Carga Masiva (MAS)

Dominio: `massive`. Contrato: §10.

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-MAS-001 | Sincronizar ítem de carga masiva | Sistema/Scheduler | ✅ |
| CU-MAS-002 | Ejecutar carga masiva desde archivo | Usuario autenticado | ✅ |
| CU-MAS-003 | Gestión de ítems de carga | Usuario autenticado | ⏳ |
| CU-MAS-004 | Gestión de maestros de carga | Usuario autenticado | ⏳ |

---

## CU-MAS-001 — Sincronizar ítem
`POST massiveload/sincronizeCargaMasivaItem` con `itemId` (String) → inicia/procesa un ítem.

## CU-MAS-002 — Ejecutar carga masiva
`POST massiveload/sincronizeCargaMasiva` con `fileUrl` y `template` (Strings) → procesa archivo.

## CU-MAS-003 / CU-MAS-004
Controladores `MassiveItemController` (`massiveload/cargaMasivaItem`) y
`MassiveMasterController` (`massiveload/cargaMasiva`) exponen CRUD de ítems/maestros.
Detalle pendiente de lectura de código.

---

> Los siguientes 4 casos (CU-MAS-005..008) son el flujo de negocio de carga masiva.
> Los pasos back/front son **borrador derivado de los endpoints de `d3.massiveload`**;
> reemplázalos por tus pasos reales.

## CU-MAS-005 — Descargar archivo base (plantilla)

- **Actor:** Usuario autenticado
- **Precondiciones:** usuario con permiso de carga masiva.
- **Postcondiciones:** el usuario obtiene el archivo base para llenar.

### Pasos — Frontend
1. El usuario hace clic en "Descargar plantilla".
2. La SPA descarga el archivo ( `GET` al endpoint de plantilla).
3. El navegador guarda el archivo base.

### Pasos — Backend
1. Expone el archivo base (plantilla) — **endpoint por confirmar**
   (probable `GET massiveload/template` o vía `/files`; validar con `MassiveItemController`/`MassiveMasterController`).
2. Devuelve el archivo (`application/octet-stream`).

### Contrato
- Endpoint: `GET massiveload/template` (❑ por confirmar) · Auth: `Authorization`.

## CU-MAS-006 — Cargar el archivo

- **Actor:** Usuario autenticado
- **Precondiciones:** el usuario ya tiene el archivo base lleno.
- **Postcondiciones:** el archivo queda registrado para procesamiento.

### Pasos — Frontend
1. El usuario selecciona el archivo y hace clic en "Cargar".
2. La SPA envía el archivo/referencia al backend (`fileUrl` + `template`).
3. Muestra confirmación o errores de carga.

### Pasos — Backend
1. `MassiveRest.sincronizeCargaMasiva(fileUrl, template)` recibe la carga.
2. Valida y registra la carga masiva (maestro/ítems).
3. Responde `SharedIdResponse` (id de la carga).

### Contrato
- Endpoint: `POST massiveload/sincronizeCargaMasiva` (`fileUrl`, `template`) · Auth: `Authorization`.

## CU-MAS-007 — Procesar el archivo

- **Actor:** Usuario autenticado (o scheduler)
- **Precondiciones:** existe una carga registrada (CU-MAS-006).
- **Postcondiciones:** los ítems del archivo fueron procesados.

### Pasos — Frontend
1. El usuario hace clic en "Procesar" sobre la carga.
2. La SPA invoca el procesamiento enviando el `itemId`.
3. Muestra estado/resultado del procesamiento.

### Pasos — Backend
1. `MassiveRest.sincronizeCargaMasivaItem(itemId)` inicia/procesa el ítem.
2. Delega a los servicios de carga masiva (`d3.massiveload.application`).
3. Actualiza estado y responde el resultado.

### Contrato
- Endpoint: `POST massiveload/sincronizeCargaMasivaItem` (`itemId`) · Auth: `Authorization`.

## CU-MAS-008 — Consultar historial de cargas

- **Actor:** Usuario autenticado
- **Precondiciones:** existen cargas previas.
- **Postcondiciones:** se lista el historial de cargas.

### Pasos — Frontend
1. El usuario abre la vista "Historial de cargas".
2. La SPA consulta la lista de cargas.
3. Renderiza tabla con estado/fecha/resultado.

### Pasos — Backend
1. `MassiveMasterController` (`/massiveload/cargaMasiva`) lista los maestros de carga
   (historial) — **endpoint de listado por confirmar**.
2. Filtra por tenant/sesión y responde la lista.

### Contrato
- Endpoint: `GET massiveload/cargaMasiva` (❑ por confirmar) · Auth: `Authorization`.
