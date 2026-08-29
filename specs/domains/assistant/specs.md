# Specs — Asistente (ASSISTANT)

> **Front-only** (`d3_front`); no tiene backend propio (reutiliza `documents` §6).

## Diseño

Decisiones de diseño del dominio **front-only** `assistant`. Referencia: `use-cases-front.md`,
`front.md` y los dominios consumidos (`documents`, `authentication`).

### D1. Contenedor: de `MatDialog` a panel lateral derecho

**Decisión:** El asistente deja de renderizarse como `MatDialog` modal
(`AssistantDialogComponent`) y pasa a ser un **panel lateral fijo a la derecha** de la SPA.

- **Antes:** `app.component.html` renderizaba `<app-assistant-button>` y, al abrir, un
  `MatDialog` modal bloqueaba el fondo (overlay + `MatDialogRef`).
- **Ahora:** el botón flotante (`assistant-button`) abre un contenedor tipo `mat-sidenav`
  (o `cdk-overlay` posicionado a la derecha, `position: fixed; right: 0`) que se desliza
  desde el borde derecho. El resto de la app queda visible e interactivo.
- **Comportamiento de apertura/cierre:**
  - Apertura: botón flotante o tecla **F9**.
  - Cierre: botón de cierre del panel, tecla **Esc**, o F9 de nuevo.
  - El estado abierto/cerrado se sigue controlando desde `AssistantService.isOpenDialog()`
    (renombrar a `isOpenPanel()` recomendado en `backlog.md`).
- **Justificación:** permite al operador consultar/abrir documentos y navegar a módulos sin
  perder el contexto de la pantalla actual; mejora la usabilidad frente al modal bloqueante.
- **Layout/Responsive:** en viewport estrecho (< `md`), el panel ocupa `100%` del ancho; en
  desktop ocupa un ancho fijo (p.ej. `380px`) anclado a la derecha con `z-index` sobre el
  contenido pero sin `backdrop` que bloquee clics.

**Riesgo / notas:**
- No usar `MatDialog` (si se mantiene Material, preferir `MatSidenav` dentro del shell o
  `CdkOverlay` con `ConnectedPosition`/`GlobalPositionStrategy` a la derecha).
- Respetar `focar` el input al abrir y devolver el foco al trigger al cerrar (accesibilidad).

### D2. El asistente NO es una IA

**Decisión:** mantener el asistente como un intérprete de comandos basado en prefijos
(`@`, `/`) + búsqueda de documentos. No se integra ningún LLM/endpoint de IA.

- Toda la "inteligencia" es coincidencia por prefijo + filtrado de plantillas en caché.
- Esto se documenta explícitamente para evitar que lectores futuros esperen un backend de IA.

### D3. Dependencias y datos

- El listado de plantillas para `/` viene de `TemplateService.template()` (ya poblado al
  cargar sesión por `user.component.ts` → `GET /template/getTemplates/{profile}`).
- La búsqueda `@` va siempre a `POST /document/getDocuments`; no se cachea (resultados
  frescos por código).
- El avatar mostrado en el panel se lee de `jwtAuth.user()?.imagen` (solo lectura).

