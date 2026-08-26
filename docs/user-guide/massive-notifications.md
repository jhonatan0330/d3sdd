# Carga Masiva

Importación de datos en lote.

- Sincronizar ítem: `POST massiveload/sincronizeCargaMasivaItem` (`itemId`)
- Ejecutar carga: `POST massiveload/sincronizeCargaMasiva` (`fileUrl`, `template`)
- Gestión de ítems: `massiveload/cargaMasivaItem`
- Gestión de maestros: `massiveload/cargaMasiva`

# Notificaciones

Bandeja de actividades y transferencia de trabajo.

- Listar: `GET notification/getNotifications`
- Marcar leída: `POST notification/readActivity`
- Transferir: `POST notification/transfer`
- Usuarios para transferir: `POST notification/userToTransfer`
