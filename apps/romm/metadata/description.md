# Checklist
## Dynamic compose for romm
This is a romm update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://romm.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${ROOT_FOLDER_HOST}/media/data/roms:/romm/library
- [ ] ${APP_DATA_DIR}/data/redis:/redis-data
- [ ] ${APP_DATA_DIR}/data/romm-resources:/romm/resources
- [ ] ${APP_DATA_DIR}/data/config:/romm/config
- [ ] ${APP_DATA_DIR}/data/assets:/romm/assets
- [ ] ${APP_DATA_DIR}/data/mysql/config:/config
##### Specific instructions :
- [ ] 🌳 Environment
- [ ] 🔗 Depends on

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "romm",
      "image": "ghcr.io/rommapp/romm:3.7.2",
      "isMain": true,
      "internalPort": 8080,
      "environment": {
        "DB_HOST": "romm-db",
        "DB_NAME": "romm",
        "DB_USER": "tipi",
        "DB_PASSWD": "${ROMM_MYSQL_PASSWORD}",
        "ROMM_AUTH_SECRET_KEY": "${ROMM_AUTH_SECRET_KEY}",
        "IGDB_CLIENT_ID": "${ROMM_IGDB_CLIENT_ID}",
        "IGDB_CLIENT_SECRET": "${ROMM_IGDB_CLIENT_SECRET}",
        "MOBYGAMES_API_KEY": "${ROMM_MOBYGAMES_API_KEY}",
        "STEAMGRIDDB_API_KEY": "${ROMM_STEAMGRIDDB_API_KEY}"
      },
      "dependsOn": [
        "romm-db"
      ],
      "volumes": [
        {
          "hostPath": "${ROOT_FOLDER_HOST}/media/data/roms",
          "containerPath": "/romm/library"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/redis",
          "containerPath": "/redis-data"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/romm-resources",
          "containerPath": "/romm/resources"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/config",
          "containerPath": "/romm/config"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/assets",
          "containerPath": "/romm/assets"
        }
      ]
    },
    {
      "name": "romm-db",
      "image": "lscr.io/linuxserver/mariadb:11.4.4",
      "environment": {
        "MYSQL_ROOT_PASSWORD": "${ROMM_MYSQL_ROOT_PASSWORD}",
        "MYSQL_DATABASE": "romm",
        "MYSQL_USER": "tipi",
        "MYSQL_PASSWORD": "${ROMM_MYSQL_PASSWORD}"
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/mysql/config",
          "containerPath": "/config"
        }
      ]
    }
  ]
} 
```
# Original YAML
```yaml
services:
  romm:
    image: ghcr.io/rommapp/romm:3.7.2
    container_name: romm
    environment:
    - DB_HOST=romm-db
    - DB_NAME=romm
    - DB_USER=tipi
    - DB_PASSWD=${ROMM_MYSQL_PASSWORD}
    - ROMM_AUTH_SECRET_KEY=${ROMM_AUTH_SECRET_KEY}
    - IGDB_CLIENT_ID=${ROMM_IGDB_CLIENT_ID}
    - IGDB_CLIENT_SECRET=${ROMM_IGDB_CLIENT_SECRET}
    - MOBYGAMES_API_KEY=${ROMM_MOBYGAMES_API_KEY}
    - STEAMGRIDDB_API_KEY=${ROMM_STEAMGRIDDB_API_KEY}
    restart: unless-stopped
    volumes:
    - ${ROOT_FOLDER_HOST}/media/data/roms:/romm/library
    - ${APP_DATA_DIR}/data/redis:/redis-data
    - ${APP_DATA_DIR}/data/romm-resources:/romm/resources
    - ${APP_DATA_DIR}/data/config:/romm/config
    - ${APP_DATA_DIR}/data/assets:/romm/assets
    ports:
    - ${APP_PORT}:8080
    depends_on:
    - romm-db
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.romm-web-redirect.redirectscheme.scheme: https
      traefik.http.services.romm.loadbalancer.server.port: 8080
      traefik.http.routers.romm-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.romm-insecure.entrypoints: web
      traefik.http.routers.romm-insecure.service: romm
      traefik.http.routers.romm-insecure.middlewares: romm-web-redirect
      traefik.http.routers.romm.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.romm.entrypoints: websecure
      traefik.http.routers.romm.service: romm
      traefik.http.routers.romm.tls.certresolver: myresolver
      traefik.http.routers.romm-local-insecure.rule: Host(`romm.${LOCAL_DOMAIN}`)
      traefik.http.routers.romm-local-insecure.entrypoints: web
      traefik.http.routers.romm-local-insecure.service: romm
      traefik.http.routers.romm-local-insecure.middlewares: romm-web-redirect
      traefik.http.routers.romm-local.rule: Host(`romm.${LOCAL_DOMAIN}`)
      traefik.http.routers.romm-local.entrypoints: websecure
      traefik.http.routers.romm-local.service: romm
      traefik.http.routers.romm-local.tls: true
      runtipi.managed: true
  romm-db:
    image: lscr.io/linuxserver/mariadb:11.4.4
    container_name: romm-db
    environment:
    - MYSQL_ROOT_PASSWORD=${ROMM_MYSQL_ROOT_PASSWORD}
    - MYSQL_DATABASE=romm
    - MYSQL_USER=tipi
    - MYSQL_PASSWORD=${ROMM_MYSQL_PASSWORD}
    volumes:
    - ${APP_DATA_DIR}/data/mysql/config:/config
    restart: unless-stopped
    networks:
    - tipi_main_network
    labels:
      runtipi.managed: true
 
```
