# Config Server — Centralized Configuration

Spring Cloud Config Server for the Nephro Platform. Serves externalized configuration to all microservices from a private Git repository, eliminating the need to rebuild images when config changes.

## Tech Stack

- Java 17, Spring Boot 3.4.2
- Spring Cloud Config Server
- Spring Cloud Netflix Eureka Client
- Git backend (GitHub)

## Port

`8888`

## How It Works

Each microservice sets:

```properties
spring.config.import=optional:configserver:http://config-server:8888
```

On startup it fetches its configuration from the config server, which clones and reads from the Git repo at `https://github.com/pedinephro2/nephro-config.git`.

Config files in the repo follow the naming convention:
```
{service-name}-{profile}.properties
# e.g. microservice1-docker.properties
```

## Environment Variables

| Variable | Description |
|---|---|
| `CONFIG_REPO_USERNAME` | GitHub username with access to the config repo |
| `CONFIG_REPO_TOKEN` | GitHub Personal Access Token (needs `repo` scope) |
| `SPRING_PROFILES_ACTIVE` | Set to `docker` for Docker-specific config |

## Running Locally

```bash
export CONFIG_REPO_USERNAME=your-username
export CONFIG_REPO_TOKEN=your-pat
mvn spring-boot:run
```

## Docker

```bash
docker build -t azizos07/config-server:latest .
docker run -p 8888:8888 \
  -e SPRING_PROFILES_ACTIVE=docker \
  -e CONFIG_REPO_USERNAME=your-username \
  -e CONFIG_REPO_TOKEN=your-pat \
  azizos07/config-server:latest
```

## Health Check

```bash
curl http://localhost:8888/actuator/health
```

Fetch config for a service:

```bash
curl http://localhost:8888/microservice1/docker
```
