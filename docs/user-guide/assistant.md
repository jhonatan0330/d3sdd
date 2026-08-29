# Asistente (panel de comandos)

> El asistente **no es una inteligencia artificial**: es un buscador/navegador de comandos
> dentro de la aplicación. Se abre como un **panel lateral a la derecha** (ya no es un modal).

## Pantalla / activación

- Botón flotante en la esquina inferior derecha, o tecla **F9** (PC).
- Para cerrar: botón de cierre del panel, tecla **Esc**, o **F9** de nuevo.
- Mientras está abierto, el resto de la pantalla queda visible e interactivo.

## Comandos disponibles

| Comando | Qué hace |
|---------|----------|
| `@<código>` | Busca un documento por su código (p.ej. `@PV-0001`). Si hay uno, lo abre; si hay varios, los lista. |
| `/<texto>` | Busca un módulo/plantilla por nombre o código y navega a él (p.ej. `/pedido`). |
| `?` o `help` | Muestra la ayuda / sintaxis soportada. |

## Pasos de uso

1. Pulsa **F9** o el botón flotante para abrir el panel derecho.
2. Escribe `@PV-0001` y presiona Enter.
3. El sistema abre el documento encontrado (o lista las coincidencias).
4. Para ir a un módulo, escribe `/pedido` y elige la plantilla sugerida.
5. Cierra el panel con **Esc** o el botón de cierre.

> Nota: la búsqueda de módulos (`/`) usa las plantillas que ya tienes cargadas; la búsqueda
> de documentos (`@`) consulta el servidor en ese momento.

## Casos de uso relacionados (SDD)

- [CU-ASSISTANT-001..004](../../specs/domains/assistant/use-cases-back.md)
- Consume los dominios `documents` (búsqueda de documentos / plantillas) y `authentication`
  (solo avatar del usuario).

