# Checklist
## Dynamic compose for syncthing
This is a syncthing update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://syncthing.tipi.local
- [ ] Additionnal Port(s)
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data:/var/syncthing
- [ ] ${ROOT_FOLDER_HOST}/media/data/syncthing:/media/data/syncthing
##### Specific instructions :
- [ ] 🌳 Environment
- [ ] 🖥 Hostname
- [ ] 👼 Stop grace period

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "syncthing",
      "image": "syncthing/syncthing:1.29",
      "isMain": true,
      "internalPort": 8384,
      "addPorts": [
        {
          "hostPort": 22000,
          "containerPort": 22000,
          "tcp": true,
          "udp": true
        },
        {
          "hostPort": 21027,
          "containerPort": 21027,
          "udp": true
        }
      ],
      "hostname": "${SYNCTHING_HOSTNAME:-tipi}",
      "environment": {
        "PUID": "1000",
        "PGID": "1000"
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data",
          "containerPath": "/var/syncthing"
        },
        {
          "hostPath": "${ROOT_FOLDER_HOST}/media/data/syncthing",
          "containerPath": "/media/data/syncthing"
        }
      ],
      "stop_grace_period": "1m"
    }
  ]
} 
```
# Original YAML
```yaml
version: '3.7'
services:
  syncthing:
    container_name: syncthing
    image: syncthing/syncthing:1.29
    stop_grace_period: 1m
    hostname: ${SYNCTHING_HOSTNAME:-tipi}
    environment:
    - PUID=1000
    - PGID=1000
    volumes:
    - ${APP_DATA_DIR}/data:/var/syncthing
    - ${ROOT_FOLDER_HOST}/media/data/syncthing:/media/data/syncthing
    ports:
    - ${APP_PORT}:8384
    - 22000:22000/tcp
    - 22000:22000/udp
    - 21027:21027/udp
    restart: unless-stopped
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.syncthing-web-redirect.redirectscheme.scheme: https
      traefik.http.services.syncthing.loadbalancer.server.port: 8384
      traefik.http.routers.syncthing-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.syncthing-insecure.entrypoints: web
      traefik.http.routers.syncthing-insecure.service: syncthing
      traefik.http.routers.syncthing-insecure.middlewares: syncthing-web-redirect
      traefik.http.routers.syncthing.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.syncthing.entrypoints: websecure
      traefik.http.routers.syncthing.service: syncthing
      traefik.http.routers.syncthing.tls.certresolver: myresolver
      traefik.http.routers.syncthing-local-insecure.rule: Host(`syncthing.${LOCAL_DOMAIN}`)
      traefik.http.routers.syncthing-local-insecure.entrypoints: web
      traefik.http.routers.syncthing-local-insecure.service: syncthing
      traefik.http.routers.syncthing-local-insecure.middlewares: syncthing-web-redirect
      traefik.http.routers.syncthing-local.rule: Host(`syncthing.${LOCAL_DOMAIN}`)
      traefik.http.routers.syncthing-local.entrypoints: websecure
      traefik.http.routers.syncthing-local.service: syncthing
      traefik.http.routers.syncthing-local.tls: true
      runtipi.managed: true
 
```
