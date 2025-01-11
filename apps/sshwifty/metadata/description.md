# Checklist
## Dynamic compose for sshwifty
This is a sshwifty update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://sshwifty.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Specific instructions :
- [ ] 👑 Privileged

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "sshwifty",
      "image": "niruix/sshwifty:0.3.15-beta-release",
      "isMain": true,
      "internalPort": 8182,
      "privileged": true
    }
  ]
} 
```
# Original YAML
```yaml
version: '3.5'
services:
  sshwifty:
    image: niruix/sshwifty:0.3.15-beta-release
    restart: unless-stopped
    container_name: sshwifty
    privileged: true
    ports:
    - ${APP_PORT}:8182
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.sshwifty-web-redirect.redirectscheme.scheme: https
      traefik.http.services.sshwifty.loadbalancer.server.port: 8182
      traefik.http.routers.sshwifty-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.sshwifty-insecure.entrypoints: web
      traefik.http.routers.sshwifty-insecure.service: sshwifty
      traefik.http.routers.sshwifty-insecure.middlewares: sshwifty-web-redirect
      traefik.http.routers.sshwifty.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.sshwifty.entrypoints: websecure
      traefik.http.routers.sshwifty.service: sshwifty
      traefik.http.routers.sshwifty.tls.certresolver: myresolver
      traefik.http.routers.sshwifty-local-insecure.rule: Host(`sshwifty.${LOCAL_DOMAIN}`)
      traefik.http.routers.sshwifty-local-insecure.entrypoints: web
      traefik.http.routers.sshwifty-local-insecure.service: sshwifty
      traefik.http.routers.sshwifty-local-insecure.middlewares: sshwifty-web-redirect
      traefik.http.routers.sshwifty-local.rule: Host(`sshwifty.${LOCAL_DOMAIN}`)
      traefik.http.routers.sshwifty-local.entrypoints: websecure
      traefik.http.routers.sshwifty-local.service: sshwifty
      traefik.http.routers.sshwifty-local.tls: true
      runtipi.managed: true
 
```
