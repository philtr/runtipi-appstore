# Checklist
## Dynamic compose for crafty
This is a crafty update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://crafty.tipi.local
- [ ] Additionnal Port(s)
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/backups:/crafty/backups
- [ ] ${APP_DATA_DIR}/data/logs:/crafty/logs
- [ ] ${APP_DATA_DIR}/data/servers:/crafty/servers
- [ ] ${APP_DATA_DIR}/data/config:/crafty/app/config
- [ ] ${APP_DATA_DIR}/data/import:/crafty/import
##### Specific instructions :
- [ ] 🌳 Environment

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "crafty",
      "image": "registry.gitlab.com/crafty-controller/crafty-4:4.4.4",
      "isMain": true,
      "internalPort": 8443,
      "addPorts": [
        {
          "hostPort": 8123,
          "containerPort": 8123
        },
        {
          "hostPort": 19132,
          "containerPort": 19132,
          "udp": true
        },
        {
          "hostPort": "25500-25600",
          "containerPort": "25500-25600"
        }
      ],
      "environment": {
        "TZ": "${TZ}"
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/backups",
          "containerPath": "/crafty/backups"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/logs",
          "containerPath": "/crafty/logs"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/servers",
          "containerPath": "/crafty/servers"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/config",
          "containerPath": "/crafty/app/config"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/import",
          "containerPath": "/crafty/import"
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
  crafty:
    container_name: crafty
    image: registry.gitlab.com/crafty-controller/crafty-4:4.4.4
    restart: unless-stopped
    environment:
    - TZ=${TZ}
    ports:
    - ${APP_PORT}:8443
    - 8123:8123
    - 19132:19132/udp
    - 25500-25600:25500-25600
    networks:
    - tipi_main_network
    volumes:
    - ${APP_DATA_DIR}/data/backups:/crafty/backups
    - ${APP_DATA_DIR}/data/logs:/crafty/logs
    - ${APP_DATA_DIR}/data/servers:/crafty/servers
    - ${APP_DATA_DIR}/data/config:/crafty/app/config
    - ${APP_DATA_DIR}/data/import:/crafty/import
    labels:
      traefik.enable: true
      traefik.http.middlewares.crafty-docs-web-redirect.redirectscheme.scheme: https
      traefik.http.services.crafty-docs.loadbalancer.server.port: 8443
      traefik.http.routers.crafty-docs-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.crafty-docs-insecure.entrypoints: web
      traefik.http.routers.crafty-docs-insecure.service: crafty-docs
      traefik.http.routers.crafty-docs-insecure.middlewares: crafty-docs-web-redirect
      traefik.http.routers.crafty-docs.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.crafty-docs.entrypoints: websecure
      traefik.http.routers.crafty-docs.service: crafty-docs
      traefik.http.routers.crafty-docs.tls.certresolver: myresolver
      traefik.http.routers.crafty-docs-local-insecure.rule: Host(`crafty-docs.${LOCAL_DOMAIN}`)
      traefik.http.routers.crafty-docs-local-insecure.entrypoints: web
      traefik.http.routers.crafty-docs-local-insecure.service: crafty-docs
      traefik.http.routers.crafty-docs-local-insecure.middlewares: crafty-docs-web-redirect
      traefik.http.routers.crafty-docs-local.rule: Host(`crafty-docs.${LOCAL_DOMAIN}`)
      traefik.http.routers.crafty-docs-local.entrypoints: websecure
      traefik.http.routers.crafty-docs-local.service: crafty-docs
      traefik.http.routers.crafty-docs-local.tls: true
      runtipi.managed: true
 
```
