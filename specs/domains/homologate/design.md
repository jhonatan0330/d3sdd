# Diseño — Homologación (HOMO)

## Paquetes (ADR-001, realizado)
- `d3.homologate`

## Componentes
- `HomologateAdapterService` (orquestador).
- Entidades: `HomologateAccount`, `HomologateCatalog`, `HomologateProduct`,
  `HomologateProductStock`, `HomologateProductStockDeduction`, `HomologateTariff`,
  `HomologateFaq`, `HomologateFee`.
- `ConfigEnum`: configuración de homologación.

## Notas
Relacionado con `accounting` (cuentas) y `tariff` (tarifas).
