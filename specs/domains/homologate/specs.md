# Specs — Homologación (HOMO)

## Requisitos

### Funcionales
- R-HOMO-001: Mapeo de cuentas, catálogos, productos, tarifas entre sistemas externos.
- R-HOMO-002: Stock y deducciones de stock homologados.

### No funcionales
- NF-HOMO-001: Configuración vía enums (`ConfigEnum`).

## Diseño

### Paquetes (ARCH-001, realizado)
- `d3.homologate`

### Componentes
- `HomologateAdapterService` (orquestador).
- Entidades: `HomologateAccount`, `HomologateCatalog`, `HomologateProduct`,
  `HomologateProductStock`, `HomologateProductStockDeduction`, `HomologateTariff`,
  `HomologateFaq`, `HomologateFee`.
- `ConfigEnum`: configuración de homologación.

### Notas
Relacionado con `accounting` (cuentas) y `tariff` (tarifas).
