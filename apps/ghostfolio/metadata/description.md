# Checklist
## Dynamic compose for ghostfolio
This is a ghostfolio update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://ghostfolio.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/db:/var/lib/postgresql/data
- [ ] ${APP_DATA_DIR}/data/redis:/data
##### Specific instructions :
- [ ] 🌳 Environment
- [ ] 🔗 Depends on
- [ ] 🩺 Healthcheck
- [ ] ⌨ Command

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "ghostfolio",
      "image": "ghostfolio/ghostfolio:2.134.0",
      "isMain": true,
      "internalPort": 3333,
      "environment": {
        "NODE_ENV": "production",
        "HOST": "0.0.0.0",
        "PORT": 3333,
        "ACCESS_TOKEN_SALT": "$GHOSTFOLIO_ACCESS_TOKEN_SALT",
        "DATABASE_URL": "postgresql://ghostfolio:${GHOSTFOLIO_DB_PASSWORD}@ghostfolio-db:5432/ghostfolio?sslmode=prefer",
        "JWT_SECRET_KEY": "${GHOSTFOLIO_JWT_SECRET_KEY}",
        "POSTGRES_DB": "ghostfolio",
        "POSTGRES_USER": "ghostfolio",
        "POSTGRES_PASSWORD": "${GHOSTFOLIO_DB_PASSWORD}",
        "REDIS_HOST": "ghostfolio-redis",
        "REDIS_PASSWORD": "${GHOSTFOLIO_REDIS_PASSWORD}",
        "REDIS_PORT": 6379
      },
      "dependsOn": {
        "ghostfolio-db": {
          "condition": "service_healthy"
        },
        "ghostfolio-redis": {
          "condition": "service_healthy"
        }
      }
    },
    {
      "name": "ghostfolio-db",
      "image": "postgres:15.4-alpine",
      "environment": {
        "POSTGRES_DB": "ghostfolio",
        "POSTGRES_USER": "ghostfolio",
        "POSTGRES_PASSWORD": "${GHOSTFOLIO_DB_PASSWORD}",
        "PGDATA": "/var/lib/postgresql/data"
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/db",
          "containerPath": "/var/lib/postgresql/data"
        }
      ],
      "healthCheck": {
        "interval": "10s",
        "timeout": "5s",
        "retries": 5,
        "test": "pg_isready -d ghostfolio"
      }
    },
    {
      "name": "ghostfolio-redis",
      "image": "redis:7-alpine",
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/redis",
          "containerPath": "/data"
        }
      ],
      "command": "--requirepass ${GHOSTFOLIO_REDIS_PASSWORD}\n",
      "healthCheck": {
        "interval": "10s",
        "timeout": "5s",
        "retries": 5,
        "startPeriod": "30s",
        "test": "redis-cli ping"
      }
    }
  ]
} 
```
# Original YAML
```yaml
version: '3.9'
services:
  ghostfolio:
    container_name: ghostfolio
    image: ghostfolio/ghostfolio:2.134.0
    restart: unless-stopped
    ports:
    - ${APP_PORT}:3333
    environment:
      NODE_ENV: production
      HOST: 0.0.0.0
      PORT: 3333
      ACCESS_TOKEN_SALT: $GHOSTFOLIO_ACCESS_TOKEN_SALT
      DATABASE_URL: postgresql://ghostfolio:${GHOSTFOLIO_DB_PASSWORD}@ghostfolio-db:5432/ghostfolio?sslmode=prefer
      JWT_SECRET_KEY: ${GHOSTFOLIO_JWT_SECRET_KEY}
      POSTGRES_DB: ghostfolio
      POSTGRES_USER: ghostfolio
      POSTGRES_PASSWORD: ${GHOSTFOLIO_DB_PASSWORD}
      REDIS_HOST: ghostfolio-redis
      REDIS_PASSWORD: ${GHOSTFOLIO_REDIS_PASSWORD}
      REDIS_PORT: 6379
    networks:
    - tipi_main_network
    depends_on:
      ghostfolio-db:
        condition: service_healthy
      ghostfolio-redis:
        condition: service_healthy
    labels:
      traefik.enable: true
      traefik.http.middlewares.ghostfolio-web-redirect.redirectscheme.scheme: https
      traefik.http.services.ghostfolio.loadbalancer.server.port: 3333
      traefik.http.routers.ghostfolio-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.ghostfolio-insecure.entrypoints: web
      traefik.http.routers.ghostfolio-insecure.service: ghostfolio
      traefik.http.routers.ghostfolio-insecure.middlewares: ghostfolio-web-redirect
      traefik.http.routers.ghostfolio.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.ghostfolio.entrypoints: websecure
      traefik.http.routers.ghostfolio.service: ghostfolio
      traefik.http.routers.ghostfolio.tls.certresolver: myresolver
      traefik.http.routers.ghostfolio-local-insecure.rule: Host(`ghostfolio.${LOCAL_DOMAIN}`)
      traefik.http.routers.ghostfolio-local-insecure.entrypoints: web
      traefik.http.routers.ghostfolio-local-insecure.service: ghostfolio
      traefik.http.routers.ghostfolio-local-insecure.middlewares: ghostfolio-web-redirect
      traefik.http.routers.ghostfolio-local.rule: Host(`ghostfolio.${LOCAL_DOMAIN}`)
      traefik.http.routers.ghostfolio-local.entrypoints: websecure
      traefik.http.routers.ghostfolio-local.service: ghostfolio
      traefik.http.routers.ghostfolio-local.tls: true
      runtipi.managed: true
  ghostfolio-db:
    container_name: ghostfolio-db
    image: postgres:15.4-alpine
    restart: unless-stopped
    environment:
      POSTGRES_DB: ghostfolio
      POSTGRES_USER: ghostfolio
      POSTGRES_PASSWORD: ${GHOSTFOLIO_DB_PASSWORD}
      PGDATA: /var/lib/postgresql/data
    healthcheck:
      test:
      - CMD
      - pg_isready
      - -d
      - ghostfolio
      interval: 10s
      timeout: 5s
      retries: 5
    volumes:
    - ${APP_DATA_DIR}/data/db:/var/lib/postgresql/data
    networks:
    - tipi_main_network
    labels:
      runtipi.managed: true
  ghostfolio-redis:
    container_name: ghostfolio-redis
    image: redis:7-alpine
    restart: unless-stopped
    command: '--requirepass ${GHOSTFOLIO_REDIS_PASSWORD}

      '
    healthcheck:
      test:
      - CMD
      - redis-cli
      - ping
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 30s
    volumes:
    - ${APP_DATA_DIR}/data/redis:/data
    networks:
    - tipi_main_network
    labels:
      runtipi.managed: true
 
```
