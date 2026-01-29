# sgivu-config-repo - SGIVU

## Descripción

Repositorio centralizado de los archivos YAML que el Spring Cloud Config Server expone a los microservicios de SGIVU. Permite versionar la configuración compartida y mantener un historial claro de cambios por entorno.

## Arquitectura y Rol

- Fuente de verdad para `sgivu-config`, el Config Server.
- Permite que `sgivu-docker-compose` y despliegues en EC2 obtengan configuraciones coherentes para cada servicio.
- Separa la lógica de negocio del código de configuración por entorno (`default`, `dev`, `prod`).

Cada archivo corresponde a la configuración de un microservicio y entorno específicos. Spring Cloud Config usa el nombre base (`{application}.yml`) para la configuración común y los sufijos (`-dev`, `-prod`) para resolver la configuración según el profile activo.

## Configuración

- Usa el archivo base (sin sufijo de perfil) para valores comunes y sobrescribe solo diferencias en `dev` o `prod`.
- Variables sensibles deben definirse con placeholders `${VAR_NAME:default}` y resolverse desde `.env` o secretos externos.
- Respeta las claves de Spring (`spring.application.name`, `server.port`, etc.) para evitar conflictos.

## Ejecución Local

Este repositorio no se ejecuta por sí mismo. Para consumir la configuración:

```bash
docker compose -f ../sgivu/infra/compose/sgivu-docker-compose/docker-compose.yml up -d sgivu-config
curl http://localhost:8888/sgivu-auth/dev
```

Si los servicios ya están levantados con `sgivu-docker-compose`, tras actualizar este repo reinicia el contenedor del Config Server o usa `/actuator/refresh` en cada microservicio que lo soporte.

## Seguridad

- No guardes secretos reales en los YAML; usa variables de entorno o gestores de secretos.
- Mantén el repositorio privado con permisos adecuados para el Config Server.

## Dependencias

- Consumido por `sgivu-config`, que sirve propiedades al resto de microservicios.

## Convenciones

- Sincroniza puertos, URLs y credenciales entre entornos.
- Documenta en PRs cambios que afecten comportamiento crítico.
- Valida los YAML con `yamllint` antes de fusionar.
