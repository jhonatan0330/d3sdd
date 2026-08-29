# Specs — Configuración y Formularios (CFG)

## Requisitos

### Funcionales
- R-CFG-001: Propiedades parametrizables por tipo/campo con valores definidos.
- R-CFG-002: Exportar/importar/comparar la configuración de la instancia (`FileVO`).
- R-CFG-003: Formularios dinámicos (módulo `process_form`) — pendiente de lectura.

### No funcionales
- NF-CFG-001: Autenticación por `Authorization`.
- NF-CFG-002: Solo propiedades activas en consultas (`STATE_ACTIVE`).

## Diseño

### Paquetes (realizado según ARCH-001)
- `d3.property` (propiedades)
- `d3.configuration_file` (export/import de configuración)
- `d3.process_form` (formularios dinámicos)

### Componentes
- `PropertyController` (`d3.property`, `/property`): `PropiedadSvc`, `PropiedadValorDefinidoSvc`.
- `ConfigurationController` (`d3.configuration_file`, `/configuration`): `ExportConfigurationFileService`,
  `ImportConfigurationFileService`.
- Formularios dinámicos: `d3.process_form` (`TemplateController`, `/template`).

### DTOs
- `PropiedadDTO`, `PropiedadFilterDTO`, `PropiedadValorDefinidoDTO`,
  `PropiedadValorDefinidoFilterDTO`, `ExportListRequest`, `FileVO`.
