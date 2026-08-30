# Specs — Shared (SHR)

Dominio transversal: objetos, servicios y componentes compartidos entre múltiples dominios.

## Requisitos

### Funcionales
- **R-SHR-001** DTOs compartidos: `BasicDTO`, `BasicFilterDTO`, `BasicParamDTO`.
- **R-SHR-002** Servicios utilitarios: `CopierService`, `ErrorHandlerService`, `FileHandlerService`, `PdfService`.
- **R-SHR-003** Interceptores HTTP: `TokenInterceptor`, `ErrorInterceptor`.
- **R-SHR-004** Componentes reutilizables: `app-dropdown`, `bpm-diagram`, `bpm-leaf-diagram`, `visor-pdf-dialog`.

### No funcionales
- **NF-SHR-001** `shared` no puede depender de ningún dominio específico.
- **NF-SHR-002** Los dominios pueden importar de `shared`, pero no al revés.
- **NF-SHR-003** Si un objeto se usa en ≤2 dominios, va en `shared`; si es solo 1, va en ese dominio.

## Diseño

### Backend (`d3brain`)

Paquete: `d3.shared`

```
d3.shared/
  domain/        # DTOs compartidos, excepciones, constantes
  application/   # Servicios utilitarios (fechas, validaciones, logging)
  infrastructure/ # Configuración base, utilidades de BD
```

### Frontend (`d3_front`)

Carpeta: `src/app/shared/`

```
src/app/shared/
  components/    # Componentes reutilizables (botones, modales, tablas)
  services/      # Servicios comunes (auth, notifications, storage)
  models/        # Modelos compartidos (pagination, response, error)
  guards/        # Guards de rutas (auth, roles)
  interceptors/  # Interceptors HTTP (token, error handling)
  utils/         # Utilidades (fechas, validaciones, formateo)
```

### Reglas de uso
- No crear dependencias circulares entre dominios vía `shared`.
- `shared` no puede depender de ningún dominio específico.
- Los dominios pueden importar de `shared`.
