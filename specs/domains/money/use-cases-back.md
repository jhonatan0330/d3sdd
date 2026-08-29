# Casos de uso — Dinero / Cajas (MONEY) (BACKEND)

> Capa backend (d3brain). Contratos/endpoints aquí. Los pasos de UI (si los hubiera) van en use-cases-front.md.

Dominio: `money`. Paquete `d3.money`. Módulo de servicios (sin controlador REST propio).

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-MON-001 | Gestionar cuentas | Sistema | ✅ |
| CU-MON-002 | Registrar movimientos | Sistema | ✅ |
| CU-MON-003 | Gestionar turnos | Sistema | ✅ |

---

## CU-MON-001/002/003
`CuentaSvc`, `MovimientoSvc`, `TurnoSvc` gestionan cuentas, movimientos y turnos de caja.

