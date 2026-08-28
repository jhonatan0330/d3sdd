# Implementación Front — Asistente (ASSISTANT)

Detalles de implementación del front (`d3_front`). Dominio **front-only**: aquí vive la
lógica y el UI; no hay backend propio.

## Estructura de archivos

```
d3Front/src/app/assistant/
  assistant-button/        # botón flotante que abre el asistente (trigger F9)
  assistant-dialog/       # UI del asistente  ←  migrar a panel lateral (ver design.md D1)
  assistant.models.ts     # AssistantMessage, AssistantState, AssistantIntent, *Result, TemplateData
  assistant.service.ts    # @Injectable({providedIn:'root'}): orquesta los comandos
```

## `assistant.service.ts` (orquestador)

- `mensajes: Signal<AssistantMessage[]>` — semilla con mensajes de bienvenida.
- `interpretar(texto): AssistantIntent` — clasifica en `buscar-por-arroba` / `buscar-template-por-slash` /
  `vacio` / `desconocido` según prefijo.
- `ejecutar(intent): Observable<AssistantResult>` — para `buscar-por-arroba` llama
  `ApiService.listarDocumentos(filter)`; para `buscar-template-por-slash` usa
  `filtrarTemplates()`.
- `filtrarTemplates(texto)` — filtra `TemplateService.template()` (caché de
  `DocumentoPlantillaDTO`) por nombre/código.
- `abrirDocumento(doc)` — `UtilsService.modalWithParams(doc)` para abrir el documento.
- `abrirTemplateDirect(item)` — `Router.navigate(['/list/list', item.id])`.

## Wiring en el shell

- `app.component.ts` importa `AssistantButtonComponent`, `AssistantService`,
  `AssistantDialogComponent` (pronto `AssistantPanelComponent`).
- `app.component.html` renderiza `<app-assistant-button>` y el contenedor del asistente.

## Migración a panel lateral (design.md D1)

El contenedor pasa de `MatDialog` a un panel derecho. Puntos de cambio:

| Archivo | Cambio |
|---------|--------|
| `assistant.service.ts` | `isOpenDialog()` → `isOpenPanel()` (estado abierto/cerrado) |
| `assistant-dialog/assistant-dialog.component.ts` | dejar de recibir `MatDialogRef`; leer estado de `AssistantService` |
| `app.component.html` | reemplazar `<mat-dialog>` por `<mat-sidenav position="end">` o `cdk-overlay` a la derecha |
| `assistant-button/` | toggle de apertura (F9 / click) sin `MatDialog.open` |

## DTOs y modelos

- `assistant.models.ts`: `AssistantMessage`, `AssistantState`, `AssistantIntent`,
  `DocumentSearchResult`, `TemplateSearchResult`, `TemplateData`.
- Tipos de dominio reutilizados: `DocumentoPlantillaDTO`, `PedidoVentaDTO`,
  `PedidoVentaFilterDTO` (desde `app/modules/full/neuron/model/sw42.domain`).
