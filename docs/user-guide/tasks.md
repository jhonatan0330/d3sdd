# Tareas

Gestión de tareas personales en la app. Casos de uso relacionados:
[CU-TASK-001..005](../specs/domains/tasks/use-cases.md).

> Endpoints y DTOs en el [contrato de API](../specs/contract/api-contract.md) §7.

## Listar tareas

- La pantalla de tareas llama `GET /task/` y muestra las tareas del usuario autenticado
  (título, prioridad, fecha de vencimiento).

## Crear tarea

1. Agrega una tarea con un título.
2. La app llama `POST /task/create`. Por defecto asigna `priority = 1`, `order = 0`.
3. La nueva tarea aparece en la lista.

## Actualizar tarea

- Edita título, notas, fecha de vencimiento o prioridad y guarda (`POST /task/update`).

## Eliminar tarea

- Elimina la tarea (`POST /task/delete/{id}`); se quita de la lista local.

## Notas

- Las fechas usan el formato `yyyy-MM-dd@HH:mm:ss.SSSZ` (zona `America/Bogota`).
- Las tareas son por usuario; no hay tareas compartidas/asignadas a terceros hoy.
