# Checklist
## Dynamic compose for emulatorjs
This is a emulatorjs update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://emulatorjs.tipi.local
- [ ] 🌐 Additionnal Port(s)
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/emulatorjs-config:/config
- [ ] ${APP_DATA_DIR}/data/emulatorjs:/data
##### Specific instructions :
- [ ] 🌳 Environment

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "emulatorjs",
      "image": "lscr.io/linuxserver/emulatorjs:1.9.2",
      "isMain": true,
      "internalPort": 80,
      "addPorts": [
        {
          "hostPort": 8165,
          "containerPort": 3000
        }
      ],
      "environment": {
        "TZ": "${TZ}"
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/emulatorjs-config",
          "containerPath": "/config"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/emulatorjs",
          "containerPath": "/data"
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
  emulatorjs:
    container_name: emulatorjs
    image: lscr.io/linuxserver/emulatorjs:1.9.2
    ports:
    - ${APP_PORT}:80
    - 8165:3000
    volumes:
    - ${APP_DATA_DIR}/data/emulatorjs-config:/config
    - ${APP_DATA_DIR}/data/emulatorjs:/data
    environment:
    - TZ=${TZ}
    restart: unless-stopped
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.emulatorjs-web-redirect.redirectscheme.scheme: https
      traefik.http.services.emulatorjs.loadbalancer.server.port: 80
      traefik.http.routers.emulatorjs-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.emulatorjs-insecure.entrypoints: web
      traefik.http.routers.emulatorjs-insecure.service: emulatorjs
      traefik.http.routers.emulatorjs-insecure.middlewares: emulatorjs-web-redirect
      traefik.http.routers.emulatorjs.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.emulatorjs.entrypoints: websecure
      traefik.http.routers.emulatorjs.service: emulatorjs
      traefik.http.routers.emulatorjs.tls.certresolver: myresolver
      traefik.http.routers.emulatorjs-local-insecure.rule: Host(`emulatorjs.${LOCAL_DOMAIN}`)
      traefik.http.routers.emulatorjs-local-insecure.entrypoints: web
      traefik.http.routers.emulatorjs-local-insecure.service: emulatorjs
      traefik.http.routers.emulatorjs-local-insecure.middlewares: emulatorjs-web-redirect
      traefik.http.routers.emulatorjs-local.rule: Host(`emulatorjs.${LOCAL_DOMAIN}`)
      traefik.http.routers.emulatorjs-local.entrypoints: websecure
      traefik.http.routers.emulatorjs-local.service: emulatorjs
      traefik.http.routers.emulatorjs-local.tls: true
      runtipi.managed: true
 
```
