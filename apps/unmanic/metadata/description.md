# Checklist
## Dynamic compose for unmanic
This is a unmanic update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://unmanic.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/config:/config
- [ ] ${ROOT_FOLDER_HOST}/media/data:/library
- [ ] ${APP_DATA_DIR}/data/temp:/tmp/unmanic
##### Specific instructions :
- [ ] 🌳 Environment
- [ ] 👑 Privileged

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "unmanic",
      "image": "josh5/unmanic:0.2.8",
      "isMain": true,
      "internalPort": 8888,
      "environment": {
        "PUID": "${TIPI_UID}",
        "PGID": "${TIPI_GID}"
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/config",
          "containerPath": "/config"
        },
        {
          "hostPath": "${ROOT_FOLDER_HOST}/media/data",
          "containerPath": "/library"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/temp",
          "containerPath": "/tmp/unmanic"
        }
      ],
      "privileged": true
    }
  ]
} 
```
# Original YAML
```yaml
version: '3.5'
services:
  unmanic:
    image: josh5/unmanic:0.2.8
    restart: unless-stopped
    container_name: unmanic
    privileged: true
    ports:
    - ${APP_PORT}:8888
    networks:
    - tipi_main_network
    environment:
    - PUID=${TIPI_UID}
    - PGID=${TIPI_GID}
    volumes:
    - ${APP_DATA_DIR}/data/config:/config
    - ${ROOT_FOLDER_HOST}/media/data:/library
    - ${APP_DATA_DIR}/data/temp:/tmp/unmanic
    labels:
      traefik.enable: true
      traefik.http.middlewares.unmanic-web-redirect.redirectscheme.scheme: https
      traefik.http.services.unmanic.loadbalancer.server.port: 8888
      traefik.http.routers.unmanic-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.unmanic-insecure.entrypoints: web
      traefik.http.routers.unmanic-insecure.service: unmanic
      traefik.http.routers.unmanic-insecure.middlewares: unmanic-web-redirect
      traefik.http.routers.unmanic.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.unmanic.entrypoints: websecure
      traefik.http.routers.unmanic.service: unmanic
      traefik.http.routers.unmanic.tls.certresolver: myresolver
      traefik.http.routers.unmanic-local-insecure.rule: Host(`unmanic.${LOCAL_DOMAIN}`)
      traefik.http.routers.unmanic-local-insecure.entrypoints: web
      traefik.http.routers.unmanic-local-insecure.service: unmanic
      traefik.http.routers.unmanic-local-insecure.middlewares: unmanic-web-redirect
      traefik.http.routers.unmanic-local.rule: Host(`unmanic.${LOCAL_DOMAIN}`)
      traefik.http.routers.unmanic-local.entrypoints: websecure
      traefik.http.routers.unmanic-local.service: unmanic
      traefik.http.routers.unmanic-local.tls: true
      runtipi.managed: true
 
```
