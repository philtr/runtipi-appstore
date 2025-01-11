# Checklist
## Dynamic compose for movary
This is a movary update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://movary.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/movary:/app/storage
- [ ] ${APP_DATA_DIR}/data/movary:/app/storage
- [ ] ${APP_DATA_DIR}/data/mysql:/var/lib/mysql
##### Specific instructions :
- [ ] 🌳 Environment
- [ ] 👤 User (1000:1000)
- [ ] 🔗 Depends on
- [ ] ⌨ Command
- [ ] 🩺 Healthcheck

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "movary",
      "image": "leepeuker/movary:0.62.2",
      "isMain": true,
      "internalPort": 80,
      "user": "1000:1000",
      "environment": {
        "TMDB_API_KEY": "${MOVARY_TMDB_API_KEY}",
        "TIMEZONE": "${TZ}",
        "DATABASE_MODE": "mysql",
        "DATABASE_MYSQL_HOST": "movary-db",
        "DATABASE_MYSQL_NAME": "movary",
        "DATABASE_MYSQL_USER": "tipi",
        "DATABASE_MYSQL_PASSWORD": "${MOVARY_MYSQL_PASSWORD}",
        "TMDB_ENABLE_IMAGE_CACHING": "1",
        "APPLICATION_URL": "${APP_PROTOCOL:-http}://${APP_DOMAIN}",
        "PLEX_IDENTIFIER": "${MOVARY_PLEX_IDENTIFIER}",
        "JELLYFIN_DEVICE_ID": "${MOVARY_JELLYFIN_IDENTIFIER}"
      },
      "dependsOn": {
        "movary-db": {
          "condition": "service_healthy"
        }
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/movary",
          "containerPath": "/app/storage"
        }
      ]
    },
    {
      "name": "movary-migration",
      "image": "leepeuker/movary:0.62.2",
      "user": "1000:1000",
      "environment": {
        "TMDB_API_KEY": "${MOVARY_TMDB_API_KEY}",
        "TIMEZONE": "${TZ}",
        "DATABASE_MODE": "mysql",
        "DATABASE_MYSQL_HOST": "movary-db",
        "DATABASE_MYSQL_NAME": "movary",
        "DATABASE_MYSQL_USER": "tipi",
        "DATABASE_MYSQL_PASSWORD": "${MOVARY_MYSQL_PASSWORD}",
        "TMDB_ENABLE_IMAGE_CACHING": "1",
        "APPLICATION_URL": "${APP_PROTOCOL:-http}://${APP_DOMAIN}",
        "PLEX_IDENTIFIER": "${MOVARY_PLEX_IDENTIFIER}",
        "JELLYFIN_DEVICE_ID": "${MOVARY_JELLYFIN_IDENTIFIER}"
      },
      "dependsOn": {
        "movary-db": {
          "condition": "service_healthy"
        }
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/movary",
          "containerPath": "/app/storage"
        }
      ],
      "command": "php bin/console.php database:migration:migrate"
    },
    {
      "name": "movary-db",
      "image": "mysql:8.0",
      "environment": {
        "MYSQL_DATABASE": "movary",
        "MYSQL_USER": "tipi",
        "MYSQL_PASSWORD": "${MOVARY_MYSQL_PASSWORD}",
        "MYSQL_ROOT_PASSWORD": "${MOVARY_MYSQL_PASSWORD}"
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/mysql",
          "containerPath": "/var/lib/mysql"
        }
      ],
      "healthCheck": {
        "timeout": "20s",
        "retries": 10,
        "test": "mysqladmin ping -h localhost"
      }
    }
  ]
} 
```
# Original YAML
```yaml
version: '3'
services:
  movary:
    image: leepeuker/movary:0.62.2
    container_name: movary
    environment:
    - TMDB_API_KEY=${MOVARY_TMDB_API_KEY}
    - TIMEZONE=${TZ}
    - DATABASE_MODE=mysql
    - DATABASE_MYSQL_HOST=movary-db
    - DATABASE_MYSQL_NAME=movary
    - DATABASE_MYSQL_USER=tipi
    - DATABASE_MYSQL_PASSWORD=${MOVARY_MYSQL_PASSWORD}
    - TMDB_ENABLE_IMAGE_CACHING=1
    - APPLICATION_URL=${APP_PROTOCOL:-http}://${APP_DOMAIN}
    - PLEX_IDENTIFIER=${MOVARY_PLEX_IDENTIFIER}
    - JELLYFIN_DEVICE_ID=${MOVARY_JELLYFIN_IDENTIFIER}
    user: 1000:1000
    restart: unless-stopped
    volumes:
    - ${APP_DATA_DIR}/data/movary:/app/storage
    ports:
    - ${APP_PORT}:80
    depends_on:
      movary-db:
        condition: service_healthy
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.movary-web-redirect.redirectscheme.scheme: https
      traefik.http.services.movary.loadbalancer.server.port: 80
      traefik.http.routers.movary-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.movary-insecure.entrypoints: web
      traefik.http.routers.movary-insecure.service: movary
      traefik.http.routers.movary-insecure.middlewares: movary-web-redirect
      traefik.http.routers.movary.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.movary.entrypoints: websecure
      traefik.http.routers.movary.service: movary
      traefik.http.routers.movary.tls.certresolver: myresolver
      traefik.http.routers.movary-local-insecure.rule: Host(`movary.${LOCAL_DOMAIN}`)
      traefik.http.routers.movary-local-insecure.entrypoints: web
      traefik.http.routers.movary-local-insecure.service: movary
      traefik.http.routers.movary-local-insecure.middlewares: movary-web-redirect
      traefik.http.routers.movary-local.rule: Host(`movary.${LOCAL_DOMAIN}`)
      traefik.http.routers.movary-local.entrypoints: websecure
      traefik.http.routers.movary-local.service: movary
      traefik.http.routers.movary-local.tls: true
      runtipi.managed: true
  movary-migration:
    image: leepeuker/movary:0.62.2
    container_name: movary-migration
    command: php bin/console.php database:migration:migrate
    environment:
    - TMDB_API_KEY=${MOVARY_TMDB_API_KEY}
    - TIMEZONE=${TZ}
    - DATABASE_MODE=mysql
    - DATABASE_MYSQL_HOST=movary-db
    - DATABASE_MYSQL_NAME=movary
    - DATABASE_MYSQL_USER=tipi
    - DATABASE_MYSQL_PASSWORD=${MOVARY_MYSQL_PASSWORD}
    - TMDB_ENABLE_IMAGE_CACHING=1
    - APPLICATION_URL=${APP_PROTOCOL:-http}://${APP_DOMAIN}
    - PLEX_IDENTIFIER=${MOVARY_PLEX_IDENTIFIER}
    - JELLYFIN_DEVICE_ID=${MOVARY_JELLYFIN_IDENTIFIER}
    user: 1000:1000
    volumes:
    - ${APP_DATA_DIR}/data/movary:/app/storage
    depends_on:
      movary-db:
        condition: service_healthy
    networks:
    - tipi_main_network
    labels:
      runtipi.managed: true
  movary-db:
    image: mysql:8.0
    container_name: movary-db
    environment:
      MYSQL_DATABASE: movary
      MYSQL_USER: tipi
      MYSQL_PASSWORD: ${MOVARY_MYSQL_PASSWORD}
      MYSQL_ROOT_PASSWORD: ${MOVARY_MYSQL_PASSWORD}
    volumes:
    - ${APP_DATA_DIR}/data/mysql:/var/lib/mysql
    networks:
    - tipi_main_network
    healthcheck:
      test:
      - CMD
      - mysqladmin
      - ping
      - -h
      - localhost
      timeout: 20s
      retries: 10
    labels:
      runtipi.managed: true
 
```
