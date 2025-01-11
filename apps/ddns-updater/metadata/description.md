# Checklist
## Dynamic compose for ddns-updater
This is a ddns-updater update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://ddns-updater.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/config:/updater/data

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "ddns-updater",
      "image": "qmcgaw/ddns-updater:v2.9.0",
      "isMain": true,
      "internalPort": 8000,
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/config",
          "containerPath": "/updater/data"
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
  ddns-updater:
    container_name: ddns-updater
    image: qmcgaw/ddns-updater:v2.9.0
    restart: unless-stopped
    volumes:
    - ${APP_DATA_DIR}/config:/updater/data
    ports:
    - ${APP_PORT}:8000
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.ddns-updater-web-redirect.redirectscheme.scheme: https
      traefik.http.services.ddns-updater.loadbalancer.server.port: 8000
      traefik.http.routers.ddns-updater-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.ddns-updater-insecure.entrypoints: web
      traefik.http.routers.ddns-updater-insecure.service: ddns-updater
      traefik.http.routers.ddns-updater-insecure.middlewares: ddns-updater-web-redirect
      traefik.http.routers.ddns-updater.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.ddns-updater.entrypoints: websecure
      traefik.http.routers.ddns-updater.service: ddns-updater
      traefik.http.routers.ddns-updater.tls.certresolver: myresolver
      traefik.http.routers.ddns-updater-local-insecure.rule: Host(`ddns-updater.${LOCAL_DOMAIN}`)
      traefik.http.routers.ddns-updater-local-insecure.entrypoints: web
      traefik.http.routers.ddns-updater-local-insecure.service: ddns-updater
      traefik.http.routers.ddns-updater-local-insecure.middlewares: ddns-updater-web-redirect
      traefik.http.routers.ddns-updater-local.rule: Host(`ddns-updater.${LOCAL_DOMAIN}`)
      traefik.http.routers.ddns-updater-local.entrypoints: websecure
      traefik.http.routers.ddns-updater-local.service: ddns-updater
      traefik.http.routers.ddns-updater-local.tls: true
      runtipi.managed: true
 
```
