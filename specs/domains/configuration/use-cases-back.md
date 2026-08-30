# Casos de uso — Configuración (CFG) (BACKEND)

> Capa backend (d3brain). Contratos/endpoints aquí. Los pasos de UI (si los hubiera) van en use-cases-front.md.

Dominio: `configuration` (propiedades, configuración export/import, formularios dinámicos, homologación).

## Configuración y Formularios

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

---

## Homologación

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-HOMO-001 | Homologar cuentas | Sistema | ✅ |
| CU-HOMO-002 | Homologar catálogos | Sistema | ✅ |
| CU-HOMO-003 | Homologar productos/stock | Sistema | ✅ |
| CU-HOMO-004 | Homologar tarifas/faq/fees | Sistema | ✅ |

---

## CU-HOMO-001..004
`HomologateAdapterService` + `HomologateAccount`, `HomologateCatalog`, `HomologateProduct`,
`HomologateProductStock`, `HomologateProductStockDeduction`, `HomologateTariff`,
`HomologateFaq`, `HomologateFee` adaptan entidades entre orígenes.
