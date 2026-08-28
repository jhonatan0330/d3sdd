# Casos de uso — Asistente (ASSISTANT)

Dominio: `assistant`. **Front-only** (no tiene backend propio; consume los dominios
`documents` y `authentication`). Contrato asociado: reutiliza
[`contract/api-contract.md`](../contract/api-contract.md) §6 (documents).

> El asistente **NO es una IA**: es un buscador/navegador de comandos dentro de la SPA.
> Interpreta dos prefijos (`@` y `/`) y opera sobre datos ya cargados (plantillas) o vía
> endpoints existentes de `documents`.

| ID | Nombre | Actor | Estado |
|----|--------|-------|--------|
| CU-ASSISTANT-001 | Abrir/cerrar el asistente | Usuario | ✅ documentado |
| CU-ASSISTANT-002 | Buscar documento por código (`@código`) | Usuario | ✅ documentado |
| CU-ASSISTANT-003 | Navegar a módulo/plantilla por nombre (`/texto`) | Usuario | ✅ documentado |
| CU-ASSISTANT-004 | Mostrar ayuda / intención desconocida | Sistema (SPA) | ✅ documentado |

---

## CU-ASSISTANT-001 — Abrir/cerrar el asistente

- **Actor:** Usuario de la SPA.
- **Precondición:** Sesión activa.
- **Pasos:**
  1. El usuario pulsa el botón flotante o la tecla **F9** (PC).
  2. Se despliega el panel del asistente. **Cambio de diseño (ver `design.md` D1):** el
     asistente dejó de ser un `MatDialog` modal para convertirse en un **panel lateral
     derecho** (`mat-sidenav` / `cdk-overlay` anclado a la derecha), permitiendo seguir
     viendo y usando el resto de la app mientras está abierto.
  3. El usuario cierra con la tecla **Esc**, el botón de cierre del panel, o volviendo a
     pulsar F9.
- **Postcondición:** El panel se muestra/oculta sin bloquear el resto de la interfaz.
- **Errores:** —

---

## CU-ASSISTANT-002 — Buscar documento por código (`@código`)

- **Actor:** Usuario.
- **Pasos:**
  1. El usuario escribe `@<código>` (p.ej. `@PV-0001`).
  2. `AssistantService.interpretar()` detecta la intención `buscar-por-arroba`.
  3. `ejecutar()` llama a `ApiService.listarDocumentos(filter)` →
     **`POST /document/getDocuments`** (dominio `documents`, §6).
  4. Si hay **un** match → se abre el documento con `UtilsService.modalWithParams(doc)`.
  5. Si hay **varios** → se listan para que el usuario elija.
- **Postcondición:** Documento abierto o lista de coincidencias mostrada.
- **Errores:** Sin coincidencias → mensaje "No se encontró ningún documento".

---

## CU-ASSISTANT-003 — Navegar a módulo/plantilla por nombre (`/texto`)

- **Actor:** Usuario.
- **Pasos:**
  1. El usuario escribe `/<texto>` (p.ej. `/pedido`).
  2. `filtrarTemplates()` filtra en el cliente la lista en caché `TemplateService.template()`
     (no hay HTTP; son `DocumentoPlantillaDTO`).
  3. Si hay **un** match → navega a `/list/list/{templateId}`.
  4. Si hay **varios** → lista los módulos/plantillas coincidentes.
- **Postcondición:** Navegación a la plantilla o lista de coincidencias.
- **Errores:** Sin coincidencias → mensaje de ayuda.

---

## CU-ASSISTANT-004 — Mostrar ayuda / intención desconocida

- **Actor:** Sistema (SPA).
- **Pasos:**
  1. Texto sin prefijo reconocido o comando `?`/`help` → intención `desconocido`.
  2. El asistente muestra un mensaje con la sintaxis soportada (`@código`, `/texto`).
- **Postcondición:** Usuario informado de las capacidades.

---

## Servicios consumidos (cross-module)

| Servicio (front) | Módulo que lo provee | Dominio backend | Endpoint/contrato |
|------------------|----------------------|-----------------|-------------------|
| `ApiService` | `neuron` | documents | `POST /document/getDocuments` (§6) |
| `TemplateService` | `neuron` | documents | `GET /template/getTemplates/{profile}` (§6, caché) |
| `UtilsService` | `neuron` | documents / document-transition | modal dinámico `modalWithParams` |
| `LoginService` | `authentication` | authentication | solo lectura de avatar (`jwtAuth.user()?.imagen`) |
| `Router` | Angular | — | navegación `/list/list/{id}` |
