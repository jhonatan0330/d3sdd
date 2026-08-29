# Specs — Web Services (WS)

## Requisitos

### Funcionales
- R-WS-001: Definir y copiar web services externos.
- R-WS-002: Ejecutar web services con preparación de parámetros (`WebServiceCallPrepare`).

### No funcionales
- NF-WS-001: Cliente HTTP configurado (`WebClientConfig`).

## Diseño

### Paquetes (ARCH-001, realizado)
- `d3.webservice`

### Componentes
- `WebServiceController` (`/webservice`): `copy`.
- `WebServiceSvc`, `WebServiceEjecucionSvc`, `WebServiceExecuteAPI`, `WebServiceCopyAPI`.
- `WebClientConfig`: cliente para invocación externa.

### DTOs
- `WebServiceDTO`, `WebServiceEjecucionDTO`, `WebServiceFilterDTO`, `WebServiceEjecucionFilterDTO`.
