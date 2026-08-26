# Diseño — Configuración y Formularios (CFG)

## Paquetes (realizado según ADR-001)
- `d3.property` (propiedades)
- `d3.configuration_file` (export/import de configuración)
- `d3.process_form` (formularios dinámicos)

## Componentes
- `PropertyController` (`d3.property`, `/property`): `PropiedadSvc`, `PropiedadValorDefinidoSvc`.
- `ConfigurationController` (`d3.configuration_file`, `/configuration`): `ExportConfigurationFileService`,
  `ImportConfigurationFileService`.
- Formularios dinámicos: `d3.process_form` (`TemplateController`, `/template`).

## DTOs
- `PropiedadDTO`, `PropiedadFilterDTO`, `PropiedadValorDefinidoDTO`,
  `PropiedadValorDefinidoFilterDTO`, `ExportListRequest`, `FileVO`.
