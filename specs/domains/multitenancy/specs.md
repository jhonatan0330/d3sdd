# Specs — Multi-tenancy (MT)

## Requisitos

### Funcionales
- R-MT-001: Aislamiento de datos por tenant vía `TenantRoutingDataSource`.
- R-MT-002: Registro/descubrimiento de tenants (`TenantRegistry`, `DatabaseTenantRegistry`).
- R-MT-003: Creación y prueba de nuevos tenants.

### No funcionales
- NF-MT-001: El tenant NO viaja en el request; se deriva de la sesión/API key.

## Diseño

### Paquetes (ARCH-001, realizado)
- `d3.multitenancy`

### Componentes
- `TenantFilter`, `TenantContext`: resolución por request.
- `TenantRoutingDataSource`, `TenantDataSourceFactory`, `TenantDataSourceConfiguration`:
  enrutamiento a catálogo JDBC por tenant.
- `DatabaseTenantRegistry`, `DatabaseTenantMetadataProvider`, `TenantMetadataProvider`:
  registro/metadata.
- `TenantIteratorService`: iteración por tenant.
- `TenantDTO`, `TenantFilterDTO`, `TenantMapper`: modelo.

### Notas
No expone REST propio; es infraestructura transversal usada por todos los dominios.
