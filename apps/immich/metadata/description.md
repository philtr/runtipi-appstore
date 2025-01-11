# Checklist
## Dynamic compose for immich
This is a immich update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://immich.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${ROOT_FOLDER_HOST}/media/data/images/immich:/usr/src/app/upload
- [ ] ${ROOT_FOLDER_HOST}/media/data/images/immich:/usr/src/app/upload
- [ ] ${APP_DATA_DIR}/data/immich-ml-cache:/cache
- [ ] ${APP_DATA_DIR}/data/db:/var/lib/postgresql/data
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
      "name": "immich",
      "image": "ghcr.io/immich-app/immich-server:v1.124.2",
      "isMain": true,
      "internalPort": 2283,
      "environment": {
        "NODE_ENV": "production",
        "DB_HOSTNAME": "immich-db",
        "DB_USERNAME": "tipi",
        "DB_PASSWORD": "${DB_PASSWORD}",
        "ENABLE_MAPBOX": "false",
        "DB_DATABASE_NAME": "immich",
        "REDIS_HOSTNAME": "immich-redis",
        "JWT_SECRET": "${JWT_SECRET}"
      },
      "dependsOn": [
        "immich-redis",
        "immich-db"
      ],
      "volumes": [
        {
          "hostPath": "${ROOT_FOLDER_HOST}/media/data/images/immich",
          "containerPath": "/usr/src/app/upload"
        }
      ]
    },
    {
      "name": "immich-machine-learning",
      "image": "ghcr.io/immich-app/immich-machine-learning:v1.124.2",
      "environment": {
        "NODE_ENV": "production",
        "DB_HOSTNAME": "immich-db",
        "DB_USERNAME": "tipi",
        "DB_PASSWORD": "${DB_PASSWORD}",
        "DB_NAME": "immich",
        "DB_DATABASE_NAME": "immich"
      },
      "dependsOn": [
        "immich-db"
      ],
      "volumes": [
        {
          "hostPath": "${ROOT_FOLDER_HOST}/media/data/images/immich",
          "containerPath": "/usr/src/app/upload"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/immich-ml-cache",
          "containerPath": "/cache"
        }
      ]
    },
    {
      "name": "immich-redis",
      "image": "redis:6.2",
      "healthCheck": {
        "test": "redis-cli ping || exit 1"
      }
    },
    {
      "name": "immich-db",
      "image": "tensorchord/pgvecto-rs:pg14-v0.2.0@sha256:90724186f0a3517cf6914295b5ab410db9ce23190a2d9d0b9dd6463e3fa298f0",
      "environment": {
        "POSTGRES_PASSWORD": "${DB_PASSWORD}",
        "POSTGRES_USER": "tipi",
        "POSTGRES_DB": "immich",
        "PG_DATA": "/var/lib/postgresql/data"
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/db",
          "containerPath": "/var/lib/postgresql/data"
        }
      ],
      "command": [
        "postgres",
        "-c",
        "shared_preload_libraries=vectors.so",
        "-c",
        "search_path=\"$$user\", public, vectors",
        "-c",
        "logging_collector=on",
        "-c",
        "max_wal_size=2GB",
        "-c",
        "shared_buffers=512MB",
        "-c",
        "wal_compression=on"
      ],
      "healthCheck": {
        "interval": "5m",
        "startPeriod": "5m",
        "test": "pg_isready --dbname='${DB_DATABASE_NAME}' || exit 1; Chksum=\"$$(psql --dbname='${DB_DATABASE_NAME}' --username='${DB_USERNAME}' --tuples-only --no-align --command='SELECT COALESCE(SUM(checksum_failures), 0) FROM pg_stat_database')\"; echo \"checksum failure count is $$Chksum\"; [ \"$$Chksum\" = '0' ] || exit 1"
      }
    }
  ]
} 
```
# Original YAML
```yaml
version: '3.7'
services:
  immich:
    container_name: immich
    image: ghcr.io/immich-app/immich-server:v1.124.2
    volumes:
    - ${ROOT_FOLDER_HOST}/media/data/images/immich:/usr/src/app/upload
    environment:
    - NODE_ENV=production
    - DB_HOSTNAME=immich-db
    - DB_USERNAME=tipi
    - DB_PASSWORD=${DB_PASSWORD}
    - ENABLE_MAPBOX=false
    - DB_DATABASE_NAME=immich
    - REDIS_HOSTNAME=immich-redis
    - JWT_SECRET=${JWT_SECRET}
    depends_on:
    - immich-redis
    - immich-db
    ports:
    - ${APP_PORT}:2283
    restart: unless-stopped
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.immich-web-redirect.redirectscheme.scheme: https
      traefik.http.services.immich.loadbalancer.server.port: 2283
      traefik.http.routers.immich-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.immich-insecure.entrypoints: web
      traefik.http.routers.immich-insecure.service: immich
      traefik.http.routers.immich-insecure.middlewares: immich-web-redirect
      traefik.http.routers.immich.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.immich.entrypoints: websecure
      traefik.http.routers.immich.service: immich
      traefik.http.routers.immich.tls.certresolver: myresolver
      traefik.http.routers.immich-local-insecure.rule: Host(`immich.${LOCAL_DOMAIN}`)
      traefik.http.routers.immich-local-insecure.entrypoints: web
      traefik.http.routers.immich-local-insecure.service: immich
      traefik.http.routers.immich-local-insecure.middlewares: immich-web-redirect
      traefik.http.routers.immich-local.rule: Host(`immich.${LOCAL_DOMAIN}`)
      traefik.http.routers.immich-local.entrypoints: websecure
      traefik.http.routers.immich-local.service: immich
      traefik.http.routers.immich-local.tls: true
      runtipi.managed: true
  immich-machine-learning:
    container_name: immich-machine-learning
    image: ghcr.io/immich-app/immich-machine-learning:v1.124.2
    volumes:
    - ${ROOT_FOLDER_HOST}/media/data/images/immich:/usr/src/app/upload
    - ${APP_DATA_DIR}/data/immich-ml-cache:/cache
    environment:
    - NODE_ENV=production
    - DB_HOSTNAME=immich-db
    - DB_USERNAME=tipi
    - DB_PASSWORD=${DB_PASSWORD}
    - DB_NAME=immich
    - DB_DATABASE_NAME=immich
    depends_on:
    - immich-db
    restart: unless-stopped
    networks:
    - tipi_main_network
    labels:
      runtipi.managed: true
  immich-redis:
    container_name: immich-redis
    image: redis:6.2
    restart: unless-stopped
    networks:
    - tipi_main_network
    healthcheck:
      test: redis-cli ping || exit 1
    labels:
      runtipi.managed: true
  immich-db:
    container_name: immich-db
    image: tensorchord/pgvecto-rs:pg14-v0.2.0@sha256:90724186f0a3517cf6914295b5ab410db9ce23190a2d9d0b9dd6463e3fa298f0
    environment:
      POSTGRES_PASSWORD: ${DB_PASSWORD}
      POSTGRES_USER: tipi
      POSTGRES_DB: immich
      PG_DATA: /var/lib/postgresql/data
    volumes:
    - ${APP_DATA_DIR}/data/db:/var/lib/postgresql/data
    restart: unless-stopped
    networks:
    - tipi_main_network
    healthcheck:
      test: pg_isready --dbname='${DB_DATABASE_NAME}' || exit 1; Chksum="$$(psql --dbname='${DB_DATABASE_NAME}'
        --username='${DB_USERNAME}' --tuples-only --no-align --command='SELECT COALESCE(SUM(checksum_failures),
        0) FROM pg_stat_database')"; echo "checksum failure count is $$Chksum"; [
        "$$Chksum" = '0' ] || exit 1
      interval: 5m
      start_interval: 30s
      start_period: 5m
    command:
    - postgres
    - -c
    - shared_preload_libraries=vectors.so
    - -c
    - search_path="$$user", public, vectors
    - -c
    - logging_collector=on
    - -c
    - max_wal_size=2GB
    - -c
    - shared_buffers=512MB
    - -c
    - wal_compression=on
    labels:
      runtipi.managed: true
 
```
