# SGIVU - Instrucciones para Agentes de IA

Este archivo proporciona orientación al trabajar con el código de este repositorio. El repositorio principal de la plataforma es [`sgivu`](https://github.com/stevenrq/sgivu).

## Qué contiene este repositorio

Configuración YAML centralizada para la plataforma de microservicios SGIVU, consumida por un Spring Cloud Config Server (`sgivu-config`). Este repositorio no contiene código de aplicación, ni sistema de construcción ni tests — solo archivos de configuración.

## Convención de nombres de archivos

Cada microservicio tiene hasta tres archivos siguiendo la resolución de Spring Cloud Config:

- `{service}.yml` — configuración compartida/por defecto
- `{service}-dev.yml` — sobrescrituras para el perfil de desarrollo
- `{service}-prod.yml` — sobrescrituras para el perfil de producción

Los archivos de perfil solo deben sobrescribir lo que difiere del archivo base.

## Servicios y puertos por defecto

| Servicio            | Puerto | Rol                                                         |
| ------------------- | ------ | ----------------------------------------------------------- |
| sgivu-discovery     | 8761   | Registro de servicios Eureka                                |
| sgivu-gateway       | 8080   | API gateway (cliente OAuth2, sesiones en Redis)             |
| sgivu-auth          | 9000   | Servidor de autorización (OAuth2/OIDC, sesiones JDBC)       |
| sgivu-user          | 8081   | Gestión de usuarios                                         |
| sgivu-client        | 8082   | Gestión de clientes                                         |
| sgivu-vehicle       | 8083   | Gestión de vehículos (AWS S3 para imágenes)                 |
| sgivu-purchase-sale | 8084   | Transacciones de compra/venta (caché de dashboard en Redis) |

## Infraestructura compartida entre servicios

- **Eureka**: todos los servicios se registran en `sgivu-discovery` en `http://sgivu-discovery:8761/eureka`
- **PostgreSQL**: los servicios de datos usan Flyway para migraciones de esquema
- **Redis**: gateway (sesiones) y purchase-sale (caché de dashboard) usan Redis; la conexión está separada por perfil (`-dev.yml` / `-prod.yml`)
- **OAuth2**: `sgivu-auth` es el proveedor OIDC; otros servicios lo referencian vía `services.map.sgivu-auth`

## Validación

Validar YAML con `yamllint` antes de mergear:

```bash
yamllint *.yml
```

## Valores sensibles

Todos los secretos usan `${VAR_NAME}` o `${VAR_NAME:default}` como placeholders resueltos desde variables de entorno o archivos `.env` en tiempo de ejecución. Nunca commitear secretos reales.

## Probar la configuración localmente

Este repositorio no se ejecuta de forma independiente. Para verificar cambios:

```bash
docker compose -f ../sgivu/infra/compose/sgivu-docker-compose/docker-compose.yml up -d sgivu-config
curl http://localhost:8888/sgivu-auth/dev
```

## Sincronización de Documentación

La documentación del proyecto en `sgivu/docs/` describe el comportamiento real del sistema. Cuando cambies la configuración de un servicio en este repositorio, actualiza también los archivos `.mdx` afectados si existe documentación explícita para ese aspecto.

**Casos comunes que requieren actualización de docs**:

- Cambiar puertos, URLs base o nombres de servicio → `docs/infrastructure/**`, `docs/services/**`
- Cambiar configuración Flyway (locations, perfiles) → `docs/infrastructure/docker-compose.mdx`, `docs/infrastructure/deployment.mdx`
- Cambiar variables de entorno documentadas → `docs/config/environments/variables.mdx`
- Cambiar configuración de Redis, OAuth2 o seguridad → `docs/security/**`

## Documentación

Documentación completa del proyecto: <https://sgivu.mintlify.app> (fuente en `sgivu/docs`, construida con Mintlify).
