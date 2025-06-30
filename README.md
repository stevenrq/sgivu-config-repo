# SGIVU Configuration Repository

This repository contains externalized configuration files for all SGIVU microservices. It serves as the centralized
configuration store that is consumed by the Spring Cloud Config Server to provide environment-specific configurations
across the entire microservices ecosystem.

## 📖 Documentation

For detailed documentation, please
visit: [https://deepwiki.com/stevenrq/sgivu-config](https://deepwiki.com/stevenrq/sgivu-config)

## 🏗️ Repository Structure

This repository follows the Spring Cloud Config naming convention and contains configuration files organized by service
and environment:

```
sgivu-config-repo/
├── sgivu-auth-default.yml      # Auth service base configuration
├── sgivu-auth-dev.yml          # Auth service development environment
├── sgivu-auth-prod.yml         # Auth service production environment
├── sgivu-discovery-default.yml # Discovery service base configuration
├── sgivu-discovery-dev.yml     # Discovery service development environment
├── sgivu-discovery-prod.yml    # Discovery service production environment
├── sgivu-gateway-default.yml   # Gateway service base configuration
├── sgivu-gateway-dev.yml       # Gateway service development environment
├── sgivu-gateway-prod.yml      # Gateway service production environment
├── sgivu-user-default.yml      # User service base configuration
├── sgivu-user-dev.yml          # User service development environment
└── sgivu-user-prod.yml         # User service production environment
```

## 🚀 Managed Services

This configuration repository manages settings for the following SGIVU microservices:

### 🔐 Authentication Service (sgivu-auth)

- **Port**: 9000 (configurable via PORT environment variable)
- **Features**: OAuth2 Authorization Server, JWT token management
- **Eureka Integration**: Service discovery registration
- **Endpoints**: User service integration, health monitoring

### 🔍 Discovery Service (sgivu-discovery)

- **Port**: 8761
- **Features**: Eureka Server for service registry
- **Configuration**: Standalone mode (no registration with other Eureka instances)
- **Service URL**: http://localhost:8761/eureka/

### 🌐 Gateway Service (sgivu-gateway)

- **Features**: Spring Cloud Gateway with load balancing
- **Routing**: Dynamic routing to microservices via service discovery
- **Circuit Breaker**: Resilience4j integration for fault tolerance
- **Load Balancing**: Automatic load balancing to service instances

### 👤 User Service (sgivu-user)

- **Features**: User management and profile operations
- **Database Integration**: Data persistence layer
- **Security**: Integration with authentication service
- **Business Logic**: User-related business operations

## 📋 Configuration Patterns

### Naming Convention

Files follow the Spring Cloud Config naming pattern:

```
{application-name}-{profile}.yml
```

Where:

- `application-name`: Name of the microservice (sgivu-auth, sgivu-discovery, etc.)
- `profile`: Environment profile (default, dev, prod)

### Environment Profiles

#### Default Profile

- Contains base configuration shared across all environments
- Provides fallback values when environment-specific values are not defined
- Used as the foundation for environment-specific overrides

#### Development Profile (dev)

- Configuration optimized for local development
- Debug settings and development-friendly configurations
- Local service URLs and relaxed security settings

#### Production Profile (prod)

- Production-ready configuration with security hardening
- Performance optimizations and monitoring configurations
- Production service URLs and strict security policies

## 🔧 Configuration Categories

### Service Discovery

- Eureka client configuration for service registration
- Instance identification and health check settings
- Service URL mapping for inter-service communication

### Routing and Gateway

- Dynamic routing rules for the API gateway
- Load balancing configuration
- Circuit breaker patterns for resilience
- Fallback mechanisms for service failures

### Security

- OAuth2 client and server configurations
- JWT token settings and validation
- CORS and security headers configuration

### Monitoring and Observability

- Actuator endpoint exposure
- Health check configurations
- Metrics and tracing settings
- Logging configurations

### Database and Persistence

- Data source configurations
- Connection pool settings
- Transaction management
- Database migration settings

## 🚀 Usage with Config Server

These configuration files are consumed by the SGIVU Config Server:

1. **Config Server Setup**:
   ```yaml
   spring:
     cloud:
       config:
         server:
           git:
             uri: https://github.com/stevenrq/sgivu-config-repo.git
   ```

2. **Client Configuration**:
   ```yaml
   spring:
     config:
       import: configserver:http://localhost:8888
     profiles:
       active: dev  # or prod
   ```

3. **Configuration Retrieval**:
    - Services automatically fetch their configuration on startup
    - Real-time configuration updates via refresh endpoints
    - Environment-specific configuration loading

## 🔄 Configuration Management

### Adding New Configurations

1. Create new configuration files following the naming convention
2. Add service-specific properties and settings
3. Commit and push changes to the repository
4. Configuration Server automatically detects and serves new configurations

### Updating Existing Configurations

1. Modify the appropriate configuration file
2. Test changes in development environment first
3. Commit and push changes
4. Services can refresh configuration without restart:
   ```bash
   POST http://{service-host}:{service-port}/actuator/refresh
   ```

### Environment-Specific Overrides

- Properties in profile-specific files override default values
- Use environment variables for sensitive configurations
- Implement configuration encryption for production secrets

## 🔐 Security Best Practices

### Sensitive Data

- Use encrypted properties for passwords and secrets
- Leverage Spring Cloud Config encryption features
- Store sensitive values as environment variables in production

### Access Control

- Implement proper Git repository access controls
- Use branch protection rules for production configurations
- Audit configuration changes through Git history

### Configuration Validation

- Validate configuration syntax before committing
- Test configuration changes in staging environments
- Use configuration profiles to isolate environments

## 📊 Monitoring Configuration Changes

### Git History

- Track all configuration changes through Git commits
- Use meaningful commit messages for configuration updates
- Tag releases for configuration versions

### Config Server Monitoring

- Monitor configuration retrieval through Config Server logs
- Set up alerts for configuration fetch failures
- Track configuration refresh events across services

## 🤝 Integration

This configuration repository integrates with:

- **SGIVU Config Server** - Serves configurations to all microservices
- **All SGIVU Microservices** - Consumes environment-specific configurations
- **CI/CD Pipelines** - Automated configuration deployment
- **Monitoring Systems** - Configuration change tracking and alerting

## 🚀 Development Workflow

### Local Development

1. Clone this repository alongside the Config Server
2. Ensure Config Server points to this repository
3. Start services with `dev` profile for development configurations

### Production Deployment

1. Update production configuration files
2. Test changes in staging environment
3. Deploy configuration changes before service updates
4. Monitor service health after configuration changes

## 📝 Configuration File Examples

### Basic Service Configuration

```yaml
# Service registration
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/

# Server configuration
server:
  port: ${PORT:8080}

# Management endpoints
management:
  endpoints:
    web:
      exposure:
        include: health, info, refresh
```

### Gateway Routing Configuration

```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: service-route
          uri: lb://service-name
          predicates:
            - Path=/api/service/**
          filters:
            - name: CircuitBreaker
              args:
                name: serviceCircuitBreaker
```

## 📝 License

This project is licensed under the terms specified in the LICENSE file.

## 👥 Contributing

### Configuration Changes

1. Fork the repository
2. Create a feature branch for configuration changes
3. Test changes thoroughly in development environment
4. Submit a pull request with detailed change description
5. Ensure all services are compatible with configuration updates

### Best Practices

- Follow the established naming conventions
- Document complex configuration changes
- Test configuration changes across all affected services
- Use semantic versioning for major configuration updates

---

For more detailed information about the SGIVU Config Server and configuration management,
visit: [https://deepwiki.com/stevenrq/sgivu-config](https://deepwiki.com/stevenrq/sgivu-config)
