# Diseño — Multi-tenancy (MT)

## Paquetes (ADR-001, realizado)
- `d3.multitenancy`

## Componentes
- `TenantFilter`, `TenantContext`: resolución por request.
- `TenantRoutingDataSource`, `TenantDataSourceFactory`, `TenantDataSourceConfiguration`:
  enrutamiento a catálogo JDBC por tenant.
- `DatabaseTenantRegistry`, `DatabaseTenantMetadataProvider`, `TenantMetadataProvider`:
  registro/metadata.
- `TenantIteratorService`: iteración por tenant.
- `TenantDTO`, `TenantFilterDTO`, `TenantMapper`: modelo.

## Notas
No expone REST propio; es infraestructura transversal usada por todos los dominios.
