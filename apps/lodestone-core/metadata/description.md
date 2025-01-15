# Checklist
## Dynamic compose for lodestone-core
This is a lodestone-core update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://lodestone-core.tipi.local
- [ ] 🌐 Additionnal Port(s)
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/lodestone-data:/root/.lodestone
##### Specific instructions :
- [ ] 👤 User (0:0)

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "lodestone-core",
      "image": "ghcr.io/lodestone-team/lodestone_core:0.5.1",
      "isMain": true,
      "internalPort": 16662,
      "addPorts": [
        {
          "hostPort": "25565-25575",
          "containerPort": "25565-25575"
        }
      ],
      "user": "0:0",
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/lodestone-data",
          "containerPath": "/root/.lodestone"
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
  lodestone-core:
    container_name: lodestone-core
    image: ghcr.io/lodestone-team/lodestone_core:0.5.1
    ports:
    - ${APP_PORT}:16662
    - 25565-25575:25565-25575
    restart: unless-stopped
    user: 0:0
    volumes:
    - ${APP_DATA_DIR}/data/lodestone-data:/root/.lodestone
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.lodestone-core-web-redirect.redirectscheme.scheme: https
      traefik.http.services.lodestone-core.loadbalancer.server.port: 16662
      traefik.http.routers.lodestone-core-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.lodestone-core-insecure.entrypoints: web
      traefik.http.routers.lodestone-core-insecure.service: lodestone-core
      traefik.http.routers.lodestone-core-insecure.middlewares: lodestone-core-web-redirect
      traefik.http.routers.lodestone-core.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.lodestone-core.entrypoints: websecure
      traefik.http.routers.lodestone-core.service: lodestone-core
      traefik.http.routers.lodestone-core.tls.certresolver: myresolver
      traefik.http.routers.lodestone-core-local-insecure.rule: Host(`lodestone-core.${LOCAL_DOMAIN}`)
      traefik.http.routers.lodestone-core-local-insecure.entrypoints: web
      traefik.http.routers.lodestone-core-local-insecure.service: lodestone-core
      traefik.http.routers.lodestone-core-local-insecure.middlewares: lodestone-core-web-redirect
      traefik.http.routers.lodestone-core-local.rule: Host(`lodestone-core.${LOCAL_DOMAIN}`)
      traefik.http.routers.lodestone-core-local.entrypoints: websecure
      traefik.http.routers.lodestone-core-local.service: lodestone-core
      traefik.http.routers.lodestone-core-local.tls: true
      runtipi.managed: true
 
```
