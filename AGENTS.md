# Guía del Repositorio

## Estructura del Proyecto y Módulos

- YAML en raíz: `sgivu-<servicio>-<perfil>.yml` (auth, client, discovery, gateway, purchase-sale, user, vehicle; perfiles default/dev/prod). Pon valores comunes en `-default` y sobrescribe solo lo que cambia por entorno.
- Diagramas en `../sgivu/docs/diagrams/01-system-architecture.puml`; actualízalos si cambian componentes o dependencias.
- Al agregar un servicio, respeta el patrón y crea al menos `-default` para evitar faltas de configuración al arrancar.

## Comandos de Build, Pruebas y Desarrollo

- `yamllint .` — valida sintaxis e indentación antes de un PR.
- `docker compose -f ../sgivu/infra/compose/sgivu-docker-compose/docker-compose.yml up -d sgivu-config` — levanta un Config Server local apuntando a este repo.
- `curl http://localhost:8888/sgivu-auth/dev` (cambia servicio/perfil) — smoke test para confirmar que sirve las propiedades esperadas.
- `plantuml ../sgivu/docs/diagrams/01-system-architecture.puml` — regenera el mapa si tienes PlantUML.

## Estilo de Código y Convenciones

- YAML: indentación de 2 espacios, sin tabs; claves en minúsculas; convención de Spring (`spring.application.name`, `server.port`, `spring.datasource.*`).
- Tokens o secretos siempre como placeholders `${VAR_NAME:default}`; no se versionan secretos reales.
- Mantén URLs, puertos y timeouts alineados entre entornos; sobrescribe solo si un entorno difiere.
- Al agregar propiedades, refleja las claves en `-default`, `-dev` y `-prod` con valores específicos para evitar ausencias.

## Guía de Pruebas

- Ejecuta `yamllint .` y un smoke test del Config Server (`curl` anterior) para los servicios tocados.
- En cambios transversales (rutas del gateway, discovery), valida hosts y puertos coherentes por perfil en servicios dependientes.
- Si impacta clientes (CORS, JWT, timeouts), documenta en el PR el efecto esperado y si requiere refresh o reinicio de consumidores.

## Commits y Pull Requests

- Mensajes de commit: formato breve e imperativo con servicio y perfil, p. ej., `auth-dev: ajustar expiración JWT` o `gateway-default: agregar rate limits`.
- El PR debe listar servicios/perfiles afectados, motivo breve y notas de despliegue (reinicio del Config Server o `/actuator/refresh`). Vincula issues cuando existan.
- Para cambios sensibles (seguridad, red), añade diffs o fragmentos relevantes; no se requieren capturas porque este repo es solo configuración.

## Seguridad y Configuración

- No incluyas secretos; usa variables de entorno o almacenes externos.
- Reduce defaults permisivos (CORS, niveles de log) en `-prod`; mantén `-dev` amigable pero coherente.
- Alinea nombres de variables de entorno con las usadas en los YAML (`DEV_*`, `PROD_*`, `SGIVU_*`, `EUREKA_URL`, `PORT`) para facilitar la automatización.

## Notas Específicas del Servicio

- Replica cambios transversales en `-default`, `-dev` y `-prod` para evitar configuraciones incompletas al arrancar servicios.
