# Checklist
## Dynamic compose for peppermint
This is a peppermint update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://peppermint.tipi.local
- [ ] Additionnal Port(s)
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

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "peppermint",
      "image": "pepperlabs/peppermint:latest",
      "isMain": true,
      "internalPort": 3000,
      "addPorts": [
        {
          "hostPort": 8217,
          "containerPort": 5003
        }
      ],
      "environment": {
        "DB_USERNAME": "tipi",
        "DB_PASSWORD": "${PEPPERMINT_DB_PASSWORD}",
        "DB_HOST": "peppermint-db",
        "API_URL": "{APP_PROTOCOL:-http}://${PEPPERMINT_DOMAIN_API}"
      },
      "dependsOn": [
        "peppermint-db"
      ]
    },
    {
      "name": "peppermint-db",
      "image": "postgres:latest",
      "environment": {
        "POSTGRES_USER": "tipi",
        "POSTGRES_PASSWORD": "${PEPPERMINT_DB_PASSWORD}",
        "POSTGRES_DB": "peppermint"
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
  peppermint:
    image: pepperlabs/peppermint:latest
    container_name: peppermint
    environment:
    - DB_USERNAME=tipi
    - DB_PASSWORD=${PEPPERMINT_DB_PASSWORD}
    - DB_HOST=peppermint-db
    - API_URL={APP_PROTOCOL:-http}://${PEPPERMINT_DOMAIN_API}
    restart: unless-stopped
    ports:
    - ${APP_PORT}:3000
    - 8217:5003
    depends_on:
    - peppermint-db
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.peppermint-web-redirect.redirectscheme.scheme: https
      traefik.http.services.peppermint.loadbalancer.server.port: 3000
      traefik.http.routers.peppermint-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.peppermint-insecure.entrypoints: web
      traefik.http.routers.peppermint-insecure.service: peppermint
      traefik.http.routers.peppermint-insecure.middlewares: peppermint-web-redirect
      traefik.http.routers.peppermint.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.peppermint.entrypoints: websecure
      traefik.http.routers.peppermint.service: peppermint
      traefik.http.routers.peppermint.tls.certresolver: myresolver
      traefik.http.routers.peppermint-local-insecure.rule: Host(`peppermint.${LOCAL_DOMAIN}`)
      traefik.http.routers.peppermint-local-insecure.entrypoints: web
      traefik.http.routers.peppermint-local-insecure.service: peppermint
      traefik.http.routers.peppermint-local-insecure.middlewares: peppermint-web-redirect
      traefik.http.routers.peppermint-local.rule: Host(`peppermint.${LOCAL_DOMAIN}`)
      traefik.http.routers.peppermint-local.entrypoints: websecure
      traefik.http.routers.peppermint-local.service: peppermint
      traefik.http.routers.peppermint-local.tls: true
      traefik.http.middlewares.peppermint-api-web-redirect.redirectscheme.scheme: https
      traefik.http.services.peppermint-api.loadbalancer.server.port: 5003
      traefik.http.routers.peppermint-api-insecure.rule: Host(`${PEPPERMINT_DOMAIN_API}`)
      traefik.http.routers.peppermint-api-insecure.entrypoints: web
      traefik.http.routers.peppermint-api-insecure.service: peppermint
      traefik.http.routers.ppeppermint-api-insecure.middlewares: peppermint-api-web-redirect
      traefik.http.routers.peppermint-api.rule: Host(`${PEPPERMINT_DOMAIN_API}`)
      traefik.http.routers.peppermint-api.entrypoints: websecure
      traefik.http.routers.peppermint-api.service: peppermint
      traefik.http.routers.peppermint-api.tls.certresolver: myresolver
      traefik.http.routers.peppermint-api-local-insecure.rule: Host(`peppermintapi.${LOCAL_DOMAIN}`)
      traefik.http.routers.peppermint-api-local-insecure.entrypoints: web
      traefik.http.routers.peppermint-api-local-insecure.service: peppermint
      traefik.http.routers.peppermint-api-local-insecure.middlewares: peppermint-api-web-redirect
      traefik.http.routers.peppermint-api-local.rule: Host(`peppermintapi.${LOCAL_DOMAIN}`)
      traefik.http.routers.peppermint-api-local.entrypoints: websecure
      traefik.http.routers.peppermint-api-local.service: peppermint
      traefik.http.routers.peppermint-api-local.tls: true
      runtipi.managed: true
  peppermint-db:
    container_name: peppermint-db
    image: postgres:latest
    restart: unless-stopped
    environment:
    - POSTGRES_USER=tipi
    - POSTGRES_PASSWORD=${PEPPERMINT_DB_PASSWORD}
    - POSTGRES_DB=peppermint
    volumes:
    - ${APP_DATA_DIR}/data/postgres:/var/lib/postgresql/data
    networks:
    - tipi_main_network
    labels:
      runtipi.managed: true
 
```
