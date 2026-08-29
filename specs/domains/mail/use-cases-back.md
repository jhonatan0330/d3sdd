# Casos de uso — Correo (MAIL) (BACKEND)

> Capa backend (d3brain). Contratos/endpoints aquí. Los pasos de UI (si los hubiera) van en use-cases-front.md.

Dominio: `mail`. Paquete `d3.mail`. Módulo de servicios (sin controlador REST propio;
consumido por `notifications` y recuperación de clave).

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-MAIL-001 | Enviar mensaje de correo | Sistema | ✅ |
| CU-MAIL-002 | Recuperar contraseña por correo | Sistema | ✅ |
| CU-MAIL-003 | Plantillas de correo | Sistema | ✅ |
| CU-MAIL-004 | Cola de envío | Sistema | ✅ |

---

## CU-MAIL-001..004
`MailSendMessageService`, `MailRecoverPasswordService`, `MailUserSendMessage`,
`MailReleaseMessageQueueService`, `MailSendMessageToAdminService`, `MensajePlantillaCorreoSvc`
gestionan el envío y las plantillas.

