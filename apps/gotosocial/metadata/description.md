# Checklist
## Dynamic compose for gotosocial
This is a gotosocial update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://gotosocial.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/gotosocial:/gotosocial/storage
- [ ] ${APP_DATA_DIR}/data/db:/var/lib/postgresql/data
##### Specific instructions :
- [ ] 🌳 Environment
- [ ] 👤 User (1000:1000)
- [ ] 🔗 Depends on

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "gotosocial",
      "image": "superseriousbusiness/gotosocial:0.17.3",
      "isMain": true,
      "internalPort": 8080,
      "user": "1000:1000",
      "environment": {
        "GTS_HOST": "${APP_DOMAIN}",
        "GTS_LETSENCRYPT_ENABLED": "false",
        "GTS_DB_TYPE": "postgres",
        "GTS_DB_ADDRESS": "gotosocial-db",
        "GTS_DB_PORT": "5432",
        "GTS_DB_USER": "tipi",
        "GTS_DB_PASSWORD": "${GTS_DB_PASSWORD}",
        "GTS_DB_DATABASE": "gotosocial-db",
        "GTS_ACCOUNTS_REGISTRATION_OPEN": "${GTS_ACCOUNTS_REGISTRATION_OPEN}",
        "GTS_SMTP_HOST": "${GTS_SMTP_HOST}",
        "GTS_SMTP_PORT": "${GTS_SMTP_PORT}",
        "GTS_SMTP_USERNAME": "${GTS_SMTP_USERNAME}",
        "GTS_SMTP_PASSWORD": "${GTS_SMTP_PASSWORD}",
        "GTS_SMTP_FROM": "${GTS_SMTP_FROM}"
      },
      "dependsOn": [
        "gotosocial-db"
      ],
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/gotosocial",
          "containerPath": "/gotosocial/storage"
        }
      ]
    },
    {
      "name": "gotosocial-db",
      "image": "postgres:14",
      "environment": {
        "POSTGRES_PASSWORD": "${GTS_DB_PASSWORD}",
        "POSTGRES_USER": "tipi",
        "POSTGRES_DB": "gotosocial-db",
        "PG_DATA": "/var/lib/postgresql/data"
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/db",
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
  gotosocial:
    container_name: gotosocial
    image: superseriousbusiness/gotosocial:0.17.3
    user: 1000:1000
    ports:
    - ${APP_PORT}:8080
    volumes:
    - ${APP_DATA_DIR}/data/gotosocial:/gotosocial/storage
    depends_on:
    - gotosocial-db
    environment:
    - GTS_HOST=${APP_DOMAIN}
    - GTS_LETSENCRYPT_ENABLED=false
    - GTS_DB_TYPE=postgres
    - GTS_DB_ADDRESS=gotosocial-db
    - GTS_DB_PORT=5432
    - GTS_DB_USER=tipi
    - GTS_DB_PASSWORD=${GTS_DB_PASSWORD}
    - GTS_DB_DATABASE=gotosocial-db
    - GTS_ACCOUNTS_REGISTRATION_OPEN=${GTS_ACCOUNTS_REGISTRATION_OPEN}
    - GTS_SMTP_HOST=${GTS_SMTP_HOST}
    - GTS_SMTP_PORT=${GTS_SMTP_PORT}
    - GTS_SMTP_USERNAME=${GTS_SMTP_USERNAME}
    - GTS_SMTP_PASSWORD=${GTS_SMTP_PASSWORD}
    - GTS_SMTP_FROM=${GTS_SMTP_FROM}
    restart: unless-stopped
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.gotosocial-web-redirect.redirectscheme.scheme: https
      traefik.http.services.gotosocial.loadbalancer.server.port: 8080
      traefik.http.routers.gotosocial-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.gotosocial-insecure.entrypoints: web
      traefik.http.routers.gotosocial-insecure.service: gotosocial
      traefik.http.routers.gotosocial-insecure.middlewares: gotosocial-web-redirect
      traefik.http.routers.gotosocial.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.gotosocial.entrypoints: websecure
      traefik.http.routers.gotosocial.service: gotosocial
      traefik.http.routers.gotosocial.tls.certresolver: myresolver
      traefik.http.routers.gotosocial-local-insecure.rule: Host(`gotosocial.${LOCAL_DOMAIN}`)
      traefik.http.routers.gotosocial-local-insecure.entrypoints: web
      traefik.http.routers.gotosocial-local-insecure.service: gotosocial
      traefik.http.routers.gotosocial-local-insecure.middlewares: gotosocial-web-redirect
      traefik.http.routers.gotosocial-local.rule: Host(`gotosocial.${LOCAL_DOMAIN}`)
      traefik.http.routers.gotosocial-local.entrypoints: websecure
      traefik.http.routers.gotosocial-local.service: gotosocial
      traefik.http.routers.gotosocial-local.tls: true
      runtipi.managed: true
  gotosocial-db:
    container_name: gotosocial-db
    image: postgres:14
    environment:
      POSTGRES_PASSWORD: ${GTS_DB_PASSWORD}
      POSTGRES_USER: tipi
      POSTGRES_DB: gotosocial-db
      PG_DATA: /var/lib/postgresql/data
    volumes:
    - ${APP_DATA_DIR}/data/db:/var/lib/postgresql/data
    restart: unless-stopped
    networks:
    - tipi_main_network
    labels:
      runtipi.managed: true
 
```
