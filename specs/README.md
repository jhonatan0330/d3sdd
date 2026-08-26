# SDD — Software D3

Spec-Driven Development para el ecosistema D3, que agrupa dos proyectos:

| Proyecto | Rol | Stack |
|----------|-----|-------|
| `d3_front` | Frontend SPA | Angular 22.1, TypeScript, Tailwind |
| `d3brain`  | Backend REST  | Spring Boot 3.5, Java 17, Gradle, MyBatis |

Esta carpeta `specs/` es la **fuente de verdad de la especificación**. El código de ambos
proyectos se implementa a partir de lo documentado aquí, no al revés.

## Principio rector: el acuerdo de API es único

El contrato de API vive en [`contract/`](contract/api-contract.md) y su forma máyquina-legible
[`contract/openapi.yaml`](contract/openapi.yaml). Tanto `d3_front` como `d3brain` deben
cumplirlo. Cualquier cambio en un endpoint, DTO, auth o formato de error se documenta AHÍ
primero, y luego se implementa en los dos lados. Así front y back nunca se desincronizan.

## Estructura

```
specs/                       # SDD (desarrolladores) — fuente de verdad técnica
  README.md                 # Este archivo (proceso + índice)
  contract/
    api-contract.md         # Acuerdo de API human-readable (fuente de verdad)
    openapi.yaml            # Contrato formal OpenAPI 3.0
  domains/
    <dominio>/
      use-cases.md          # Catálogo de casos de uso (CU-xxx) — compartido
      requirements.md       # Requisitos (formato EARS) + reglas de negocio
      design.md             # Decisiones de diseño (compartidas y por lado)
      tasks.md              # Desglose back/front (T-xxx)
      front.md              # (opcional) detalles de implementación del front
      back.md               # (opcional) detalles de implementación del back
  backlog.md                # Nuevos casos de uso propuestos (por priorizar)

docs/                        # Manual de uso del front (usuarios finales)
  user-guide/
    index.md                # Índice y mapa de la app
    login.md                # Ingreso (normal / Google / recuperación)
    navigation.md           # Navegación y módulos
    <sección>.md            # Una por dominio visible en la UI
```

## Convención de separación front/back

- **Contract siempre compartido y único** en `contract/` (ambos proyectos lo respetan).
- **Casos de uso compartidos** en `domains/<d>/use-cases.md` (una funcionalidad = un CU).
- **Implementación por lado**: en `tasks.md` se separan ítems back (`d3brain`) y front
  (`d3_front`). Cuando un dominio crece, sus detalles de diseño/implementación se mueven a
  `front.md` / `back.md` **dentro del mismo dominio**, para no perder trazabilidad con el CU.
- **No duplicar el contract** en dos carpetas (evita deriva entre proyectos).

## Manual de uso (docs/user-guide)

Documentación orientada a **usuarios finales** de la SPA Angular. Complementa (no reemplaza)
el SDD: cada sección enlaza al caso de uso que implementa. Ver
[`docs/user-guide/index.md`](../docs/user-guide/index.md).

## Proceso (pasos de SDD)

Para **cada nueva funcionalidad** (o bug que cambie contrato):

1. **Caso de uso** — crear/actualizar `use-cases.md` del dominio con actor, precondición,
   pasos, postcondición y errores.
2. **Contrato** — si la funcionalidad toca la API, actualizar `contract/api-contract.md` y
   `contract/openapi.yaml`. Marcar el cambio como *breaking* o *no-breaking*.
3. **Requisitos** — registrar requisitos en `requirements.md`.
4. **Diseño** — anotar decisiones en `design.md` (p.ej. migración a JWT).
5. **Tasks** — desglosar en `tasks.md` con ítems verificables (back y front por separado).
6. **Implementar** — back (`d3brain`) y front (`d3_front`). Verificar:
   - Back: `./gradlew.bat build -x test` (el compilador Java es la verificación).
   - Front: `npm run build` y `npx tsc -p tsconfig.app.json --noEmit`.
7. **Cerrar** — tachar el ítem en `tasks.md` y, si aplica, mover el CU de `backlog.md` a su
   dominio.

## Convenciones de documentación

- IDs de caso de uso: `CU-<dominio>-<nnn>` (ej. `CU-AUTH-001`).
- Ítems de task: `T-<dominio>-<nnn>` con estado `[ ]` / `[x]`.
- Los DTOs se nombran igual en back (`com.softure...domain`) y front (`sw42.domain`).
- El contrato de API NO tiene versionado formal todavía; los cambios breaking se listan en
  `contract/api-contract.md` > "Cambios y versionado".

## Estado actual de documentación

| Dominio | use-cases | contract | tasks |
|---------|:---------:|:--------:|:-----:|
| authentication | ✅ inicial | ✅ | ✅ |
| documents | ⏳ pendiente | (en contract) | ⏳ |
| tasks | ⏳ pendiente | (en contract) | ⏳ |
| accounting | ⏳ pendiente | (en contract) | ⏳ |
| massive | ⏳ pendiente | (en contract) | ⏳ |
| notifications | ⏳ pendiente | (en contract) | ⏳ |
| config-forms | ⏳ pendiente | (en contract) | ⏳ |
