# Autorización y Roles

Control de acceso y identidad.

- Listar roles: `GET user/getRole`
- Roles de usuario: `GET user/roles/{userId}`
- Cambiar contraseña: `POST user/cambiarClaveUsuarioAutenticacion`
- 2FA: `POST user/dfa`

# Transición de Documentos

Cambio de estado y trazabilidad.

- Cambiar estado: `POST rest/changeState`
- Traza de relaciones: `POST template/getTrace`
- Campos de traza: `GET template/getTraceFields/{documentId}/{transaction}`
