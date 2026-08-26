# Diseño — Facturación Electrónica (FE)

## Paquetes (ADR-001, realizado)
- `d3.fe` (raíz del módulo)

## Componentes
- `FEController` (`/fe`): `sign`, `signWithZip`, `signNE`, `signNEWithZip`, `generateCU`.
- `SignerService`: lógica de firma.
- `DianSoapSecurityHeader`: cabecera SOAP para el WS DIAN.
- `FEResponse`: respuesta de firma.

## DTOs
- `FEResponse`.
