# Checklist
## Dynamic compose for drawio
This is a drawio update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://drawio.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Specific instructions :
- [ ] 🔤 TTY (True)
- [ ] 🤖 Stdin open

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "drawio",
      "image": "jgraph/drawio:24.7.17",
      "isMain": true,
      "internalPort": 8080,
      "tty": true,
      "stdinOpen": true
    }
  ]
} 
```
# Original YAML
```yaml
version: '3.7'
services:
  drawio:
    image: jgraph/drawio:24.7.17
    ports:
    - ${APP_PORT}:8080
    container_name: drawio
    tty: true
    stdin_open: true
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.drawio-web-redirect.redirectscheme.scheme: https
      traefik.http.services.drawio.loadbalancer.server.port: 8080
      traefik.http.routers.drawio-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.drawio-insecure.entrypoints: web
      traefik.http.routers.drawio-insecure.service: drawio
      traefik.http.routers.drawio-insecure.middlewares: drawio-web-redirect
      traefik.http.routers.drawio.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.drawio.entrypoints: websecure
      traefik.http.routers.drawio.service: drawio
      traefik.http.routers.drawio.tls.certresolver: myresolver
      traefik.http.routers.drawio-local-insecure.rule: Host(`drawio.${LOCAL_DOMAIN}`)
      traefik.http.routers.drawio-local-insecure.entrypoints: web
      traefik.http.routers.drawio-local-insecure.service: drawio
      traefik.http.routers.drawio-local-insecure.middlewares: drawio-web-redirect
      traefik.http.routers.drawio-local.rule: Host(`drawio.${LOCAL_DOMAIN}`)
      traefik.http.routers.drawio-local.entrypoints: websecure
      traefik.http.routers.drawio-local.service: drawio
      traefik.http.routers.drawio-local.tls: true
      runtipi.managed: true
 
```
