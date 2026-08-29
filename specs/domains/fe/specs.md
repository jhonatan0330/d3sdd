# Specs — Facturación Electrónica (FE)

## Requisitos

### Funcionales
- R-FE-001: Firmar XML de facturación y notas electrónicas con certificado digital.
- R-FE-002: Generar el Código Único (CUF/E) según normativa DIAN.
- R-FE-003: Soporte de carga vía ZIP.

### No funcionales
- NF-FE-001: Cabecera SOAP de seguridad DIAN (`DianSoapSecurityHeader`).
- NF-FE-002: Manejo de keystore (`KeyStoreDataProvider`, `FirstCertificateSelector`).

## Diseño

### Paquetes (ARCH-001, realizado)
- `d3.fe` (raíz del módulo)

### Componentes
- `FEController` (`/fe`): `sign`, `signWithZip`, `signNE`, `signNEWithZip`, `generateCU`.
- `SignerService`: lógica de firma.
- `DianSoapSecurityHeader`: cabecera SOAP para el WS DIAN.
- `FEResponse`: respuesta de firma.

### DTOs
- `FEResponse`.
