# Módulos auxiliares / servicios

Dominios `d3.*` expuestos por HTTP o de servicios, documentados en `specs/domains/<modulo>/`.

## Con endpoint HTTP

| Módulo | Endpoints | Caso de uso SDD |
|--------|-----------|-----------------|
| Facturación electrónica (fe) | `fe/sign`, `fe/signWithZip`, `fe/signNE`, `fe/signNEWithZip`, `fe/generateCU` | [CU-FE-001..005](../../specs/domains/fe/use-cases.md) |
| Subida de archivos (upload) | `GET /files/{visibility}/{type}/{year}/{month}/{day}/{filename}` | [CU-UP-001](../../specs/domains/upload/use-cases.md) |
| Web services (webservice) | `POST /webservice/copy` | [CU-WS-001](../../specs/domains/webservice/use-cases.md) |
| Diseñador de procesos (process-designer) | `POST /process_designer/copy` | [CU-PD-001](../../specs/domains/process-designer/use-cases.md) |

## Módulos de servicio (sin REST propio)

- Multi-tenancy (`d3.multitenancy`): resolución de tenant por request — [CU-MT-001..003](../../specs/domains/multitenancy/use-cases.md)
- Inventario (`d3.inventory`) — [CU-INV-001..004](../../specs/domains/inventory/use-cases.md)
- Dinero/Cajas (`d3.money`) — [CU-MON-001..003](../../specs/domains/money/use-cases.md)
- Tarifas (`d3.tariff`) — [CU-TAR-001..002](../../specs/domains/tariff/use-cases.md)
- Reportes (`d3.report`) — [CU-REP-001..002](../../specs/domains/report/use-cases.md)
- Correo (`d3.mail`) — [CU-MAIL-001..004](../../specs/domains/mail/use-cases.md)
- Homologación (`d3.homologate`) — [CU-HOMO-001..004](../../specs/domains/homologate/use-cases.md)
- Log de transacciones (`d3.document_transaction`) — [CU-DTX-001..003](../../specs/domains/document-transaction/use-cases.md)
