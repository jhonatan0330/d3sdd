# Casos de uso — Multi-tenancy (MT)

Dominio: `multitenancy`. Paquete `d3.multitenancy`. Módulo de infraestructura (sin
controlador REST propio; consumido internamente por `TenantFilter`).

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-MT-001 | Resolver tenant por request (catálogo JDBC) | Sistema | ✅ |
| CU-MT-002 | Crear/registrar tenant | Admin | 🔧 (ver INF-NEW-001) |
| CU-MT-003 | Iterar sobre tenants (jobs) | Sistema | ✅ |

---

## CU-MT-001 — Resolución de tenant
`TenantFilter` → `TenantContext` resuelve el tenant activo desde la sesión/catalog.

## CU-MT-002 — Creación de tenant
Pendiente de especificar (backlog INF-NEW-001: probar creación de tenants).

## CU-MT-003 — Iteración
`TenantIteratorService` ejecuta lógica por tenant (multi-tenant batch).
