# ARCH-011 — Sincronización de servicios frontend con contract.md

- **Estado:** Aprobado
- **Fecha:** 2026-08-29
- **Dominios afectados:** todos (`d3_front`)
- **Relacionado con:** [ARCH-011](../backlog.md#arch-011) en backlog

## Contexto

El frontend (`d3_front`) consume endpoints REST del backend (`d3brain`). El contrato
oficial está documentado en `sdd/specs/contract.md` (fuente de verdad humana) y
`openapi.yaml` (fuente de verdad máquina). Actualmente existen divergencias entre los
tipos TypeScript del frontend y los DTOs definidos en el contrato:

1. **Nombres de campos inconsistentes**: el contract define `user`, el front usa `responsable`
   o `usuario`; el contract define `createdAt`, el front no lo tiene.
2. **Sin separación request/response**: se usa un solo tipo (`Task`) para ambos flujos,
   cuando el contrato define `TaskDTO` (respuesta) y `TaskRequest` (request) como tipos
   distintos.
3. **Endpoints desalineados**: el service llama a paths que no coinciden con el contract
   (ej. `getTaskById` usa `/task/{id}` sin query param cuando el contract dice
   `/task/{id}?id={key}`).
4. **Tipos de respuesta genéricos**: se usa `IdResponse` cuando el contract define
   `SharedIdResponse { id, code?, state?, comment?, messages[] }`.
5. **DTOs compartidos en archivos monolíticos**: `sw42.domain.ts` (584 líneas) mezcla
   DTOs de múltiples dominios sin relación con el contract.

## Decisión

### Regla central

**Los tipos TypeScript en el frontend deben ser espejo exacto de los DTOs definidos
en `contract.md` para el dominio correspondiente.** El contract.md es la fuente de
verdad; el código del frontend debe reflejarlo sin desviaciones.

### Estructura de archivos por dominio

```
src/app/<dominio>/
  <dominio>.types.ts    ← Interfaces que espejan el contract.md (TaskDTO, TaskRequest, etc.)
  <dominio>.service.ts  ← Métodos que usan los tipos del contract y paths exactos
```

### Reglas de implementación

| # | Regla | Ejemplo |
|---|-------|---------|
| R1 | Cada dominio tiene un `<dominio>.types.ts` con interfaces del contract | `task.types.ts` con `TaskDTO` y `TaskRequest` |
| R2 | Separar tipo de respuesta (`*DTO`) del tipo de request (`*Request`) | `TaskDTO` para GET/respuesta, `TaskRequest` para POST/body |
| R3 | Los campos deben coincidir exactamente con el contract | Si el contract dice `user`, el tipo tiene `user`, no `responsable` |
| R4 | Los paths en el service deben coincidir con el contract | `GET /task/{id}?id={key}` no `/task/{id}` |
| R5 | Usar `SharedIdResponse` (definido en `shared/api-types.ts`) para respuestas con ID | No `IdResponse` ad-hoc |
| R6 | Los tipos del contract son **solo para comunicación HTTP**; el dominio puede tener tipos extendidos para UI | `TaskViewModel extends TaskDTO { selected?: boolean }` |
| R7 | No agregar campos que no existan en el contract a los tipos de comunicación | Si el contract no tiene `llaveTabla`, el tipo no lo tiene |

### Shared API types

En `src/app/shared/api-types.ts` se definen los tipos comunes del contract:

```typescript
export interface SharedIdResponse {
  id: string;
  code?: string;
  state?: string;
  comment?: string;
  messages?: string[];
}

export interface SharedApiErrorResponse {
  status: number;
  error_code: string;
  message: string;
  detail?: string;
}
```

### Mapeo de endpoints legacy

Cuando el backend expone el mismo endpoint con dos paths (legacy + nuevo), el frontend
debe usar el path del contract. Si ambos coexisten, documentar cuál es el oficial:

| Contract path | Legacy path | Acción |
|---------------|-------------|--------|
| `/document/api/guardarDocumento` | `/document/saveDocument` | Usar `/document/api/guardarDocumento` |
| `/document/api/consultarDocumento` | `/document/getDocument` | Usar `/document/api/consultarDocumento` |
| `/document/api/upload` | `/document/upload` | Usar `/document/api/upload` |

## Consecuencias

- **Positivas:**
  - El front siempre refleja el contrato; cualquier desviación es un defecto detectable.
  - Facilita review de código: se compara `*.types.ts` contra `contract.md`.
  - Reduce bugs de integración (campos faltantes, paths incorrectos).
  - Los DTOs shared (`SharedIdResponse`, etc.) se definen una vez y se reusan.
- **Riesgos:**
  - Requiere migración incremental de dominios existentes.
  - Los componentes que usan los tipos legacy necesitan adaptarse.
  - Si el contract.md cambia, se debe actualizar `*.types.ts` en el mismo paso.

## Dominios a migrar

| Dominio | Service actual | Archivos a crear/actualizar | Prioridad |
|---------|---------------|----------------------------|-----------|
| tasks | `task/tasks.service.ts` | `task.types.ts` (nuevo), `task/tasks.service.ts` | 🔴 Alta |
| notifications | `notification/notification.service.ts` | `notification.types.ts` (actualizar) | 🟡 Media |
| accounting | `accounting/accounting.service.ts` | `accounting.types.ts` (nuevo) | 🟡 Media |
| users | `users/contact.services.ts` | `users.types.ts` (nuevo) | 🟡 Media |
| authorization | `authentication/login.service.ts` | `authorization.types.ts` (nuevo) | 🟢 Baja |
| documents | `document/service/api.service.ts` | `document.types.ts` (nuevo) | 🟢 Baja (complejo) |
| config-forms | `configuration/*/` | `config.types.ts` (nuevo) | 🟢 Baja |
| massive | `massiveload/massive.component.ts` | `massive.types.ts` (nuevo) | 🟢 Baja |

## Estados

- [x] Decisión registrada (este ADR).
- [x] ADR reflejado en `architecture.md` (sección 12).
- [x] Backlog items creados en `backlog.md` (ARCH-011 + T-FRONT-xxx).
- [ ] Migración dominio `tasks` (ejemplo/piloto).
- [ ] Migración dominio `notifications`.
- [ ] Migración dominio `accounting`.
- [ ] Migración dominio `users`.
- [ ] Migración dominio `authorization`.
- [ ] Migración dominio `documents`.
- [ ] Migración dominio `config-forms`.
- [ ] Migración dominio `massive`.
