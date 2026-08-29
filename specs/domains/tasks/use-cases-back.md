# Casos de uso — Tareas (TASK) (BACKEND)

> Capa backend (d3brain). Contratos/endpoints aquí. Los pasos de UI (si los hubiera) van en use-cases-front.md.

Dominio: `tasks`. Contrato asociado: [`contract.md`](../../contract.md) §7.

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-TASK-001 | Listar tareas del usuario | Usuario autenticado | ✅ documentado |
| CU-TASK-002 | Ver tarea por id | Usuario autenticado | ✅ |
| CU-TASK-003 | Crear tarea | Usuario autenticado | ✅ |
| CU-TASK-004 | Actualizar tarea | Usuario autenticado | ✅ |
| CU-TASK-005 | Eliminar tarea | Usuario autenticado | ✅ |

---

## CU-TASK-001 — Listar tareas del usuario

- **Actor:** Usuario autenticado.
- **Pasos:** `GET /task/` con header `Authorization` → `List<TaskDTO>` (tareas del usuario
  autenticado vía `SharedAuthenticateService.getUser`).
- **Postcondición:** Se muestra la lista de tareas del usuario.

## CU-TASK-002 — Ver tarea por id

- **Pasos:** `GET /task/{id}?id={key}` con header `Authorization` → `TaskDTO`.
- **Nota:** el `id` se recibe como query param (`@RequestParam`), no del path. Validar el
  token antes de `taskService.getById`.

## CU-TASK-003 — Crear tarea

- **Pasos:** `POST /task/create` con `TaskRequest { title, notes?, dueDate?, priority?, order? }`
  y header `Authorization` → `SharedIdResponse` (id de la tarea creada).
- **Regla:** `priority` por defecto 1, `order` 0 (ver front `createTask`).

## CU-TASK-004 — Actualizar tarea

- **Pasos:** `POST /task/update` con `TaskRequest` (incluye `key`) y header `Authorization` →
  `SharedIdResponse`.

## CU-TASK-005 — Eliminar tarea

- **Pasos:** `POST /task/delete/{id}` con header `Authorization` → `SharedIdResponse`.
- **Postcondición:** La tarea se elimina para el usuario.

