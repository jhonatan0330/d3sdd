# Specs — Layout (LAY) — Front-only

Dominio front-only: interfaz gráfica general del shell de la aplicación (header, sidebar, footer, navegación).

## Requisitos

### Funcionales
- **R-LAY-001** Shell de la aplicación con header, sidebar y contenido principal.
- **R-LAY-002** Navegación lateral (menú colapsable) con rutas lazy-loaded.
- **R-LAY-003** Dashboard con indicadores y tarjetas resumen.
- **R-LAY-004** Gestión de usuario (avatar, cerrar sesión, configuración).
- **R-LAY-005** Atajos de teclado y accesos directos.

### No funcionales
- **NF-LAY-001** Front-only: no tiene backend propio.
- **NF-LAY-002** Reutiliza endpoints de `document`, `configuration` y `notification` para datos del dashboard.

## Diseño

### Frontend (`d3_front`)

Carpeta: `src/app/layout/`

Componentes:
- `layout.component` — Shell principal (mat-sidenav + router-outlet).
- `dashboard/` — Tarjetas resumen e indicadores (`indicadores.service.ts`).
- `user/` — Avatar, cerrar sesión.
- `simple-nav/` — Sidebar de navegación.
- `navigation/` — Configuración de menú.
- `shortcuts/` — Atajos de teclado.
- `change-picture/` — Cambio de imagen de perfil.
- `core/config/` — Configuración del layout.

### Notas
- El layout consume endpoints de otros dominios para poblar el dashboard.
- No tiene paquete backend propio; es puramente una capa de presentación.
