# Specs — Configuración (CFG)

Dominio unificado: configuración de la instancia, propiedades, formularios dinámicos y homologación.

## Requisitos

### Funcionales

#### Configuración y Formularios
- **R-CFG-001** Propiedades parametrizables por tipo/campo con valores definidos.
- **R-CFG-002** Exportar/importar/comparar la configuración de la instancia (`FileVO`).
- **R-CFG-003** Formularios dinámicos (módulo `process_form`).

#### Homologación
- **R-HOMO-001** Mapeo de cuentas, catálogos, productos, tarifas entre sistemas externos.
- **R-HOMO-002** Stock y deducciones de stock homologados.

### No funcionales
- **NF-CFG-001** Autenticación por `Authorization`.
- **NF-CFG-002** Solo propiedades activas en consultas (`STATE_ACTIVE`).
- **NF-HOMO-001** Configuración vía enums (`ConfigEnum`).

## Diseño

### Paquetes (realizado según ARCH-001)
- `d3.property` (propiedades)
- `d3.configuration_file` (export/import de configuración)
- `d3.process_form` (formularios dinámicos)
- `d3.homologate` (homologación entre sistemas)

### Componentes

#### Configuración
- `PropertyController` (`d3.property`, `/property`): `PropiedadSvc`, `PropiedadValorDefinidoSvc`.
- `ConfigurationController` (`d3.configuration_file`, `/configuration`): `ExportConfigurationFileService`,
  `ImportConfigurationFileService`.
- Formularios dinámicos: `d3.process_form` (`TemplateController`, `/template`).

#### Homologación
- `HomologateAdapterService` (orquestador).
- Entidades: `HomologateAccount`, `HomologateCatalog`, `HomologateProduct`,
  `HomologateProductStock`, `HomologateProductStockDeduction`, `HomologateTariff`,
  `HomologateFaq`, `HomologateFee`.
- `ConfigEnum`: configuración de homologación.

### DTOs

#### Configuración
- `PropiedadDTO`, `PropiedadFilterDTO`, `PropiedadValorDefinidoDTO`,
  `PropiedadValorDefinidoFilterDTO`, `ExportListRequest`, `FileVO`.

### Notas
- Homologación relacionado con `accounting` (cuentas) y `tariff` (tarifas).
- Configuración consumida por `documents` (plantillas) y `users` (propiedades por usuario).
