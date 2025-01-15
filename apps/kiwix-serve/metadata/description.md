# Checklist
## Dynamic compose for kiwix-serve
This is a kiwix-serve update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://kiwix-serve.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/zim:/data
##### Specific instructions :
- [ ] ⌨ Command

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "kiwix-serve",
      "image": "ghcr.io/kiwix/kiwix-serve:3.7.0-2",
      "isMain": true,
      "internalPort": 8080,
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/zim",
          "containerPath": "/data"
        }
      ],
      "command": "*.zim"
    }
  ]
} 
```
# Original YAML
```yaml
version: '3.9'
services:
  kiwix-serve:
    container_name: kiwix-serve
    image: ghcr.io/kiwix/kiwix-serve:3.7.0-2
    ports:
    - ${APP_PORT}:8080
    volumes:
    - ${APP_DATA_DIR}/data/zim:/data
    command: '*.zim'
    restart: unless-stopped
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.kiwix-serve-web-redirect.redirectscheme.scheme: https
      traefik.http.services.kiwix-serve.loadbalancer.server.port: 8080
      traefik.http.routers.kiwix-serve-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.kiwix-serve-insecure.entrypoints: web
      traefik.http.routers.kiwix-serve-insecure.service: kiwix-serve
      traefik.http.routers.kiwix-serve-insecure.middlewares: kiwix-serve-web-redirect
      traefik.http.routers.kiwix-serve.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.kiwix-serve.entrypoints: websecure
      traefik.http.routers.kiwix-serve.service: kiwix-serve
      traefik.http.routers.kiwix-serve.tls.certresolver: myresolver
      traefik.http.routers.kiwix-serve-local-insecure.rule: Host(`kiwix-serve.${LOCAL_DOMAIN}`)
      traefik.http.routers.kiwix-serve-local-insecure.entrypoints: web
      traefik.http.routers.kiwix-serve-local-insecure.service: kiwix-serve
      traefik.http.routers.kiwix-serve-local-insecure.middlewares: kiwix-serve-web-redirect
      traefik.http.routers.kiwix-serve-local.rule: Host(`kiwix-serve.${LOCAL_DOMAIN}`)
      traefik.http.routers.kiwix-serve-local.entrypoints: websecure
      traefik.http.routers.kiwix-serve-local.service: kiwix-serve
      traefik.http.routers.kiwix-serve-local.tls: true
      runtipi.managed: true
 
```
