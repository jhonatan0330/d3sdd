# Diseño — Configuración y Formularios (CFG)

## Paquetes (objetivo ADR-001)
- `d3.property` ← `com.softure.property`
- `d3.config` ← `com.softure.configuration_file`
- `d3.form` ← `com.softure.process_form`

## Componentes
- `PropertyController` (`/property`): `PropiedadSvc`, `PropiedadValorDefinidoSvc`.
- `ConfigurationController` (`/configuration`): `ExportConfigurationFileService`,
  `ImportConfigurationFileService`.
- Formularios dinámicos: `com.softure.process_form` (pendiente mapear).

## DTOs
- `PropiedadDTO`, `PropiedadFilterDTO`, `PropiedadValorDefinidoDTO`,
  `PropiedadValorDefinidoFilterDTO`, `ExportListRequest`, `FileVO`.
