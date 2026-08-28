# Manual de uso — D3 Front (Angular)

Guía para el usuario final de la aplicación SPA (`d3_front`). Explica cómo usar la interfaz
y está vinculada a los casos de uso del SDD (ver [`specs/`](../specs/README.md)).

> Nota: las secciones marcadas como *en desarrollo* dependen de funcionalidades aún en
> implementación en el backend (ver `specs/domains/authentication/`).

## Mapa de la aplicación

| Sección | Qué es | Caso de uso SDD |
|---------|--------|-----------------|
| Ingreso | Login de usuario, Google, recuperación | [CU-AUTH-001/002/005](../specs/domains/authentication/use-cases.md) |
| Navegación | Menú lateral y módulos | — |
| Documentos | Crear/consultar/expedir expedientes | [CU-DOC-001..010](../../specs/domains/documents/use-cases.md) |
| Tareas | Gestión de tareas | [CU-TASK-001..005](../../specs/domains/tasks/use-cases.md) |
| Contabilidad | Comprobantes / plan contable | [CU-ACC-001..008](../../specs/domains/accounting/use-cases.md) · [accounting.md](accounting.md) |
| Carga masiva | Carga de documentos en lote | [CU-MAS-001..004](../../specs/domains/massive/use-cases.md) · [massive-notifications.md](massive-notifications.md) |
| Notificaciones | Centro de notificaciones | [CU-NOT-001..004](../../specs/domains/notifications/use-cases.md) · [massive-notifications.md](massive-notifications.md) |
| Configuración | Org, procesos, plantillas | [CU-CFG-001..008](../../specs/domains/config-forms/use-cases.md) · [config-persons.md](config-persons.md) |
| Personas | Catálogo de usuarios | [CU-PER-001..004](../../specs/domains/persons/use-cases.md) · [config-persons.md](config-persons.md) |
| Autorización | Roles y 2FA | [CU-AUT-001..004](../../specs/domains/authorization/use-cases.md) · [auth-transition.md](auth-transition.md) |
| Transición | Cambio de estado / traza | [CU-DT-001..003](../../specs/domains/document-transition/use-cases.md) · [auth-transition.md](auth-transition.md) |
| Asistente | Buscador de comandos (`@doc`, `/módulo`, F9) — panel lateral derecho | [CU-ASSISTANT-001..004](../../specs/domains/assistant/use-cases.md) · [assistant.md](assistant.md) |
| Módulos aux. (fe, upload, ws, process, mt, inv, money, tariff, report, mail, homologate, doc-tx) | Servicios/endpoints auxiliares | [módulos.md](modules.md) |

## Cómo leer este manual

Cada página describe los pasos en la interfaz. Si eres desarrollador, usa los enlaces a los
casos de uso para ver el contrato y los requisitos técnicos.
