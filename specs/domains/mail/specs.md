# Specs — Correo (MAIL)

## Requisitos

### Funcionales
- R-MAIL-001: Envío de correos transaccionales (`MensajeDTO`).
- R-MAIL-002: Plantillas de correo (`MensajePlantillaCorreoDTO`).
- R-MAIL-003: Recuperación de contraseña por correo.

### No funcionales
- NF-MAIL-001: Cola de mensajes (`MailReleaseMessageQueueService`).

## Diseño

### Paquetes (ARCH-001, realizado)
- `d3.mail`

### Componentes
- `MailGenerateMessageService`, `MailSendMessageService`, `MailRecoverPasswordService`,
  `MailUserSendMessage`, `MailReleaseMessageQueueService`, `MailSendMessageToAdminService`.
- `MensajePlantillaCorreoSvc`, `MensajeSvc`.
- Mappers: `MensajeMapper`, `MensajePlantillaCorreoMapper`.

### DTOs
- `MensajeDTO`, `MensajePlantillaCorreoDTO` y filtros.
