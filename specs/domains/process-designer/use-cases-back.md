# Casos de uso — Diseñador de procesos (PD) (BACKEND)

> Capa backend (d3brain). Contratos/endpoints aquí. Los pasos de UI (si los hubiera) van en use-cases-front.md.

Dominio: `process-designer`. Paquete `d3.process_designer`. Contrato: §17.

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-PD-001 | Copiar proceso | Usuario autenticado | ✅ |
| CU-PD-002 | Gestionar procesos/estados/transiciones | Usuario autenticado | 🔧 |

---

## CU-PD-001 — Copiar
`POST /process_designer/copy` → duplica un proceso.

## CU-PD-002 — Procesos
`ProcesoSvc`, `ProcesoEstadoSvc`, `ProcesoTransicionSvc`, `ProcesoTransicionAutomaticaSvc`
gestionan definición de procesos (pendiente documentar CRUD completo).

