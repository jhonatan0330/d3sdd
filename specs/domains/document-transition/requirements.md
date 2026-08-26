# Requisitos — Transición de Documentos (DT)

## Funcionales
- R-DT-001: Transicionar el estado de un documento (`changeState`).
- R-DT-002: Consultar relaciones/traza de un documento gestor.
- R-DT-003: Consultar campos de traza por transacción.

## No funcionales
- NF-DT-001: Autenticación por `Authorization` (o `securityToken` en API externa).
- NF-DT-002: Pertenece a `d3.document_transition` / `d3.document_execution` (ADR-001).
