# Checklist
## Dynamic compose for seedsync
This is a seedsync update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://seedsync.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/config:/config
- [ ] ${ROOT_FOLDER_HOST}/media/torrents/complete:/downloads
##### Specific instructions :
- [ ] 🌳 Environment
- [ ] 👤 User (${SEEDSYNC_PUID:-1000}:${SEEDSYNC_PGID:-1000})

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "seedsync",
      "image": "ipsingh06/seedsync:0.8.6",
      "isMain": true,
      "internalPort": 8800,
      "user": "${SEEDSYNC_PUID:-1000}:${SEEDSYNC_PGID:-1000}",
      "environment": {
        "TZ": "${TZ}"
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/config",
          "containerPath": "/config"
        },
        {
          "hostPath": "${ROOT_FOLDER_HOST}/media/torrents/complete",
          "containerPath": "/downloads"
        }
      ]
    }
  ]
} 
```
# Original YAML
```yaml
version: '3.9'
services:
  seedsync:
    container_name: seedsync
    image: ipsingh06/seedsync:0.8.6
    user: ${SEEDSYNC_PUID:-1000}:${SEEDSYNC_PGID:-1000}
    environment:
    - TZ=${TZ}
    volumes:
    - ${APP_DATA_DIR}/data/config:/config
    - ${ROOT_FOLDER_HOST}/media/torrents/complete:/downloads
    ports:
    - ${APP_PORT}:8800
    restart: unless-stopped
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.seedsync-web-redirect.redirectscheme.scheme: https
      traefik.http.services.seedsync.loadbalancer.server.port: 8800
      traefik.http.routers.seedsync-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.seedsync-insecure.entrypoints: web
      traefik.http.routers.seedsync-insecure.service: seedsync
      traefik.http.routers.seedsync-insecure.middlewares: seedsync-web-redirect
      traefik.http.routers.seedsync.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.seedsync.entrypoints: websecure
      traefik.http.routers.seedsync.service: seedsync
      traefik.http.routers.seedsync.tls.certresolver: myresolver
      traefik.http.routers.seedsync-local-insecure.rule: Host(`seedsync.${LOCAL_DOMAIN}`)
      traefik.http.routers.seedsync-local-insecure.entrypoints: web
      traefik.http.routers.seedsync-local-insecure.service: seedsync
      traefik.http.routers.seedsync-local-insecure.middlewares: seedsync-web-redirect
      traefik.http.routers.seedsync-local.rule: Host(`seedsync.${LOCAL_DOMAIN}`)
      traefik.http.routers.seedsync-local.entrypoints: websecure
      traefik.http.routers.seedsync-local.service: seedsync
      traefik.http.routers.seedsync-local.tls: true
      runtipi.managed: true
 
```
