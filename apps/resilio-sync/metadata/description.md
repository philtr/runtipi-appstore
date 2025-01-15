# Checklist
## Dynamic compose for resilio-sync
This is a resilio-sync update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://resilio-sync.tipi.local
- [ ] 🌐 Additionnal Port(s)
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/config:/config
- [ ] ${APP_DATA_DIR}/data/downloads:/downloads
- [ ] ${APP_DATA_DIR}/data/sync:/sync
##### Specific instructions :
- [ ] 🌳 Environment

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "resilio-sync",
      "image": "lscr.io/linuxserver/resilio-sync:3.0.0",
      "isMain": true,
      "internalPort": 8888,
      "addPorts": [
        {
          "hostPort": 55555,
          "containerPort": 55555
        }
      ],
      "environment": {
        "PUID": "1000",
        "PGID": "1000"
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/config",
          "containerPath": "/config"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/downloads",
          "containerPath": "/downloads"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/sync",
          "containerPath": "/sync"
        }
      ]
    }
  ]
} 
```
# Original YAML
```yaml
version: '3.7'
services:
  resilio-sync:
    image: lscr.io/linuxserver/resilio-sync:3.0.0
    container_name: resilio-sync
    environment:
    - PUID=1000
    - PGID=1000
    volumes:
    - ${APP_DATA_DIR}/data/config:/config
    - ${APP_DATA_DIR}/data/downloads:/downloads
    - ${APP_DATA_DIR}/data/sync:/sync
    ports:
    - ${APP_PORT}:8888
    - 55555:55555
    restart: unless-stopped
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.resilio-sync-web-redirect.redirectscheme.scheme: https
      traefik.http.services.resilio-sync.loadbalancer.server.port: 8888
      traefik.http.routers.resilio-sync-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.resilio-sync-insecure.entrypoints: web
      traefik.http.routers.resilio-sync-insecure.service: resilio-sync
      traefik.http.routers.resilio-sync-insecure.middlewares: resilio-sync-web-redirect
      traefik.http.routers.resilio-sync.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.resilio-sync.entrypoints: websecure
      traefik.http.routers.resilio-sync.service: resilio-sync
      traefik.http.routers.resilio-sync.tls.certresolver: myresolver
      traefik.http.routers.resilio-sync-local-insecure.rule: Host(`resilio-sync.${LOCAL_DOMAIN}`)
      traefik.http.routers.resilio-sync-local-insecure.entrypoints: web
      traefik.http.routers.resilio-sync-local-insecure.service: resilio-sync
      traefik.http.routers.resilio-sync-local-insecure.middlewares: resilio-sync-web-redirect
      traefik.http.routers.resilio-sync-local.rule: Host(`resilio-sync.${LOCAL_DOMAIN}`)
      traefik.http.routers.resilio-sync-local.entrypoints: websecure
      traefik.http.routers.resilio-sync-local.service: resilio-sync
      traefik.http.routers.resilio-sync-local.tls: true
      runtipi.managed: true
 
```
