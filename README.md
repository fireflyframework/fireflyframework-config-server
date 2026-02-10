# fireflyframework-config-server
    
[![CI](https://github.com/fireflyframework/fireflyframework-config-server/actions/workflows/ci.yml/badge.svg)](https://github.com/fireflyframework/fireflyframework-config-server/actions/workflows/ci.yml)

Firefly Framework Config Server - Centralized configuration management for Firefly Framework microservices.


## Project Description

This project implements a **Spring Cloud Config Server** that centralizes configuration management for distributed applications. It uses the `native` profile to serve configuration files stored locally in an organized hierarchical structure.

## Architecture and Structure

### Directory Structure

```
src/main/resources/config/
├── common/                     # All applications of the common layer
│   ├── application.yaml        # Common base configuration
│   ├── application-dev.yaml    # Common configuration for development
│   ├── application-pre.yaml    # Common configuration for pre-production
│   └── application-pro.yaml    # Common configuration for production
├── core/                       # Applications of the core layer
│   ├── application.yaml        # Core base configuration
│   ├── application-dev.yaml    # Core configuration for development
│   ├── application-pre.yaml    # Core configuration for pre-production
│   └── application-pro.yaml    # Core configuration for production
└── domain/                     # Applications of the domain layer
    ├── distributor-domain-branding/
    │   ├── distributor-domain-branding.yaml
    │   ├── distributor-domain-branding-dev.yaml
    │   ├── distributor-domain-branding-pre.yaml
    │   └── distributor-domain-branding-pro.yaml
    ├── domain-endpoints-dev.yaml
    ├── domain-endpoints-pre.yaml
    └── domain-endpoints-pro.yaml
```

### Configuration Hierarchy

The server is configured to search for configurations in the following priority order:

1. **Common**: All applications of the common layer
2. **Core**: Applications of the core layer
3. **Domain**: Applications of the domain layer
4. **Application-specific**: Specific configurations using the `{application}` pattern

## Server Configuration

### `application.yml` File

```yaml
server:
  port: 8888                    # Config Server port

spring:
  profiles:
    active: native              # Native profile for local files
  cloud:
    config:
      server:
        native:
          search-locations: >   # Hierarchical search locations
            classpath:/config/common,
            classpath:/config/core,
            classpath:/config/domain,
            classpath:/config/common/{application},
            classpath:/config/core/{application},
            classpath:/config/domain/{application}
```

### Environment Profiles

The system supports three main environments:
- **dev**: Development
- **pre**: Pre-production  
- **pro**: Production

## How It Works

### 1. Configuration Resolution

When a client application requests its configuration, the Config Server:

1. Searches for files that match the application name
2. Applies the location hierarchy (common → core → domain)
3. Combines configurations according to the active profile
4. Returns the consolidated configuration

### 2. File Naming Patterns

- `application.yaml`: Base configuration
- `application-{profile}.yaml`: Profile-specific configuration
- `{application-name}.yaml`: Application-specific configuration
- `{application-name}-{profile}.yaml`: Application and profile-specific configuration

### 3. Configuration Examples

#### Common Layer
```yaml
# common/application.yaml
endpoints:
  distributor-mgmt: http://localhost:8082
```

#### Domain Layer
```yaml
# domain/distributor-domain-branding/distributor-domain-branding.yaml
spring:
  application:
    version: 1.0.0
    description: Distributor Domain Branding Layer Application

firefly:
  cqrs:
    enabled: true
  eda:
    enabled: true
    default-publisher-type: KAFKA

server:
  port: ${SERVER_PORT:8080}
```

## How to Run the Server

### Prerequisites
- Java 21 or higher
- Maven 3.6+

### Execution

1. **Compile the project:**
   ```bash
   mvn clean compile
   ```

2. **Run the server:**
   ```bash
   mvn spring-boot:run
   ```

3. **Run with specific profile:**
   ```bash
   mvn spring-boot:run -Dspring-boot.run.profiles=dev
   ```

### Verification

The server will be available at: `http://localhost:8888`

## How to Access Configurations

### Configuration Endpoints

The Config Server exposes the following URLs to access configurations:

#### URL Format
```
http://localhost:8888/{application}/{profile}
http://localhost:8888/{application}/{profile}/{label}
http://localhost:8888/{application}-{profile}.json
http://localhost:8888/{application}-{profile}.yaml
http://localhost:8888/{application}-{profile}.properties
```

#### Access Examples

1. **Specific application configuration:**
   ```
   GET http://localhost:8888/distributor-domain-branding/dev
   ```

2. **Configuration in YAML format:**
   ```
   GET http://localhost:8888/distributor-domain-branding-dev.yaml
   ```

3. **Common configuration for development:**
   ```
   GET http://localhost:8888/application/dev
   ```

### Server Response

The server returns a JSON response with the following structure:
```json
{
  "name": "distributor-domain-branding",
  "profiles": ["dev"],
  "label": null,
  "version": null,
  "state": null,
  "propertySources": [
    {
      "name": "classpath:/config/domain/distributor-domain-branding/distributor-domain-branding-dev.yaml",
      "source": {
        "endpoints.distributor-mgmt": "dev.portal.aws",
        "spring.application.version": "1.0.0"
      }
    }
  ]
}
```

## Client Configuration

### Configuration in client's `bootstrap.yml`:
```yaml
spring:
  application:
    name: distributor-domain-branding
  cloud:
    config:
      uri: http://localhost:8888
      profile: dev
```

### Configuration in client's `application.yml`:
```yaml
spring:
  config:
    import: "configserver:http://localhost:8888"
```

## Advantages of this Structure

1. **Separation of Responsibilities**: Configurations organized by layers (common, core, domain)
2. **Reusability**: Common configurations shared between applications
3. **Flexibility**: Support for multiple environments and profiles
4. **Maintainability**: Clear and predictable structure
5. **Scalability**: Easy addition of new applications and configurations

## Additional Notes

- The server uses the `native` profile to serve files from the classpath
- Configurations can be overridden following the established hierarchy
- It's possible to add new search locations by modifying `search-locations`
- The server supports automatic refresh when configuration changes are detected

## Troubleshooting

### Common Issues

1. **Port 8888 already in use**: Change the port in `application.yml`
2. **Configuration not found**: Verify directory structure and file names
3. **Profiles not applied**: Ensure the profile name matches exactly

### Debug Logging

Enable debug logging by adding to `application.yml`:
```yaml
logging:
  level:
    org.springframework.cloud.config: DEBUG
```