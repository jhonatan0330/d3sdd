# Diseño — Correo (MAIL)

## Paquetes (ADR-001, realizado)
- `d3.mail`

## Componentes
- `MailGenerateMessageService`, `MailSendMessageService`, `MailRecoverPasswordService`,
  `MailUserSendMessage`, `MailReleaseMessageQueueService`, `MailSendMessageToAdminService`.
- `MensajePlantillaCorreoSvc`, `MensajeSvc`.
- Mappers: `MensajeMapper`, `MensajePlantillaCorreoMapper`.

## DTOs
- `MensajeDTO`, `MensajePlantillaCorreoDTO` y filtros.
