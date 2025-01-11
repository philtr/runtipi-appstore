# Checklist
## Dynamic compose for glances
This is a glances update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://glances.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] /var/run/docker.sock:/var/run/docker.sock:ro
##### Specific instructions :
- [ ] 🌳 Environment
- [ ] 🔢 PID (host)

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "glances",
      "image": "nicolargo/glances:4.3.0.8-full",
      "isMain": true,
      "internalPort": 61208,
      "pid": "host",
      "environment": {
        "TZ": "${TZ}",
        "GLANCES_OPT": "-w"
      },
      "volumes": [
        {
          "hostPath": "/var/run/docker.sock",
          "containerPath": "/var/run/docker.sock",
          "readOnly": true
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
  glances:
    container_name: glances
    restart: unless-stopped
    ports:
    - ${APP_PORT}:61208
    environment:
    - TZ=${TZ}
    - GLANCES_OPT=-w
    volumes:
    - /var/run/docker.sock:/var/run/docker.sock:ro
    pid: host
    image: nicolargo/glances:4.3.0.8-full
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.glances-web-redirect.redirectscheme.scheme: https
      traefik.http.services.glances.loadbalancer.server.port: 61208
      traefik.http.routers.glances-local-insecure.rule: Host(`glances.${LOCAL_DOMAIN}`)
      traefik.http.routers.glances-local-insecure.entrypoints: web
      traefik.http.routers.glances-local-insecure.service: glances
      traefik.http.routers.glances-local-insecure.middlewares: glances-web-redirect
      traefik.http.routers.glances-local.rule: Host(`glances.${LOCAL_DOMAIN}`)
      traefik.http.routers.glances-local.entrypoints: websecure
      traefik.http.routers.glances-local.service: glances
      traefik.http.routers.glances-local.tls: true
      runtipi.managed: true
 
```
