# Specs — Subida de archivos (UPLOAD)

## Requisitos

### Funcionales
- R-UP-001: Servir archivos subidos por ruta compuesta (visibilidad/tipo/año/mes/día/nombre).
- R-UP-002: Gestionar la carga de archivos (DTO `CargaArchivoDTO`).

### No funcionales
- NF-UP-001: Autenticación por `Authorization` en las rutas protegidas.

## Diseño

### Paquetes (ARCH-001, realizado)
- `d3.upload`

### Componentes
- `FileController` (`/files`): sirve archivos.
- `CargaArchivoSvc`, `UploadSvc`: lógica de carga.
- `CargaArchivoDTO`: metadata de carga.

### Notas
El endpoint de carga del front suele pasar por `/rest/upload` o `/document/upload`
(módulo `documents`); aquí `upload` es la capa de almacenamiento.
