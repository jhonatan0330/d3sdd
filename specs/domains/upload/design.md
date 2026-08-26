# Diseño — Subida de archivos (UPLOAD)

## Paquetes (ADR-001, realizado)
- `d3.upload`

## Componentes
- `FileController` (`/files`): sirve archivos.
- `CargaArchivoSvc`, `UploadSvc`: lógica de carga.
- `CargaArchivoDTO`: metadata de carga.

## Notas
El endpoint de carga del front suele pasar por `/rest/upload` o `/document/upload`
(módulo `documents`); aquí `upload` es la capa de almacenamiento.
