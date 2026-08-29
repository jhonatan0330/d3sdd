# SDD — Software D3

Spec-Driven Development para el ecosistema D3, que agrupa dos proyectos:

| Proyecto | Rol | Stack |
|----------|-----|-------|
| `d3_front` | Frontend SPA | Angular 22.1, TypeScript, Tailwind |
| `d3brain`  | Backend REST  | Spring Boot 3.5, Java 17, Gradle, MyBatis |

Esta carpeta `specs/` es la **fuente de verdad de la especificación**. El código de ambos
proyectos se implementa a partir de lo documentado aquí, no al revés.

## Principio rector: el acuerdo de API es único

El contrato de API vive en [`contract.md`](contract.md) y su forma máquina-legible
[`openapi.yaml`](openapi.yaml). Tanto `d3_front` como `d3brain` deben
cumplirlo. Cualquier cambio en un endpoint, DTO, auth o formato de error se documenta AHÍ
primero, y luego se implementa en los dos lados. Así front y back nunca se desincronizan.

## Estructura

```
specs/                       # SDD (desarrolladores) — fuente de verdad técnica
  README.md                 # Este archivo (proceso + índice)
  architecture.md           # Estándares globales (paquetes, APIs, naming, seguridad)
  backlog.md                # Backlog consolidado: tasks + nuevos casos de uso
  contract.md               # Acuerdo de API human-readable (fuente de verdad)
  domains/
    <dominio>/
      use-cases-back.md          # Casos de uso — capa backend (d3brain): contratos/endpoints
      use-cases-front.md         # (solo si aplica) Casos de uso — capa frontend (d3_front): pasos de UI
      specs.md              # Requisitos + diseño consolidados
  backlog-strategies/       # Documentación de estrategias/arquitectura (ligadas a backlog)
  openapi.yaml              # Contrato formal OpenAPI 3.0

docs/                        # Manual de uso del front (usuarios finales)
  user-guide/
    index.md                # Índice y mapa de la app
    login.md                # Ingreso (normal / Google / recuperación)
    navigation.md           # Navegación y módulos
    <sección>.md            # Una por dominio visible en la UI
```

## Convención de separación front/back

- **Contract siempre compartido y único** en la raíz de `specs/` (ambos proyectos lo respetan).
- **Casos de uso por capa**: la parte backend (contratos/endpoints) va en
  `domains/<d>/use-cases-back.md`; la parte frontend (pasos de UI) va en
  `domains/<d>/use-cases-front.md` cuando el dominio tiene interacción de usuario.
  Dominios puramente backend (ej. `fe`, `accounting`) solo tienen `use-cases-back.md`;
  dominios front-only (ej. `assistant`) solo tienen `use-cases-front.md`.
  Cada archivo repite la tabla de CUs; solo varían los pasos por capa.
- **Implementación por lado**: las tareas se consolidan en `backlog.md` (archivo único).
- **No duplicar el contract** en dos carpetas (evita deriva entre proyectos).

## Manual de uso (docs/user-guide)

Documentación orientada a **usuarios finales** de la SPA Angular. Complementa (no reemplaza)
el SDD: cada sección enlaza al caso de uso que implementa. Ver
[`docs/user-guide/index.md`](../docs/user-guide/index.md).

## Proceso (pasos de SDD)

Para **cada nueva funcionalidad** (o bug que cambie contrato):

1. **Specs** — registrar requisitos y decisiones de diseño en `specs.md`.
2. **Caso de uso** — crear/actualizar `use-cases-back.md` (capa backend: endpoints/contrato) y,
   si hay UI, `use-cases-front.md` (capa frontend: pasos de UI) del dominio, con actor,
   precondición, pasos, postcondición y errores.
3. **Contrato** — si la funcionalidad toca la API, actualizar `contract.md` y
   `openapi.yaml`. Marcar el cambio como *breaking* o *no-breaking*.
4. **Tasks** — desglosar en `backlog.md` con ítems verificables (back y front por separado).
5. **Implementar** — back (`d3brain`) y front (`d3_front`). Verificar:
   - Back: `./gradlew.bat build -x test` (el compilador Java es la verificación).
   - Front: `npm run build` y `npx tsc -p tsconfig.app.json --noEmit`.
6. **Cerrar** — tachar el ítem en `backlog.md`.

## Convenciones de documentación

- IDs de caso de uso: `CU-<dominio>-<nnn>` (ej. `CU-AUTH-001`).
- Ítems de task: `T-<dominio>-<nnn>` con estado `[ ]` / `[x]`.
- Los DTOs se nombran igual en back (`d3...domain`) y front (`sw42.domain`).
- El contrato de API NO tiene versionado formal todavía; los cambios breaking se listan en
  `contract.md` > "Cambios y versionado".
- Para estándares globales (paquetes, APIs, seguridad, etc.), ver [`architecture.md`](architecture.md).

## Estrategias (specs/backlog-strategies/)

Carpeta para documentación de estrategias y decisiones arquitectónicas. Cada archivo
en esta carpeta se relaciona con un ítem del backlog.

**Uso:**
- Cuando una tarea del backlog requiere una estrategia detallada, se crea un archivo
  en `backlog-strategies/` con el formato `ARCH-xxx-<nombre>.md`.
- El archivo se referencia desde el backlog y viceversa.
- Ejemplo: `ARCH-001-package-rename.md` documenta la estrategia de renombrado de paquetes
  y se relaciona con `ARCH-001` en el backlog.

## Estado actual de documentación

Para el inventario completo de dominios y su estado detallado, ver [`domains.md`](domains.md).
