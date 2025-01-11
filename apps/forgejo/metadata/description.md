# Checklist
## Dynamic compose for forgejo
This is a forgejo update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://forgejo.tipi.local
- [ ] Additionnal Port(s)
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/forgejo:/data
- [ ] ${APP_DATA_DIR}/data/postgres:/var/lib/postgresql/data
##### Specific instructions :
- [ ] 🌳 Environment
- [ ] 🔗 Depends on

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "forgejo",
      "image": "codeberg.org/forgejo/forgejo:1.21.11-0",
      "isMain": true,
      "internalPort": 3000,
      "addPorts": [
        {
          "hostPort": 222,
          "containerPort": 22
        }
      ],
      "environment": {
        "USER_UID": "1000",
        "USER_GID": "1000",
        "FORGEJO__database__DB_TYPE": "postgres",
        "FORGEJO__database__HOST": "forgejo-db:5432",
        "FORGEJO__database__NAME": "forgejo",
        "FORGEJO__database__USER": "forgejo",
        "FORGEJO__database__PASSWD": "${FORGEJO_DB_PASSWORD}"
      },
      "dependsOn": [
        "forgejo-db"
      ],
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/forgejo",
          "containerPath": "/data"
        }
      ]
    },
    {
      "name": "forgejo-db",
      "image": "postgres:14",
      "environment": {
        "POSTGRES_USER": "forgejo",
        "POSTGRES_PASSWORD": "${FORGEJO_DB_PASSWORD}",
        "POSTGRES_DB": "forgejo"
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/postgres",
          "containerPath": "/var/lib/postgresql/data"
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
  forgejo:
    image: codeberg.org/forgejo/forgejo:1.21.11-0
    container_name: forgejo
    environment:
    - USER_UID=1000
    - USER_GID=1000
    - FORGEJO__database__DB_TYPE=postgres
    - FORGEJO__database__HOST=forgejo-db:5432
    - FORGEJO__database__NAME=forgejo
    - FORGEJO__database__USER=forgejo
    - FORGEJO__database__PASSWD=${FORGEJO_DB_PASSWORD}
    restart: unless-stopped
    volumes:
    - ${APP_DATA_DIR}/data/forgejo:/data
    ports:
    - ${APP_PORT}:3000
    - '222:22'
    depends_on:
    - forgejo-db
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.forgejo-web-redirect.redirectscheme.scheme: https
      traefik.http.services.forgejo.loadbalancer.server.port: 3000
      traefik.http.routers.forgejo-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.forgejo-insecure.entrypoints: web
      traefik.http.routers.forgejo-insecure.service: forgejo
      traefik.http.routers.forgejo-insecure.middlewares: forgejo-web-redirect
      traefik.http.routers.forgejo.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.forgejo.entrypoints: websecure
      traefik.http.routers.forgejo.service: forgejo
      traefik.http.routers.forgejo.tls.certresolver: myresolver
      traefik.http.routers.forgejo-local-insecure.rule: Host(`forgejo.${LOCAL_DOMAIN}`)
      traefik.http.routers.forgejo-local-insecure.entrypoints: web
      traefik.http.routers.forgejo-local-insecure.service: forgejo
      traefik.http.routers.forgejo-local-insecure.middlewares: forgejo-web-redirect
      traefik.http.routers.forgejo-local.rule: Host(`forgejo.${LOCAL_DOMAIN}`)
      traefik.http.routers.forgejo-local.entrypoints: websecure
      traefik.http.routers.forgejo-local.service: forgejo
      traefik.http.routers.forgejo-local.tls: true
      runtipi.managed: true
  forgejo-db:
    container_name: forgejo-db
    image: postgres:14
    restart: unless-stopped
    environment:
    - POSTGRES_USER=forgejo
    - POSTGRES_PASSWORD=${FORGEJO_DB_PASSWORD}
    - POSTGRES_DB=forgejo
    volumes:
    - ${APP_DATA_DIR}/data/postgres:/var/lib/postgresql/data
    networks:
    - tipi_main_network
    labels:
      runtipi.managed: true
 
```
