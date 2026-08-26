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
