# Checklist
## Dynamic compose for planka
This is a planka update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://planka.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/user-avatars:/app/public/user-avatars
- [ ] ${APP_DATA_DIR}/data/project-background-images:/app/public/project-background-images
- [ ] ${APP_DATA_DIR}/data/attachments:/app/private/attachments
- [ ] ${APP_DATA_DIR}/data/postgres:/var/lib/postgresql/data
##### Specific instructions :
- [ ] 🌳 Environment
- [ ] ⌨ Command
- [ ] 🔗 Depends on

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "planka",
      "image": "ghcr.io/plankanban/planka:1.24.3",
      "isMain": true,
      "internalPort": 1337,
      "environment": {
        "BASE_URL": "${APP_PROTOCOL:-http}://${APP_DOMAIN}",
        "TRUST_PROXY": "1",
        "DATABASE_URL": "\"postgresql://postgres@postgres/planka\"",
        "SECRET_KEY": "\"${PLANKA_SECRET_KEY}\""
      },
      "dependsOn": [
        "postgres"
      ],
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/user-avatars",
          "containerPath": "/app/public/user-avatars"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/project-background-images",
          "containerPath": "/app/public/project-background-images"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/attachments",
          "containerPath": "/app/private/attachments"
        }
      ],
      "command": "bash -c\n  \"for i in `seq 1 30`; do\n    ./start.sh &&\n    s=$$? && break || s=$$?;\n    echo \\\"Tried $$i times. Waiting 5 seconds...\\\";\n    sleep 5;\n  done; (exit $$s)\"\n"
    },
    {
      "name": "postgres",
      "image": "postgres:14-alpine",
      "environment": {
        "POSTGRES_DB": "planka",
        "POSTGRES_HOST_AUTH_METHOD": "trust"
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
version: '3'
services:
  planka:
    image: ghcr.io/plankanban/planka:1.24.3
    container_name: planka
    command: "bash -c\n  \"for i in `seq 1 30`; do\n    ./start.sh &&\n    s=$$? &&\
      \ break || s=$$?;\n    echo \\\"Tried $$i times. Waiting 5 seconds...\\\";\n\
      \    sleep 5;\n  done; (exit $$s)\"\n"
    restart: unless-stopped
    volumes:
    - ${APP_DATA_DIR}/data/user-avatars:/app/public/user-avatars
    - ${APP_DATA_DIR}/data/project-background-images:/app/public/project-background-images
    - ${APP_DATA_DIR}/data/attachments:/app/private/attachments
    ports:
    - ${APP_PORT}:1337
    environment:
    - BASE_URL=${APP_PROTOCOL:-http}://${APP_DOMAIN}
    - TRUST_PROXY=1
    - DATABASE_URL="postgresql://postgres@postgres/planka"
    - SECRET_KEY="${PLANKA_SECRET_KEY}"
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.planka-web-redirect.redirectscheme.scheme: https
      traefik.http.services.planka.loadbalancer.server.port: 1337
      traefik.http.routers.planka-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.planka-insecure.entrypoints: web
      traefik.http.routers.planka-insecure.service: planka
      traefik.http.routers.planka-insecure.middlewares: planka-web-redirect
      traefik.http.routers.planka.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.planka.entrypoints: websecure
      traefik.http.routers.planka.service: planka
      traefik.http.routers.planka.tls.certresolver: myresolver
      traefik.http.routers.planka-local-insecure.rule: Host(`planka.${LOCAL_DOMAIN}`)
      traefik.http.routers.planka-local-insecure.entrypoints: web
      traefik.http.routers.planka-local-insecure.service: planka
      traefik.http.routers.planka-local-insecure.middlewares: planka-web-redirect
      traefik.http.routers.planka-local.rule: Host(`planka.${LOCAL_DOMAIN}`)
      traefik.http.routers.planka-local.entrypoints: websecure
      traefik.http.routers.planka-local.service: planka
      traefik.http.routers.planka-local.tls: true
      runtipi.managed: true
    depends_on:
    - postgres
  postgres:
    image: postgres:14-alpine
    container_name: planka-db
    restart: unless-stopped
    volumes:
    - ${APP_DATA_DIR}/data/postgres:/var/lib/postgresql/data
    environment:
      POSTGRES_DB: planka
      POSTGRES_HOST_AUTH_METHOD: trust
    networks:
    - tipi_main_network
    labels:
      runtipi.managed: true
 
```
