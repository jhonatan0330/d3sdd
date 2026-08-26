# Casos de uso — Homologación (HOMO)

Dominio: `homologate`. Paquete `d3.homologate`. Módulo de servicios (sin controlador REST
propio; adapta catálogos/productos entre sistemas).

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
