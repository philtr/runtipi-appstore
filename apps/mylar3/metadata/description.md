# Checklist
## Dynamic compose for mylar3
This is a mylar3 update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://mylar3.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/mylar3-config:/config
- [ ] ${ROOT_FOLDER_HOST}/media/data/comics:/comics
- [ ] ${ROOT_FOLDER_HOST}/media/downloads/mylar3:/downloads
##### Specific instructions :
- [ ] 🌳 Environment

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "mylar3",
      "image": "lscr.io/linuxserver/mylar3:v0.7.1-ls84",
      "isMain": true,
      "internalPort": 8090,
      "environment": {
        "PUID": "1000",
        "PGID": "1000",
        "TZ": "${TZ}"
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/mylar3-config",
          "containerPath": "/config"
        },
        {
          "hostPath": "${ROOT_FOLDER_HOST}/media/data/comics",
          "containerPath": "/comics"
        },
        {
          "hostPath": "${ROOT_FOLDER_HOST}/media/downloads/mylar3",
          "containerPath": "/downloads"
        }
      ]
    }
  ]
} 
```
# Original YAML
```yaml
version: '3'
services:
  mylar3:
    container_name: mylar3
    image: lscr.io/linuxserver/mylar3:v0.7.1-ls84
    ports:
    - ${APP_PORT}:8090
    volumes:
    - ${APP_DATA_DIR}/data/mylar3-config:/config
    - ${ROOT_FOLDER_HOST}/media/data/comics:/comics
    - ${ROOT_FOLDER_HOST}/media/downloads/mylar3:/downloads
    environment:
    - PUID=1000
    - PGID=1000
    - TZ=${TZ}
    restart: unless-stopped
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.mylar3-web-redirect.redirectscheme.scheme: https
      traefik.http.services.mylar3.loadbalancer.server.port: 8090
      traefik.http.routers.mylar3-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.mylar3-insecure.entrypoints: web
      traefik.http.routers.mylar3-insecure.service: mylar3
      traefik.http.routers.mylar3-insecure.middlewares: mylar3-web-redirect
      traefik.http.routers.mylar3.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.mylar3.entrypoints: websecure
      traefik.http.routers.mylar3.service: mylar3
      traefik.http.routers.mylar3.tls.certresolver: myresolver
      traefik.http.routers.mylar3-local-insecure.rule: Host(`mylar3.${LOCAL_DOMAIN}`)
      traefik.http.routers.mylar3-local-insecure.entrypoints: web
      traefik.http.routers.mylar3-local-insecure.service: mylar3
      traefik.http.routers.mylar3-local-insecure.middlewares: mylar3-web-redirect
      traefik.http.routers.mylar3-local.rule: Host(`mylar3.${LOCAL_DOMAIN}`)
      traefik.http.routers.mylar3-local.entrypoints: websecure
      traefik.http.routers.mylar3-local.service: mylar3
      traefik.http.routers.mylar3-local.tls: true
      runtipi.managed: true
 
```
