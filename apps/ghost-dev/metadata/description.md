# Checklist
## Dynamic compose for ghost-dev
This is a ghost-dev update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://ghost-dev.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data:/var/lib/ghost/content
##### Specific instructions :
- [ ] 🌳 Environment

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "ghost-dev",
      "image": "ghost:5.105.0",
      "isMain": true,
      "internalPort": 2368,
      "environment": {
        "database__connection__filename": "/var/lib/ghost/content/data/ghost.db",
        "NODE_ENV": "development"
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data",
          "containerPath": "/var/lib/ghost/content"
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
  ghost-dev:
    container_name: ghost-dev
    image: ghost:5.105.0
    restart: unless-stopped
    volumes:
    - ${APP_DATA_DIR}/data:/var/lib/ghost/content
    environment:
    - database__connection__filename=/var/lib/ghost/content/data/ghost.db
    - NODE_ENV=development
    ports:
    - ${APP_PORT}:2368
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.ghost-dev-web-redirect.redirectscheme.scheme: https
      traefik.http.services.ghost-dev.loadbalancer.server.port: 2368
      traefik.http.routers.ghost-dev-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.ghost-dev-insecure.entrypoints: web
      traefik.http.routers.ghost-dev-insecure.service: ghost-dev
      traefik.http.routers.ghost-dev-insecure.middlewares: ghost-dev-web-redirect
      traefik.http.routers.ghost-dev.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.ghost-dev.entrypoints: websecure
      traefik.http.routers.ghost-dev.service: ghost-dev
      traefik.http.routers.ghost-dev.tls.certresolver: myresolver
      traefik.http.routers.ghost-dev-local-insecure.rule: Host(`ghost-dev.${LOCAL_DOMAIN}`)
      traefik.http.routers.ghost-dev-local-insecure.entrypoints: web
      traefik.http.routers.ghost-dev-local-insecure.service: ghost-dev
      traefik.http.routers.ghost-dev-local-insecure.middlewares: ghost-dev-web-redirect
      traefik.http.routers.ghost-dev-local.rule: Host(`ghost-dev.${LOCAL_DOMAIN}`)
      traefik.http.routers.ghost-dev-local.entrypoints: websecure
      traefik.http.routers.ghost-dev-local.service: ghost-dev
      traefik.http.routers.ghost-dev-local.tls: true
      runtipi.managed: true
 
```
