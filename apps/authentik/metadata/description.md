# Checklist
## Dynamic compose for authentik
This is a authentik update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://authentik.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/authentik-media:/media
- [ ] ${APP_DATA_DIR}/data/authentik-custom-templates:/templates
- [ ] /var/run/docker.sock:/var/run/docker.sock
- [ ] ${APP_DATA_DIR}/data/authentik-media:/media
- [ ] ${APP_DATA_DIR}/data/authentik-certs:/certs
- [ ] ${APP_DATA_DIR}/data/authentik-custom-templates:/templates
- [ ] ${APP_DATA_DIR}/data/postgres:/var/lib/postgresql/data
- [ ] ${APP_DATA_DIR}/data/redis:/data
##### Specific instructions :
- [ ] 🌳 Environment
- [ ] ⌨ Command
- [ ] 🔗 Depends on
- [ ] 👤 User (root)
- [ ] 🩺 Healthcheck

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "authentik",
      "image": "ghcr.io/goauthentik/server:2024.12.2",
      "isMain": true,
      "internalPort": 9443,
      "environment": {
        "AUTHENTIK_REDIS__HOST": "authentik-redis",
        "AUTHENTIK_POSTGRESQL__HOST": "authentik-db",
        "AUTHENTIK_POSTGRESQL__USER": "authentik",
        "AUTHENTIK_POSTGRESQL__NAME": "authentik",
        "AUTHENTIK_POSTGRESQL__PASSWORD": "${AUTHENTIK_DB_PASSWORD}",
        "AUTHENTIK_SECRET_KEY": "${AUTHENTIK_SECRET_KEY}"
      },
      "dependsOn": [
        "authentik-db",
        "authentik-redis"
      ],
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/authentik-media",
          "containerPath": "/media"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/authentik-custom-templates",
          "containerPath": "/templates"
        }
      ],
      "command": "server"
    },
    {
      "name": "authentik-worker",
      "image": "ghcr.io/goauthentik/server:2024.12.2",
      "user": "root",
      "environment": {
        "AUTHENTIK_REDIS__HOST": "authentik-redis",
        "AUTHENTIK_POSTGRESQL__HOST": "authentik-db",
        "AUTHENTIK_POSTGRESQL__USER": "authentik",
        "AUTHENTIK_POSTGRESQL__NAME": "authentik",
        "AUTHENTIK_POSTGRESQL__PASSWORD": "${AUTHENTIK_DB_PASSWORD}",
        "AUTHENTIK_SECRET_KEY": "${AUTHENTIK_SECRET_KEY}"
      },
      "dependsOn": [
        "authentik-db",
        "authentik-redis"
      ],
      "volumes": [
        {
          "hostPath": "/var/run/docker.sock",
          "containerPath": "/var/run/docker.sock"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/authentik-media",
          "containerPath": "/media"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/authentik-certs",
          "containerPath": "/certs"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/authentik-custom-templates",
          "containerPath": "/templates"
        }
      ],
      "command": "worker"
    },
    {
      "name": "authentik-db",
      "image": "postgres:12-alpine",
      "environment": {
        "POSTGRES_PASSWORD": "${AUTHENTIK_DB_PASSWORD}",
        "POSTGRES_USER": "authentik",
        "POSTGRES_DB": "authentik"
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/postgres",
          "containerPath": "/var/lib/postgresql/data"
        }
      ],
      "healthCheck": {
        "interval": "30s",
        "timeout": "5s",
        "retries": 5,
        "startPeriod": "20s",
        "test": "pg_isready -d $${POSTGRES_DB} -U $${POSTGRES_USER}"
      }
    },
    {
      "name": "authentik-redis",
      "image": "redis:alpine",
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/redis",
          "containerPath": "/data"
        }
      ],
      "command": "--save 60 1 --loglevel warning",
      "healthCheck": {
        "interval": "30s",
        "timeout": "3s",
        "retries": 5,
        "startPeriod": "20s",
        "test": "redis-cli ping | grep PONG"
      }
    }
  ]
} 
```
# Original YAML
```yaml
version: '3.7'
services:
  authentik:
    image: ghcr.io/goauthentik/server:2024.12.2
    restart: unless-stopped
    command: server
    container_name: authentik
    environment:
      AUTHENTIK_REDIS__HOST: authentik-redis
      AUTHENTIK_POSTGRESQL__HOST: authentik-db
      AUTHENTIK_POSTGRESQL__USER: authentik
      AUTHENTIK_POSTGRESQL__NAME: authentik
      AUTHENTIK_POSTGRESQL__PASSWORD: ${AUTHENTIK_DB_PASSWORD}
      AUTHENTIK_SECRET_KEY: ${AUTHENTIK_SECRET_KEY}
    volumes:
    - ${APP_DATA_DIR}/data/authentik-media:/media
    - ${APP_DATA_DIR}/data/authentik-custom-templates:/templates
    ports:
    - ${APP_PORT}:9443
    depends_on:
    - authentik-db
    - authentik-redis
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.authentik-web-redirect.redirectscheme.scheme: https
      traefik.http.services.authentik.loadbalancer.server.port: 9000
      traefik.http.routers.authentik-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.authentik-insecure.entrypoints: web
      traefik.http.routers.authentik-insecure.service: authentik
      traefik.http.routers.authentik-insecure.middlewares: authentik-web-redirect
      traefik.http.routers.authentik.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.authentik.entrypoints: websecure
      traefik.http.routers.authentik.service: authentik
      traefik.http.routers.authentik.tls.certresolver: myresolver
      traefik.http.routers.authentik-local-insecure.rule: Host(`authentik.${LOCAL_DOMAIN}`)
      traefik.http.routers.authentik-local-insecure.entrypoints: web
      traefik.http.routers.authentik-local-insecure.service: authentik
      traefik.http.routers.authentik-local-insecure.middlewares: authentik-web-redirect
      traefik.http.routers.authentik-local.rule: Host(`authentik.${LOCAL_DOMAIN}`)
      traefik.http.routers.authentik-local.entrypoints: websecure
      traefik.http.routers.authentik-local.service: authentik
      traefik.http.routers.authentik-local.tls: true
      runtipi.managed: true
  authentik-worker:
    image: ghcr.io/goauthentik/server:2024.12.2
    restart: unless-stopped
    command: worker
    container_name: authentik-worker
    environment:
      AUTHENTIK_REDIS__HOST: authentik-redis
      AUTHENTIK_POSTGRESQL__HOST: authentik-db
      AUTHENTIK_POSTGRESQL__USER: authentik
      AUTHENTIK_POSTGRESQL__NAME: authentik
      AUTHENTIK_POSTGRESQL__PASSWORD: ${AUTHENTIK_DB_PASSWORD}
      AUTHENTIK_SECRET_KEY: ${AUTHENTIK_SECRET_KEY}
    user: root
    volumes:
    - /var/run/docker.sock:/var/run/docker.sock
    - ${APP_DATA_DIR}/data/authentik-media:/media
    - ${APP_DATA_DIR}/data/authentik-certs:/certs
    - ${APP_DATA_DIR}/data/authentik-custom-templates:/templates
    depends_on:
    - authentik-db
    - authentik-redis
    networks:
    - tipi_main_network
    labels:
      runtipi.managed: true
  authentik-db:
    container_name: authentik-db
    image: postgres:12-alpine
    restart: unless-stopped
    healthcheck:
      test:
      - CMD-SHELL
      - pg_isready -d $${POSTGRES_DB} -U $${POSTGRES_USER}
      start_period: 20s
      interval: 30s
      retries: 5
      timeout: 5s
    volumes:
    - ${APP_DATA_DIR}/data/postgres:/var/lib/postgresql/data
    environment:
      POSTGRES_PASSWORD: ${AUTHENTIK_DB_PASSWORD}
      POSTGRES_USER: authentik
      POSTGRES_DB: authentik
    networks:
    - tipi_main_network
    labels:
      runtipi.managed: true
  authentik-redis:
    image: redis:alpine
    command: --save 60 1 --loglevel warning
    container_name: authentik-redis
    restart: unless-stopped
    healthcheck:
      test:
      - CMD-SHELL
      - redis-cli ping | grep PONG
      start_period: 20s
      interval: 30s
      retries: 5
      timeout: 3s
    volumes:
    - ${APP_DATA_DIR}/data/redis:/data
    networks:
    - tipi_main_network
    labels:
      runtipi.managed: true
 
```
