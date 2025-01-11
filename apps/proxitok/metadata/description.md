# Checklist
## Dynamic compose for proxitok
This is a proxitok update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://proxitok.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] proxitok-cache:/cache
##### Specific instructions :
- [ ] 🌳 Environment
- [ ] 🔗 Depends on
- [ ] ⌨ Command

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "proxitok",
      "image": "ghcr.io/pablouser1/proxitok:master",
      "isMain": true,
      "internalPort": 8080,
      "environment": {
        "LATTE_CACHE": "/cache",
        "API_CACHE": "redis",
        "REDIS_HOST": "proxitok-redis",
        "REDIS_PORT": "6379",
        "API_SIGNER": "remote",
        "API_SIGNER_URL": "http://proxitok-signer:8080/signature"
      },
      "dependsOn": [
        "proxitok-redis",
        "proxitok-signer"
      ],
      "volumes": [
        {
          "hostPath": "proxitok-cache",
          "containerPath": "/cache"
        }
      ]
    },
    {
      "name": "proxitok-redis",
      "image": "redis:7-alpine",
      "command": "redis-server --save 60 1 --loglevel warning"
    },
    {
      "name": "proxitok-signer",
      "image": "ghcr.io/pablouser1/signtok:master"
    }
  ]
} 
```
# Original YAML
```yaml
version: '3.8'
services:
  proxitok:
    container_name: proxitok
    image: ghcr.io/pablouser1/proxitok:master
    restart: unless-stopped
    ports:
    - ${APP_PORT}:8080
    environment:
    - LATTE_CACHE=/cache
    - API_CACHE=redis
    - REDIS_HOST=proxitok-redis
    - REDIS_PORT=6379
    - API_SIGNER=remote
    - API_SIGNER_URL=http://proxitok-signer:8080/signature
    volumes:
    - proxitok-cache:/cache
    depends_on:
    - proxitok-redis
    - proxitok-signer
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.proxitok-web-redirect.redirectscheme.scheme: https
      traefik.http.services.proxitok.loadbalancer.server.port: 8080
      traefik.http.routers.proxitok-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.proxitok-insecure.entrypoints: web
      traefik.http.routers.proxitok-insecure.service: proxitok
      traefik.http.routers.proxitok-insecure.middlewares: proxitok-web-redirect
      traefik.http.routers.proxitok.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.proxitok.entrypoints: websecure
      traefik.http.routers.proxitok.service: proxitok
      traefik.http.routers.proxitok.tls.certresolver: myresolver
      traefik.http.routers.proxitok-local-insecure.rule: Host(`proxitok.${LOCAL_DOMAIN}`)
      traefik.http.routers.proxitok-local-insecure.entrypoints: web
      traefik.http.routers.proxitok-local-insecure.service: proxitok
      traefik.http.routers.proxitok-local-insecure.middlewares: proxitok-web-redirect
      traefik.http.routers.proxitok-local.rule: Host(`proxitok.${LOCAL_DOMAIN}`)
      traefik.http.routers.proxitok-local.entrypoints: websecure
      traefik.http.routers.proxitok-local.service: proxitok
      traefik.http.routers.proxitok-local.tls: true
      runtipi.managed: true
  proxitok-redis:
    container_name: proxitok-redis
    image: redis:7-alpine
    restart: unless-stopped
    command: redis-server --save 60 1 --loglevel warning
    networks:
    - tipi_main_network
    labels:
      runtipi.managed: true
  proxitok-signer:
    container_name: proxitok-signer
    image: ghcr.io/pablouser1/signtok:master
    networks:
    - tipi_main_network
    labels:
      runtipi.managed: true
volumes:
  proxitok-cache: null
 
```
