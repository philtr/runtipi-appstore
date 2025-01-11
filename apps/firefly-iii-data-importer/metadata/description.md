# Checklist
## Dynamic compose for firefly-iii-data-importer
This is a firefly-iii-data-importer update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://firefly-iii-data-importer.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Specific instructions :
- [ ] 🌳 Environment

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "firefly-iii-data-importer",
      "image": "fireflyiii/data-importer:version-1.5.6",
      "isMain": true,
      "internalPort": 8080,
      "environment": {
        "FIREFLY_III_URL": "${FIREFLY_III_URL}",
        "FIREFLY_III_ACCESS_TOKEN": "${FIREFLY_III_ACCESS_TOKEN}",
        "FIREFLY_III_CLIENT_ID": "${FIREFLY_III_CLIENT_ID}",
        "TZ": "${TZ}",
        "TRUSTED_PROXIES": "**",
        "VERIFY_TLS_SECURITY": "false",
        "APP_ENV": "local",
        "APP_DEBUG": "false",
        "LOG_CHANNEL": "stack",
        "LOG_LEVEL": "info"
      }
    }
  ]
} 
```
# Original YAML
```yaml
version: '3.9'
services:
  firefly-iii-data-importer:
    image: fireflyiii/data-importer:version-1.5.6
    container_name: firefly-iii-data-importer
    restart: unless-stopped
    ports:
    - ${APP_PORT}:8080
    environment:
    - FIREFLY_III_URL=${FIREFLY_III_URL}
    - FIREFLY_III_ACCESS_TOKEN=${FIREFLY_III_ACCESS_TOKEN}
    - FIREFLY_III_CLIENT_ID=${FIREFLY_III_CLIENT_ID}
    - TZ=${TZ}
    - TRUSTED_PROXIES=**
    - VERIFY_TLS_SECURITY=false
    - APP_ENV=local
    - APP_DEBUG=false
    - LOG_CHANNEL=stack
    - LOG_LEVEL=info
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.firefly-iii-importer-web-redirect.redirectscheme.scheme: https
      traefik.http.services.firefly-iii-importer.loadbalancer.server.port: 8080
      traefik.http.routers.firefly-iii-importer-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.firefly-iii-importer-insecure.entrypoints: web
      traefik.http.routers.firefly-iii-importer-insecure.service: firefly-iii-importer
      traefik.http.routers.firefly-iii-importer-insecure.middlewares: firefly-iii-importer-web-redirect
      traefik.http.routers.firefly-iii-importer.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.firefly-iii-importer.entrypoints: websecure
      traefik.http.routers.firefly-iii-importer.service: firefly-iii-importer
      traefik.http.routers.firefly-iii-importer.tls.certresolver: myresolver
      traefik.http.routers.firefly-iii-importer-local-insecure.rule: Host(`firefly-iii-data-importer.${LOCAL_DOMAIN}`)
      traefik.http.routers.firefly-iii-importer-local-insecure.entrypoints: web
      traefik.http.routers.firefly-iii-importer-local-insecure.service: firefly-iii-importer
      traefik.http.routers.firefly-iii-importer-local-insecure.middlewares: firefly-iii-importer-web-redirect
      traefik.http.routers.firefly-iii-importer-local.rule: Host(`firefly-iii-data-importer.${LOCAL_DOMAIN}`)
      traefik.http.routers.firefly-iii-importer-local.entrypoints: websecure
      traefik.http.routers.firefly-iii-importer-local.service: firefly-iii-importer
      traefik.http.routers.firefly-iii-importer-local.tls: true
      runtipi.managed: true
 
```
