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
| Tareas | Gestión de tareas | (pendiente de spec) |
| Contabilidad | Comprobantes / plan contable | (pendiente de spec) |
| Carga masiva | Carga de documentos en lote | (pendiente de spec) |
| Notificaciones | Centro de notificaciones | (pendiente de spec) |
| Configuración | Org, procesos, plantillas | (pendiente de spec) |

## Cómo leer este manual

Cada página describe los pasos en la interfaz. Si eres desarrollador, usa los enlaces a los
casos de uso para ver el contrato y los requisitos técnicos.
