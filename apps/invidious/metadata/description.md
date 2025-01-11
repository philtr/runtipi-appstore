# Checklist
## Dynamic compose for invidious
This is a invidious update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://invidious.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/postgres:/var/lib/postgresql/data
- [ ] ${APP_DATA_DIR}/data/init/sql:/config/sql
- [ ] ${APP_DATA_DIR}/data/init/init-invidious-db.sh:/docker-entrypoint-initdb.d/init-invidious-db.sh
##### Specific instructions :
- [ ] 🌳 Environment
- [ ] 🩺 Healthcheck
- [ ] 🔗 Depends on
- [ ] ⌨ Command
- [ ] ▶ Read only
- [ ] 🔒 Capacity drop
- [ ] Security options 🔐

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "invidious",
      "image": "quay.io/invidious/invidious:master",
      "isMain": true,
      "internalPort": 3000,
      "environment": {
        "INVIDIOUS_CONFIG": "db:\n  dbname: invidious\n  user: tipi\n  password: tipi\n  host: invidious-db\n  port: 5432\ncheck_tables: true\nhmac_key: ${INVIDIOUS_HMAC_KEY}\nuse_innertube_for_captions: true\ndomain: ${INVIDIOUS_DOMAIN}\nexternal_port: ${INVIDIOUS_EXTERNAL_PORT:-${APP_PORT}}\nhttps_only: ${INVIDIOUS_HTTPS_ONLY}\nsignature_server: inv_sig_helper:12999\nvisitor_data: ${INVIDIOUS_VISITOR_DATA}\npo_token: ${INVIDIOUS_PO_TOKEN}\n"
      },
      "dependsOn": {
        "invidious-db": {
          "condition": "service_healthy"
        },
        "inv_sig_helper": {
          "condition": "service_started"
        }
      },
      "healthCheck": {
        "interval": "30s",
        "timeout": "5s",
        "retries": 2,
        "test": "wget -nv --tries=1 --spider http://127.0.0.1:3000/api/v1/trending || exit 1"
      }
    },
    {
      "name": "inv_sig_helper",
      "image": "quay.io/invidious/inv-sig-helper:latest",
      "environment": {
        "RUST_LOG": "info"
      },
      "readOnly": true,
      "command": [
        "--tcp",
        "0.0.0.0:12999"
      ],
      "capDrop": [
        "ALL"
      ],
      "securityOpt": [
        "no-new-privileges:true"
      ]
    },
    {
      "name": "invidious-db",
      "image": "postgres:14",
      "environment": {
        "POSTGRES_DB": "invidious",
        "POSTGRES_USER": "tipi",
        "POSTGRES_PASSWORD": "tipi"
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/postgres",
          "containerPath": "/var/lib/postgresql/data"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/init/sql",
          "containerPath": "/config/sql"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/init/init-invidious-db.sh",
          "containerPath": "/docker-entrypoint-initdb.d/init-invidious-db.sh"
        }
      ],
      "healthCheck": {
        "test": "pg_isready -U $$POSTGRES_USER -d $$POSTGRES_DB"
      }
    }
  ]
} 
```
# Original YAML
```yaml
version: '3.7'
services:
  invidious:
    container_name: invidious
    image: quay.io/invidious/invidious:master
    restart: unless-stopped
    depends_on:
      invidious-db:
        condition: service_healthy
      inv_sig_helper:
        condition: service_started
    ports:
    - ${APP_PORT}:3000
    environment:
      INVIDIOUS_CONFIG: "db:\n  dbname: invidious\n  user: tipi\n  password: tipi\n\
        \  host: invidious-db\n  port: 5432\ncheck_tables: true\nhmac_key: ${INVIDIOUS_HMAC_KEY}\n\
        use_innertube_for_captions: true\ndomain: ${INVIDIOUS_DOMAIN}\nexternal_port:\
        \ ${INVIDIOUS_EXTERNAL_PORT:-${APP_PORT}}\nhttps_only: ${INVIDIOUS_HTTPS_ONLY}\n\
        signature_server: inv_sig_helper:12999\nvisitor_data: ${INVIDIOUS_VISITOR_DATA}\n\
        po_token: ${INVIDIOUS_PO_TOKEN}\n"
    healthcheck:
      test: wget -nv --tries=1 --spider http://127.0.0.1:3000/api/v1/trending || exit
        1
      interval: 30s
      timeout: 5s
      retries: 2
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.invidious-web-redirect.redirectscheme.scheme: https
      traefik.http.services.invidious.loadbalancer.server.port: 3000
      traefik.http.routers.invidious-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.invidious-insecure.entrypoints: web
      traefik.http.routers.invidious-insecure.service: invidious
      traefik.http.routers.invidious-insecure.middlewares: invidious-web-redirect
      traefik.http.routers.invidious.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.invidious.entrypoints: websecure
      traefik.http.routers.invidious.service: invidious
      traefik.http.routers.invidious.tls.certresolver: myresolver
      traefik.http.routers.invidious-local-insecure.rule: Host(`invidious.${LOCAL_DOMAIN}`)
      traefik.http.routers.invidious-local-insecure.entrypoints: web
      traefik.http.routers.invidious-local-insecure.service: invidious
      traefik.http.routers.invidious-local-insecure.middlewares: invidious-web-redirect
      traefik.http.routers.invidious-local.rule: Host(`invidious.${LOCAL_DOMAIN}`)
      traefik.http.routers.invidious-local.entrypoints: websecure
      traefik.http.routers.invidious-local.service: invidious
      traefik.http.routers.invidious-local.tls: true
      runtipi.managed: true
  inv_sig_helper:
    container_name: inv_sig_helper
    image: quay.io/invidious/inv-sig-helper:latest
    command:
    - --tcp
    - 0.0.0.0:12999
    environment:
    - RUST_LOG=info
    restart: unless-stopped
    cap_drop:
    - ALL
    read_only: true
    security_opt:
    - no-new-privileges:true
    networks:
    - tipi_main_network
    labels:
      runtipi.managed: true
  invidious-db:
    container_name: invidious-db
    image: postgres:14
    restart: unless-stopped
    volumes:
    - ${APP_DATA_DIR}/data/postgres:/var/lib/postgresql/data
    - ${APP_DATA_DIR}/data/init/sql:/config/sql
    - ${APP_DATA_DIR}/data/init/init-invidious-db.sh:/docker-entrypoint-initdb.d/init-invidious-db.sh
    environment:
      POSTGRES_DB: invidious
      POSTGRES_USER: tipi
      POSTGRES_PASSWORD: tipi
    healthcheck:
      test:
      - CMD-SHELL
      - pg_isready -U $$POSTGRES_USER -d $$POSTGRES_DB
    networks:
    - tipi_main_network
    labels:
      runtipi.managed: true
 
```
