# Documentos / Expedientes

Cómo trabajar con expedientes en la app. Casos de uso relacionados:
[CU-DOC-001..010](../specs/domains/documents/use-cases.md).

> Los endpoints exactos y el formato de los DTOs están en el
> [contrato de API](../specs/contract/api-contract.md) §6.

## Crear o editar un documento

1. Abre el módulo correspondiente (el formulario dinámico de la plantilla).
2. Completa los campos (el motor `neuron` renderiza los controles según la plantilla).
3. Guarda. La app llama al backend (`/rest/guardarDocumento`); si es nuevo se crea, si ya
   tiene id se actualiza.
4. Verás los mensajes/validaciones devueltos por el servidor.

> El botón de guardar envía un id de sesión (`non-duplicate`) para evitar guardados duplicados
> por doble clic.

## Consultar y listar

- Desde la bandeja/lista, la app consulta `/rest/listarDocumentos` (o `/document/getDocuments`)
  con los filtros (plantilla, código, texto, rango de fechas).
- Al abrir un documento, se carga el detalle completo (`/rest/consultarDocumento`).

## Cambiar estado

- Usa la opción de transición de estado; la app llama `/rest/changeState` con el ajuste.
- El historial de transiciones se consulta vía trazabilidad (`/template/getTrace`).

## Adjuntar archivos

- Sube archivos con la opción de adjunto; la app usa `/rest/upload` y guarda la URL.devuelta.

## Plantillas y campos

- Las plantillas disponibles se listan según tu perfil (`/template/getTemplates/{profile}`:
  `ADMIN`, `READER` u otro).
- Al abrir un formulario, la app resuelve los campos y sus valores dependientes
  (`/rest/obtenerCampos`, `/rest/consultarDatosBase`).

##Inventario

- Al vincular un producto, la app consulta `/document/getInventory/{id}` para mostrar existencias.
