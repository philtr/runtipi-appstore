# Checklist
## Dynamic compose for octobot
This is a octobot update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://octobot.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/user:/octobot/user
- [ ] ${APP_DATA_DIR}/data/tentacles:/octobot/tentacles
- [ ] ${APP_DATA_DIR}/data/logs:/octobot/logs
##### Specific instructions :
- [ ] 🌳 Environment
- 🏷 DNS (skipped)

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "octobot",
      "image": "drakkarsoftware/octobot:2.0.7",
      "isMain": true,
      "internalPort": 5001,
      "environment": {
        "TZ": "${TZ}"
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/user",
          "containerPath": "/octobot/user"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/tentacles",
          "containerPath": "/octobot/tentacles"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/logs",
          "containerPath": "/octobot/logs"
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
  octobot:
    container_name: octobot
    image: drakkarsoftware/octobot:2.0.7
    environment:
    - TZ=${TZ}
    volumes:
    - ${APP_DATA_DIR}/data/user:/octobot/user
    - ${APP_DATA_DIR}/data/tentacles:/octobot/tentacles
    - ${APP_DATA_DIR}/data/logs:/octobot/logs
    ports:
    - ${APP_PORT}:5001
    restart: unless-stopped
    networks:
    - tipi_main_network
    dns:
    - ${DNS_IP}
    labels:
      traefik.enable: true
      traefik.http.middlewares.octobot-web-redirect.redirectscheme.scheme: https
      traefik.http.services.octobot.loadbalancer.server.port: 5001
      traefik.http.routers.octobot-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.octobot-insecure.entrypoints: web
      traefik.http.routers.octobot-insecure.service: octobot
      traefik.http.routers.octobot-insecure.middlewares: octobot-web-redirect
      traefik.http.routers.octobot.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.octobot.entrypoints: websecure
      traefik.http.routers.octobot.service: octobot
      traefik.http.routers.octobot.tls.certresolver: myresolver
      traefik.http.routers.octobot-local-insecure.rule: Host(`octobot.${LOCAL_DOMAIN}`)
      traefik.http.routers.octobot-local-insecure.entrypoints: web
      traefik.http.routers.octobot-local-insecure.service: octobot
      traefik.http.routers.octobot-local-insecure.middlewares: octobot-web-redirect
      traefik.http.routers.octobot-local.rule: Host(`octobot.${LOCAL_DOMAIN}`)
      traefik.http.routers.octobot-local.entrypoints: websecure
      traefik.http.routers.octobot-local.service: octobot
      traefik.http.routers.octobot-local.tls: true
      runtipi.managed: true
 
```
