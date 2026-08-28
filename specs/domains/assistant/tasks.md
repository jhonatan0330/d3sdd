# Tasks — Asistente (ASSISTANT)

Desglose de implementación. Dominio **front-only** (`d3_front`); no hay tareas de backend
porque no posee contrato propio (reutiliza `documents` §6). Estado: `[ ]` pendiente,
`[x]` hecho, `[~]` en curso.

## Frontend (d3_front)

- [x] **T-ASSISTANT-001** `AssistantService` con `mensajes` (signal), `interpretar()`,
  `ejecutar()`, `filtrarTemplates()`, `abrirDocumento()`, `abrirTemplateDirect()`.
- [x] **T-ASSISTANT-002** `AssistantButtonComponent` (botón flotante) + apertura con tecla F9.
- [x] **T-ASSISTANT-003** `AssistantDialogComponent` (chat UI) — **a migrar a panel** (ver D1).
- [x] **T-ASSISTANT-004** Búsqueda `@código` → `ApiService.listarDocumentos` →
  `POST /document/getDocuments`.
- [x] **T-ASSISTANT-005** Navegación `/texto` → filtro de `TemplateService.template()` →
  `Router.navigate(['/list/list', id])`.
- [x] **T-ASSISTANT-006** **Migrar `MatDialog` → panel lateral derecho** (ver `design.md` D1):
  - [x] Reemplazar `MatDialog`/`MatDialogRef` por `AssistantPanelComponent` (panel `fixed
    right-0`) anclado a la derecha en `app.component.html` / shell.
  - [x] Renombrar `AssistantService.isOpenDialog()` → `isOpenPanel()` y ajustar triggers
    (`togglePanel()`/`openPanel()`/`closePanel()`).
  - [x] Manejar cierre con **Esc** y F9 (toggle) sin bloquear el resto de la app.
  - [x] Responsive: ancho fijo (`md:w-[400px]`) en desktop, full-width en móvil.
  - [x] Accesibilidad: foco al input al abrir (`ngAfterViewInit`) y retorno de foco al
    cerrar (`AssistantService.triggerElement`).
- [x] **T-ASSISTANT-007** Actualizar `docs/user-guide/assistant.md` para reflejar el panel
  lateral (estado anterior: modal).

## Verificación

- Front: `npm run build` y `npx tsc -p tsconfig.app.json --noEmit`
