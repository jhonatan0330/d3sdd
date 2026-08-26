# Requisitos — Tareas (TASK)

Formato EARS. Referencia: `use-cases.md` y `contract/api-contract.md` §7.

## Requisitos funcionales

- **RF-TASK-001** El sistema *debe* listar las tareas del usuario autenticado en `GET /task/`.
- **RF-TASK-002** El sistema *debe* devolver una tarea por id en `GET /task/{id}?id={key}`.
- **RF-TASK-003** El sistema *debe* crear una tarea en `POST /task/create` (`TaskRequest`).
- **RF-TASK-004** El sistema *debe* actualizar una tarea en `POST /task/update`.
- **RF-TASK-005** El sistema *debe* eliminar una tarea en `POST /task/delete/{id}`.
- **RF-TASK-006** Toda operación *requerirá* token válido (`Authorization`); el usuario se
  resuelve con `SharedAuthenticateService.getUser`.

## Reglas de negocio

- **RN-TASK-001** Una tarea pertenece a un `user`; las operaciones se limitan al usuario
  autenticado (salvo que el servicio lo permita explícitamente).
- **RN-TASK-002** Campos: `title` (obligatorio en la creación), `notes`, `dueDate`,
  `priority` (default 1), `order` (default 0), `completed` (fecha de finalización).

## Trazabilidad

| Requisito | Caso de uso | Contrato |
|-----------|-------------|----------|
| RF-TASK-001 | CU-TASK-001 | `GET /task/` |
| RF-TASK-002 | CU-TASK-002 | `GET /task/{id}` |
| RF-TASK-003 | CU-TASK-003 | `POST /task/create` |
| RF-TASK-004 | CU-TASK-004 | `POST /task/update` |
| RF-TASK-005 | CU-TASK-005 | `POST /task/delete/{id}` |
