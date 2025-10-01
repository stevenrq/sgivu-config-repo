# Repositorio de Configuraciones del Ecosistema sgivu

Este repositorio centraliza la gestión de las configuraciones para todos los microservicios del ecosistema sgivu.

## Descripción

Aquí se almacenan los archivos de configuración `.yml` que son consumidos por los servicios en los diferentes entornos (desarrollo, producción, etc.). El objetivo es tener un único punto de verdad para las configuraciones, facilitando su mantenimiento y versionado.

## Convención de Nombres

Los archivos de configuración siguen una convención estricta para asegurar la correcta asignación a cada servicio y entorno:

`sgivu-<nombre-del-servicio>-<entorno>.yml`

- **sgivu-**: Prefijo estándar para todos los servicios del proyecto.
- Identificador del microservicio (e.g., `auth`, `user`, `gateway`).
- El entorno de despliegue al que aplica la configuración.
  - `default`: Configuración base o por defecto que se aplica a todos los entornos.
  - `dev`: Configuración específica para el entorno de desarrollo.
  - `prod`: Configuración específica para el entorno de producción.

## Uso

Estos archivos de configuración son servidos por un servicio de configuración central (como Spring Cloud Config Server) a los microservicios correspondientes en el momento de su arranque.

Cada microservicio debe estar configurado para conectarse al servidor de configuración y solicitar el archivo que le corresponde según su nombre y el perfil de entorno activo.

## Contribuciones

Para añadir o modificar una configuración:

1. **Identifica el archivo correcto**: Localiza el archivo `.yml` del servicio y entorno que deseas modificar.
2. **Realiza los cambios**: Edita el archivo siguiendo el formato YAML.
3. **Documenta los cambios**: Asegúrate de que la razón del cambio quede clara en el mensaje de commit.
4. **Crea un Pull Request**: Envía tus cambios para revisión.
