# Checklist
## Dynamic compose for slskd
This is a slskd update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://slskd.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}:/app
- [ ] ${ROOT_FOLDER_HOST}/media/downloads/complete:/downloads
- [ ] ${ROOT_FOLDER_HOST}/media/downloads/incomplete:/incomplete
- [ ] ${ROOT_FOLDER_HOST}/media/:/shared
##### Specific instructions :
- [ ] 🌳 Environment

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "slskd",
      "image": "slskd/slskd:0.22.1",
      "isMain": true,
      "internalPort": 5030,
      "environment": {
        "SLSKD_WEB_USER": "${SLSKD_WEB_USER}",
        "SLSKD_WEB_PASSWORD": "${SLSKD_WEB_PASSWORD}",
        "SLSKD_USER": "${SLSKD_USER}",
        "SLSKD_PASSWORD": "${SLSKD_PASSWORD}",
        "SLSKD_REMOTE_CONFIGURATION": "${SLSKD_REMOTE_CONFIGURATION}",
        "SLSKD_CONFIG": "/app/data/slskd.yml",
        "SLSKD_SHARED_DIR": "/shared"
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}",
          "containerPath": "/app"
        },
        {
          "hostPath": "${ROOT_FOLDER_HOST}/media/downloads/complete",
          "containerPath": "/downloads"
        },
        {
          "hostPath": "${ROOT_FOLDER_HOST}/media/downloads/incomplete",
          "containerPath": "/incomplete"
        },
        {
          "hostPath": "${ROOT_FOLDER_HOST}/media/",
          "containerPath": "/shared"
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
  slskd:
    image: slskd/slskd:0.22.1
    container_name: slskd
    volumes:
    - ${APP_DATA_DIR}:/app
    - ${ROOT_FOLDER_HOST}/media/downloads/complete:/downloads
    - ${ROOT_FOLDER_HOST}/media/downloads/incomplete:/incomplete
    - ${ROOT_FOLDER_HOST}/media/:/shared
    environment:
    - SLSKD_WEB_USER=${SLSKD_WEB_USER}
    - SLSKD_WEB_PASSWORD=${SLSKD_WEB_PASSWORD}
    - SLSKD_USER=${SLSKD_USER}
    - SLSKD_PASSWORD=${SLSKD_PASSWORD}
    - SLSKD_REMOTE_CONFIGURATION=${SLSKD_REMOTE_CONFIGURATION}
    - SLSKD_CONFIG=/app/data/slskd.yml
    - SLSKD_SHARED_DIR=/shared
    restart: unless-stopped
    networks:
    - tipi_main_network
    ports:
    - ${APP_PORT}:5030
    labels:
      traefik.enable: true
      traefik.http.middlewares.slskd-web-redirect.redirectscheme.scheme: https
      traefik.http.services.slskd.loadbalancer.server.port: 5030
      traefik.http.routers.slskd-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.slskd-insecure.entrypoints: web
      traefik.http.routers.slskd-insecure.service: slskd
      traefik.http.routers.slskd-insecure.middlewares: slskd-web-redirect
      traefik.http.routers.slskd.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.slskd.entrypoints: websecure
      traefik.http.routers.slskd.service: slskd
      traefik.http.routers.slskd.tls.certresolver: myresolver
      traefik.http.routers.slskd-local-insecure.rule: Host(`slskd.${LOCAL_DOMAIN}`)
      traefik.http.routers.slskd-local-insecure.entrypoints: web
      traefik.http.routers.slskd-local-insecure.service: slskd
      traefik.http.routers.slskd-local-insecure.middlewares: slskd-web-redirect
      traefik.http.routers.slskd-local.rule: Host(`slskd.${LOCAL_DOMAIN}`)
      traefik.http.routers.slskd-local.entrypoints: websecure
      traefik.http.routers.slskd-local.service: slskd
      traefik.http.routers.slskd-local.tls: true
      runtipi.managed: true
 
```
