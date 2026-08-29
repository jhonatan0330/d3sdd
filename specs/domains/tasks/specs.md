# Specs — Tareas (TASK)

## Requisitos

Formato EARS. Referencia: `use-cases-back.md` y `contract.md` §7.

### Requisitos funcionales

- **RF-TASK-001** El sistema *debe* listar las tareas del usuario autenticado en `GET /task/`.
- **RF-TASK-002** El sistema *debe* devolver una tarea por id en `GET /task/{id}?id={key}`.
- **RF-TASK-003** El sistema *debe* crear una tarea en `POST /task/create` (`TaskRequest`).
- **RF-TASK-004** El sistema *debe* actualizar una tarea en `POST /task/update`.
- **RF-TASK-005** El sistema *debe* eliminar una tarea en `POST /task/delete/{id}`.
- **RF-TASK-006** Toda operación *requerirá* token válido (`Authorization`); el usuario se
  resuelve con `SharedAuthenticateService.getUser`.

### Reglas de negocio

- **RN-TASK-001** Una tarea pertenece a un `user`; las operaciones se limitan al usuario
  autenticado (salvo que el servicio lo permita explícitamente).
- **RN-TASK-002** Campos: `title` (obligatorio en la creación), `notes`, `dueDate`,
  `priority` (default 1), `order` (default 0), `completed` (fecha de finalización).

### Trazabilidad

| Requisito | Caso de uso | Contrato |
|-----------|-------------|----------|
| RF-TASK-001 | CU-TASK-001 | `GET /task/` |
| RF-TASK-002 | CU-TASK-002 | `GET /task/{id}` |
| RF-TASK-003 | CU-TASK-003 | `POST /task/create` |
| RF-TASK-004 | CU-TASK-004 | `POST /task/update` |
| RF-TASK-005 | CU-TASK-005 | `POST /task/delete/{id}` |

## Diseño

Decisiones de diseño del dominio de tareas. Referencia: `use-cases-back.md`, `specs.md`,
`contract.md` §7 y `specs/backlog-strategies/ARCH-001-package-rename.md`.

### D1. Paquetes (target según ARCH-001)

| Realizado (raíz `d3`) |
|--------|
| `d3.task.domain` |
| `d3.task.application` |
| `d3.task.infrastructure` |

Clases: `TaskDTO`, `TaskFilterDTO`, `TaskRequest` (domain); `TaskRest` (infrastructure);
`TaskCreateService`, `TaskUpdateService`, `TaskDeleteService`, `TaskGetByUserService`,
`TaskService` (base) (application).

### D2. Flujo

```
SPA --GET /task/--> TaskRest
        |
        v
SharedAuthenticateService.getUser(token, request)  -> usuario
        |
        v
TaskGetByUserService.call(usuario) -> List<TaskDTO>
```

- Crear/Actualizar: `TaskRequest.toModel()` mapea a `TaskDTO` y delega en
  `TaskCreateService` / `TaskUpdateService`.
- Eliminar: `TaskDeleteService.call(id, usuario)` → `SharedIdResponse`.

### D3. Autenticación

Todos los endpoints usan `Authorization` header; el usuario se resuelve con
`SharedAuthenticateService` (mismo mecanismo que el resto de la API).

### D4. Formato de fechas

`TaskDTO` y `TaskRequest` serializan fechas con patrón
`yyyy-MM-dd@HH:mm:ss.SSSZ` (timezone `America/Bogota`). El front debe respetar ese formato.

### D5. Notas de implementación

- `GET /task/{id}` toma el `id` de un query param (`@RequestParam id`), no del path. Decisión:
  mantener por compatibilidad; si se refactoriza, migrar a `@PathVariable` (backlog
  TASK-NEW-001).
- No hay paginación ni filtrado avanzado hoy (solo lista por usuario). Ver backlog.

