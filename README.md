# 🧩 SGIVU - sgivu-config-repo

## 📘 Descripción

Repositorio centralizado de los archivos YAML que el Spring Cloud Config Server expone a los microservicios de SGIVU. Permite versionar la configuración compartida y mantener un historial claro de cambios por entorno.

## 🧱 Rol en la Arquitectura

- Sirve como fuente de verdad para `sgivu-config`, el microservicio Config Server.
- Facilita que `sgivu-docker-compose` y los despliegues en EC2 obtengan configuraciones coherentes para cada servicio.
- Permite separar la lógica de negocio del código de configuración sensible a cada entorno (`default`, `dev`, `prod`).

## ⚙️ Estructura del Repositorio

```text
├── sgivu-auth-default.yml
├── sgivu-auth-dev.yml
├── sgivu-auth-prod.yml
├── sgivu-client-default.yml
├── sgivu-client-dev.yml
├── sgivu-client-prod.yml
├── sgivu-discovery-default.yml
├── sgivu-discovery-dev.yml
├── sgivu-discovery-prod.yml
├── sgivu-gateway-default.yml
├── sgivu-gateway-dev.yml
├── sgivu-gateway-prod.yml
├── sgivu-user-default.yml
├── sgivu-user-dev.yml
└── sgivu-user-prod.yml
```

Cada archivo corresponde a la configuración de un microservicio y entorno específicos. Spring Cloud Config utiliza el sufijo (`-dev`, `-prod`) para resolver la configuración según el profile activo.

## 🚀 Ejecución

Este repositorio no se ejecuta de forma independiente. Para consumir la configuración:

```bash
# 1. Arranca el Config Server
docker compose -f ../sgivu-config/docker-compose.yml up -d config-server

# 2. Verifica que los microservicios puedan leer la configuración
curl http://localhost:8888/sgivu-auth/dev
```

Si los servicios ya están levantados mediante `sgivu-docker-compose`, al actualizar este repositorio basta con reiniciar el contenedor del Config Server o invocar el endpoint `/actuator/refresh` en cada microservicio que soporte refresh.

## 🔧 Configuración

- Variables de entorno sensibles (tokens, contraseñas) deben definirse mediante placeholders (`${VAR_NAME:default}`) y resolverse desde `.env` o secretos externos.
- Usa el perfil `default` para valores comunes y sobreescribe únicamente lo necesario en `dev` o `prod`.
- Verifica que las claves respeten la convención de Spring (`spring.application.name`, `server.port`, etc.) para evitar conflictos en el Config Server.

## 🌐 Interacción con Otros Repositorios

- `sgivu-config` lee directamente de `sgivu-config-repo` (rama principal).
- `sgivu-docker-compose` monta este repositorio en el contenedor del Config Server para cargar configuraciones al iniciar.
- Cada microservicio (`sgivu-auth`, `sgivu-client`, `sgivu-discovery`, `sgivu-gateway`, `sgivu-user`) consume estos YAML según su perfil activo.

## 🧮 Buenas Prácticas

- Mantener sincronizados puertos, URLs y credenciales entre entornos.
- Evitar incluir secretos reales en los YAML; utilizar variables de entorno o servicios de secretos.
- Documentar en los PR cualquier cambio de configuración que afecte comportamientos críticos.
- Validar los YAML con herramientas como `yamllint` antes de unir cambios.

## ✨ Autor

- **Steven Ricardo Quiñones**
- **Año:** 2025
