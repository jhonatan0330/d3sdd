# Casos de uso — Configuración y Formularios (CFG)

Dominio: `config-forms` (propiedades, configuración export/import, formularios dinámicos).
Contrato: §12.

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-CFG-001 | Listar propiedades por tipo/campo | Usuario autenticado | ✅ |
| CU-CFG-002 | Listar valores definidos por filtro | Usuario autenticado | ✅ |
| CU-CFG-003 | Obtener propiedad por llave | Usuario autenticado | ✅ |
| CU-CFG-004 | Crear/editar propiedad | Usuario autenticado | ✅ |
| CU-CFG-005 | Exportar configuración completa | Usuario autenticado | ✅ |
| CU-CFG-006 | Exportar configuración por módulos | Usuario autenticado | ✅ |
| CU-CFG-007 | Importar configuración | Usuario autenticado | ✅ |
| CU-CFG-008 | Comparar configuración | Usuario autenticado | ✅ |

---

## CU-CFG-001 — Propiedades por tipo/campo
`GET property/{type}/{field}` → `List<PropiedadDTO>`.

## CU-CFG-002 — Valores definidos
`GET property/type/{type}/{filterName}` → `List<PropiedadValorDefinidoDTO>`.

## CU-CFG-003 — Propiedad por llave
`GET property/{key}` → `PropiedadDTO`.

## CU-CFG-004 — Crear propiedad
`POST property/` con `PropiedadDTO` → `PropiedadDTO`.

## CU-CFG-005..008 — Export/Import/Compare
`GET configuration/export`, `POST configuration/module`,
`POST configuration/import`, `POST configuration/compare` → `FileVO`.
