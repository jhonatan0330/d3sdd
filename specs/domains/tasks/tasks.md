# Tasks — Tareas (TASK)

Desglose de implementación. Estado: `[ ]` pendiente, `[x]` hecho, `[~]` en curso.

## Backend (d3brain)

- [x] **T-TASK-001** `GET /task/` → `TaskGetByUserService`.
- [x] **T-TASK-002** `GET /task/{id}?id=` → `TaskService.getById`.
- [x] **T-TASK-003** `POST /task/create` → `TaskCreateService`.
- [x] **T-TASK-004** `POST /task/update` → `TaskUpdateService`.
- [x] **T-TASK-005** `POST /task/delete/{id}` → `TaskDeleteService`.
- [ ] **T-TASK-006** (backlog TASK-NEW-001) Migrar `GET /task/{id}` a `@PathVariable` id.
- [ ] **T-TASK-007** (backlog TASK-NEW-002) Notificaciones de vencimiento de tarea.

## Frontend (d3_front)

- [x] **T-TASK-101** `TasksService.getTasks()` → `GET /task/`.
- [x] **T-TASK-102** `TasksService.getTaskById()` → `GET /task/{id}`.
- [x] **T-TASK-103** `TasksService.createTask()` → `POST /task/create`.
- [x] **T-TASK-104** `TasksService.updateTask()` → `POST /task/update`.
- [x] **T-TASK-105** `TasksService.deleteTask()` → `POST /task/delete/{id}`.
- [ ] **T-TASK-106** (backlog) Pantalla de edición de tarea con `dueDate`/`priority` (hoy
  `createTask` solo envía `title`; ampliar el formulario).

## Verificación

- Back: `./gradlew.bat build -x test`
- Front: `npm run build` y `npx tsc -p tsconfig.app.json --noEmit`
