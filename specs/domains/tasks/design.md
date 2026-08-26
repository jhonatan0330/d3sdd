# Diseño — Tareas (TASK)

Decisiones de diseño del dominio de tareas. Referencia: `use-cases.md`, `requirements.md`,
`contract/api-contract.md` §7 y `specs/decisions/ADR-001-package-rename.md`.

## D1. Paquetes (target según ADR-001)

| Hoy | Target (ADR-001) |
|-----|------------------|
| `com.task.task.domain` | `d3.task.domain` |
| `com.task.task.application` | `d3.task.application` |
| `com.task.task` (rest) | `d3.task.infrastructure` |

Clases: `TaskDTO`, `TaskFilterDTO`, `TaskRequest` (domain); `TaskRest` (infrastructure);
`TaskCreateService`, `TaskUpdateService`, `TaskDeleteService`, `TaskGetByUserService`,
`TaskService` (base) (application).

## D2. Flujo

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

## D3. Autenticación

Todos los endpoints usan `Authorization` header; el usuario se resuelve con
`SharedAuthenticateService` (mismo mecanismo que el resto de la API).

## D4. Formato de fechas

`TaskDTO` y `TaskRequest` serializan fechas con patrón
`yyyy-MM-dd@HH:mm:ss.SSSZ` (timezone `America/Bogota`). El front debe respetar ese formato.

## D5. Notas de implementación

- `GET /task/{id}` toma el `id` de un query param (`@RequestParam id`), no del path. Decisión:
  mantener por compatibilidad; si se refactoriza, migrar a `@PathVariable` (backlog
  TASK-NEW-001).
- No hay paginación ni filtrado avanzado hoy (solo lista por usuario). Ver backlog.
