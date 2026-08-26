# Casos de uso — Reportes (REPORT)

Dominio: `report`. Paquete `d3.report`. Generación de reportes (servlet + servicios).

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-REP-001 | Generar reporte (Jasper) | Usuario autenticado | 🔧 |
| CU-REP-002 | Ejecutar reporte parametrizado | Usuario autenticado | 🔧 |

---

## CU-REP-001/002
`ReporteServlet`, `GeneradorReportes`, `ReporteBaseSvc`, `ReporteEjecucionSvc`,
`ReportGenerateFromSql` generan reportes (Jasper). También expuesto vía `/api/getReport`.
