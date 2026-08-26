# Casos de uso — Facturación Electrónica (FE)

Dominio: `fe` (facturación electrónica / DIAN). Paquete `d3.fe`. Contrato: §17.

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-FE-001 | Firmar documento (XML) | Sistema | ✅ |
| CU-FE-002 | Firmar documento desde ZIP | Sistema | ✅ |
| CU-FE-003 | Firmar nota electrónica (NE) | Sistema | ✅ |
| CU-FE-004 | Firmar nota electrónica desde ZIP | Sistema | ✅ |
| CU-FE-005 | Generar CU/CUFE | Sistema | ✅ |

---

## CU-FE-001 — Firmar
`POST fe/sign` → firma el documento (certificado, sello de tiempo, SOAP DIAN).

## CU-FE-002 — Firmar con ZIP
`POST fe/signWithZip` → documento empaquetado en ZIP.

## CU-FE-003/004 — Nota electrónica
`POST fe/signNE`, `POST fe/signNEWithZip` → firma de NE.

## CU-FE-005 — Generar CU
`POST fe/generateCU` → calcula el Código Único (CUF/E).
