# Checklist
## Dynamic compose for wikijs
This is a wikijs update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://wikijs.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/postgres:/var/lib/postgresql/data
##### Specific instructions :
- [ ] 🌳 Environment
- [ ] 🔗 Depends on
- [ ] 📃 Logging

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "wikijs",
      "image": "ghcr.io/requarks/wiki:2.5.305",
      "isMain": true,
      "internalPort": 3000,
      "environment": {
        "DB_TYPE": "postgres",
        "DB_HOST": "wikijs-db",
        "DB_PORT": 5432,
        "DB_USER": "wikijs",
        "DB_PASS": "${WIKI_JS_DB_PASS}",
        "DB_NAME": "wiki"
      },
      "dependsOn": [
        "wikijs-db"
      ]
    },
    {
      "name": "wikijs-db",
      "image": "postgres:11-alpine",
      "environment": {
        "POSTGRES_DB": "wiki",
        "POSTGRES_PASSWORD": "${WIKI_JS_DB_PASS}",
        "POSTGRES_USER": "wikijs"
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/postgres",
          "containerPath": "/var/lib/postgresql/data"
        }
      ],
      "logging": {
        "driver": "none",
        "options": {}
      }
    }
  ]
} 
```
# Original YAML
```yaml
version: '3.7'
services:
  wikijs:
    container_name: wikijs
    image: ghcr.io/requarks/wiki:2.5.305
    depends_on:
    - wikijs-db
    environment:
      DB_TYPE: postgres
      DB_HOST: wikijs-db
      DB_PORT: 5432
      DB_USER: wikijs
      DB_PASS: ${WIKI_JS_DB_PASS}
      DB_NAME: wiki
    restart: unless-stopped
    ports:
    - ${APP_PORT}:3000
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.wikijs-web-redirect.redirectscheme.scheme: https
      traefik.http.services.wikijs.loadbalancer.server.port: 3000
      traefik.http.routers.wikijs-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.wikijs-insecure.entrypoints: web
      traefik.http.routers.wikijs-insecure.service: wikijs
      traefik.http.routers.wikijs-insecure.middlewares: wikijs-web-redirect
      traefik.http.routers.wikijs.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.wikijs.entrypoints: websecure
      traefik.http.routers.wikijs.service: wikijs
      traefik.http.routers.wikijs.tls.certresolver: myresolver
      traefik.http.routers.wikijs-local-insecure.rule: Host(`wikijs.${LOCAL_DOMAIN}`)
      traefik.http.routers.wikijs-local-insecure.entrypoints: web
      traefik.http.routers.wikijs-local-insecure.service: wikijs
      traefik.http.routers.wikijs-local-insecure.middlewares: wikijs-web-redirect
      traefik.http.routers.wikijs-local.rule: Host(`wikijs.${LOCAL_DOMAIN}`)
      traefik.http.routers.wikijs-local.entrypoints: websecure
      traefik.http.routers.wikijs-local.service: wikijs
      traefik.http.routers.wikijs-local.tls: true
      runtipi.managed: true
  wikijs-db:
    container_name: wikijs-db
    image: postgres:11-alpine
    environment:
      POSTGRES_DB: wiki
      POSTGRES_PASSWORD: ${WIKI_JS_DB_PASS}
      POSTGRES_USER: wikijs
    logging:
      driver: none
    restart: unless-stopped
    networks:
    - tipi_main_network
    volumes:
    - ${APP_DATA_DIR}/data/postgres:/var/lib/postgresql/data
    labels:
      runtipi.managed: true
 
```
