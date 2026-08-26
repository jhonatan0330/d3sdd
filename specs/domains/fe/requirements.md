# Requisitos — Facturación Electrónica (FE)

## Funcionales
- R-FE-001: Firmar XML de facturación y notas electrónicas con certificado digital.
- R-FE-002: Generar el Código Único (CUF/E) según normativa DIAN.
- R-FE-003: Soporte de carga vía ZIP.

## No funcionales
- NF-FE-001: Cabecera SOAP de seguridad DIAN (`DianSoapSecurityHeader`).
- NF-FE-002: Manejo de keystore (`KeyStoreDataProvider`, `FirstCertificateSelector`).
