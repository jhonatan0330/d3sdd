# Diseño — Carga Masiva (MAS)

## Paquetes (realizado según ADR-001)
- `d3.massiveload`

## Componentes
- `MassiveRest` (`massiveload`): sincronización de ítems y ejecución de carga.
- `MassiveItemController` (`massiveload/cargaMasivaItem`): gestión de ítems.
- `MassiveMasterController` (`massiveload/cargaMasiva`): gestión de maestros.

## Notas
El método `sincronizeCargaMasiva` recibe dos `@RequestBody` (`fileUrl`, `template`);
verificar si es un único objeto JSON o parámetros separados al migrar a ADR-001.
