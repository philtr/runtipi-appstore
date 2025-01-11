# Checklist
## Dynamic compose for portainer
This is a portainer update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://portainer.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] /var/run/docker.sock:/var/run/docker.sock
- [ ] ${APP_DATA_DIR}/data:/data

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "portainer",
      "image": "portainer/portainer-ce:2.25.1-alpine",
      "isMain": true,
      "internalPort": 9443,
      "volumes": [
        {
          "hostPath": "/var/run/docker.sock",
          "containerPath": "/var/run/docker.sock"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data",
          "containerPath": "/data"
        }
      ]
    }
  ]
} 
```
# Original YAML
```yaml
services:
  portainer:
    image: portainer/portainer-ce:2.25.1-alpine
    container_name: portainer
    restart: unless-stopped
    ports:
    - ${APP_PORT}:9443
    volumes:
    - /var/run/docker.sock:/var/run/docker.sock
    - ${APP_DATA_DIR}/data:/data
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.portainer-web-redirect.redirectscheme.scheme: https
      traefik.http.services.portainer.loadbalancer.server.port: 9000
      traefik.http.routers.portainer-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.portainer-insecure.entrypoints: web
      traefik.http.routers.portainer-insecure.service: portainer
      traefik.http.routers.portainer-insecure.middlewares: portainer-web-redirect
      traefik.http.routers.portainer.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.portainer.entrypoints: websecure
      traefik.http.routers.portainer.service: portainer
      traefik.http.routers.portainer.tls.certresolver: myresolver
      traefik.http.routers.portainer-local-insecure.rule: Host(`portainer.${LOCAL_DOMAIN}`)
      traefik.http.routers.portainer-local-insecure.entrypoints: web
      traefik.http.routers.portainer-local-insecure.service: portainer
      traefik.http.routers.portainer-local-insecure.middlewares: portainer-web-redirect
      traefik.http.routers.portainer-local.rule: Host(`portainer.${LOCAL_DOMAIN}`)
      traefik.http.routers.portainer-local.entrypoints: websecure
      traefik.http.routers.portainer-local.service: portainer
      traefik.http.routers.portainer-local.tls: true
      runtipi.managed: true
 
```
