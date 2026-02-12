# Firefly Framework - Config Server

[![CI](https://github.com/fireflyframework/fireflyframework-config-server/actions/workflows/ci.yml/badge.svg)](https://github.com/fireflyframework/fireflyframework-config-server/actions/workflows/ci.yml)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)
[![Java](https://img.shields.io/badge/Java-21%2B-orange.svg)](https://openjdk.org)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.x-green.svg)](https://spring.io/projects/spring-boot)

> Spring Cloud Config Server for centralized configuration management across Firefly Framework microservices.

---

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Requirements](#requirements)
- [Installation](#installation)
- [Quick Start](#quick-start)
- [Configuration](#configuration)
- [Documentation](#documentation)
- [Contributing](#contributing)
- [License](#license)

## Overview

Firefly Framework Config Server provides centralized configuration management for Firefly-based microservices using Spring Cloud Config Server. It serves externalized configuration from a Git repository (or other backends) to all connected microservices, enabling environment-specific configuration, runtime configuration updates, and consistent property management.

The config server is a standalone Spring Boot application that can be deployed as a dedicated microservice. All other Firefly Framework modules can connect to it via the Spring Cloud Config client auto-configuration provided by `fireflyframework-core`.

This module simplifies configuration management in distributed environments by providing a single source of truth for application properties across all environments (development, staging, production).

## Features

- Spring Cloud Config Server for centralized configuration
- Git-backed configuration repository support
- Environment-specific configuration profiles
- Runtime configuration refresh without restart
- Encryption and decryption of sensitive properties
- Integration with Firefly Core's cloud config client
- Standalone Spring Boot application deployment

## Requirements

- Java 21+
- Spring Boot 3.x
- Maven 3.9+
- Git repository for configuration storage

## Installation

```xml
<dependency>
    <groupId>org.fireflyframework</groupId>
    <artifactId>fireflyframework-config-server</artifactId>
    <version>26.02.03</version>
</dependency>
```

## Quick Start

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.config.server.EnableConfigServer;

@SpringBootApplication
@EnableConfigServer
public class FireflyConfigServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(FireflyConfigServerApplication.class, args);
    }
}
```

## Configuration

```yaml
server:
  port: 8888

spring:
  cloud:
    config:
      server:
        git:
          uri: https://github.com/your-org/config-repo
          default-label: main
          search-paths: '{application}'
```

## Documentation

No additional documentation available for this project.

## Contributing

Contributions are welcome. Please read the [CONTRIBUTING.md](CONTRIBUTING.md) guide for details on our code of conduct, development process, and how to submit pull requests.

## License

Copyright 2024-2026 Firefly Software Solutions Inc.

Licensed under the Apache License, Version 2.0. See [LICENSE](LICENSE) for details.
