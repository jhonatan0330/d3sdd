# Requisitos — Multi-tenancy (MT)

## Funcionales
- R-MT-001: Aislamiento de datos por tenant vía `TenantRoutingDataSource`.
- R-MT-002: Registro/descubrimiento de tenants (`TenantRegistry`, `DatabaseTenantRegistry`).
- R-MT-003: Creación y prueba de nuevos tenants.

## No funcionales
- NF-MT-001: El tenant NO viaja en el request; se deriva de la sesión/API key.
