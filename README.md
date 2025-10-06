
# sgivu-config-repo

Repositorio que almacena las configuraciones YAML del proyecto SGIVU. Está pensado para ser consumido por un Spring Cloud Config Server (o cualquier servidor de configuración que lea desde git/filesystem) y contiene ficheros específicos por servicio y por perfil (por ejemplo: `sgivu-auth-dev.yml`, `sgivu-auth-prod.yml`).

## Propósito

Centralizar las propiedades y configuraciones de los microservicios para facilitar:

- Gestión por entornos (dev / prod / etc.).
- Versionado y auditoría de cambios en la configuración.
- Consumo por un servidor de configuración (Spring Cloud Config) en tiempo de arranque de los servicios.

## Estructura del repositorio

Ficheros de ejemplo (no exhaustivo):

- `sgivu-auth-default.yml` — valores por defecto para el servicio `sgivu-auth`.
- `sgivu-auth-dev.yml` — valores para el entorno de desarrollo.
- `sgivu-auth-prod.yml` — valores para producción.
- `sgivu-discovery-*`, `sgivu-gateway-*`, `sgivu-user-*`, etc. — misma convención por servicio.

Convención recomendada de nombres: `<servicio>-<perfil>.yml` o `<servicio>-default.yml` para valores comunes.

## Cómo usar

1. Clona este repositorio y configura tu Spring Cloud Config Server para apuntar al repositorio git (URL).

    Ejemplo minimo de `application.yml` del Config Server:

    ```yaml
    spring:
    cloud:
        config:
        server:
            git:
            uri: https://github.com/mi-org/sgivu-config-repo.git
            clone-on-start: true
    ```

2. Levanta el Config Server. Sus endpoints expuestos serán algo como:

    - `http://config-server:8888/{application}/{profile}`
    - `http://config-server:8888/sgivu-auth/dev` — devolverá la configuración combinada para `sgivu-auth` y perfil `dev`.

3. Configura los microservicios para apuntar al Config Server al arrancar (propiedades de Bootstrap/Environment):

    ```properties
    spring.cloud.config.uri=http://config-server:8888
    ```

    O usando variables de entorno en Docker/compose.

## Probar localmente

- Levanta un Config Server local que apunte a este repositorio. Por ejemplo, usando el proyecto `sgivu-config` o un proyecto Spring Boot con dependencia `spring-cloud-config-server`.
- Desde un navegador o curl solicite: `http://localhost:8888/sgivu-auth/dev`.

Ejemplo con curl:

```bash
curl http://localhost:8888/sgivu-auth/dev
```

La respuesta debe ser un JSON con las propiedades combinadas (default + perfil específico).

## Añadir o modificar propiedades

1. Cree o edite el archivo apropiado, siguiendo la convención `<servicio>-<perfil>.yml`.
2. Haga commit y push al repositorio.
3. Si su Config Server tiene `monitor`/webhook activado (o Spring Cloud Bus configurado), los servicios pueden refrescar la configuración en caliente. Si no, reinicie el servicio para que recargue la configuración.

## Seguridad y secretos

- Evite almacenar secretos en texto plano en este repositorio. Utilice un gestor de secretos (Vault, AWS Secrets Manager, Azure Key Vault) o cifrado (`spring-cloud-config` soporta encryption/decryption).
- Si debe incluir credenciales de prueba, asegúrese de usarlas solo en ramas privadas o en archivos que no se versionen (por ejemplo, usando herramientas de secret management o variables de entorno en CI/CD).

## Buenas prácticas

- Mantener valores sensibles fuera del repo.
- Documentar cada clave nueva con un comentario en el commit/PR.
- Mantener `*-default.yml` con valores seguros por defecto.
- Usar ramas por entorno o etiquetado semántico si necesita promover cambios entre entornos.

## Integración con CI/CD

- En pipelines, valide cambios de configuración y pruebe que no rompen el arranque de los servicios antes de promover a producción.
- Considere añadir pruebas que arranquen el Config Server con el repo y validen endpoints básicos.

## Licencia

Consulte el fichero `LICENSE` en la raíz del repositorio.
