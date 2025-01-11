# Checklist
## Dynamic compose for odoo
This is a odoo update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://odoo.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/postgresql:/var/lib/postgresql/data
- [ ] ${APP_DATA_DIR}/data/addons:/mnt/extra-addons
- [ ] ${APP_DATA_DIR}/data/etc:/etc/odoo
- [ ] ${APP_DATA_DIR}/data/odoo-web-data:/var/lib/odoo
- [ ] ${APP_DATA_DIR}/data/filestore:/root/.local/share
##### Specific instructions :
- [ ] 🌳 Environment
- [ ] 👤 User (root)
- [ ] ⌨ Command
- [ ] 🔗 Depends on
- [ ] 🔤 TTY (True)

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "odoodb",
      "image": "postgres:15",
      "user": "root",
      "environment": {
        "POSTGRES_USER": "odoo",
        "POSTGRES_PASSWORD": "${ODOO_POSTGRES_PASSWORD}",
        "POSTGRES_DB": "postgres"
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/postgresql",
          "containerPath": "/var/lib/postgresql/data"
        }
      ]
    },
    {
      "name": "odoo",
      "image": "odoo:18",
      "isMain": true,
      "internalPort": 8069,
      "user": "root",
      "environment": {
        "HOST": "odoodb",
        "USER": "odoo",
        "PASSWORD": "${ODOO_POSTGRES_PASSWORD}"
      },
      "dependsOn": [
        "odoodb"
      ],
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/addons",
          "containerPath": "/mnt/extra-addons"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/etc",
          "containerPath": "/etc/odoo"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/odoo-web-data",
          "containerPath": "/var/lib/odoo"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/filestore",
          "containerPath": "/root/.local/share"
        }
      ],
      "command": "--",
      "tty": true
    }
  ]
} 
```
# Original YAML
```yaml
services:
  odoodb:
    container_name: odoodb
    image: postgres:15
    user: root
    environment:
    - POSTGRES_USER=odoo
    - POSTGRES_PASSWORD=${ODOO_POSTGRES_PASSWORD}
    - POSTGRES_DB=postgres
    restart: unless-stopped
    volumes:
    - ${APP_DATA_DIR}/data/postgresql:/var/lib/postgresql/data
    networks:
    - tipi_main_network
    labels:
      runtipi.managed: true
  odoo:
    container_name: odoo
    image: odoo:18
    user: root
    depends_on:
    - odoodb
    ports:
    - ${APP_PORT}:8069
    tty: true
    command: --
    environment:
    - HOST=odoodb
    - USER=odoo
    - PASSWORD=${ODOO_POSTGRES_PASSWORD}
    volumes:
    - ${APP_DATA_DIR}/data/addons:/mnt/extra-addons
    - ${APP_DATA_DIR}/data/etc:/etc/odoo
    - ${APP_DATA_DIR}/data/odoo-web-data:/var/lib/odoo
    - ${APP_DATA_DIR}/data/filestore:/root/.local/share
    restart: unless-stopped
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.odoo-web-redirect.redirectscheme.scheme: https
      traefik.http.services.odoo.loadbalancer.server.port: 8069
      traefik.http.routers.odoo-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.odoo-insecure.entrypoints: web
      traefik.http.routers.odoo-insecure.service: odoo
      traefik.http.routers.odoo-insecure.middlewares: odoo-web-redirect
      traefik.http.routers.odoo.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.odoo.entrypoints: websecure
      traefik.http.routers.odoo.service: odoo
      traefik.http.routers.odoo.tls.certresolver: myresolver
      traefik.http.routers.odoo-local-insecure.rule: Host(`odoo.${LOCAL_DOMAIN}`)
      traefik.http.routers.odoo-local-insecure.entrypoints: web
      traefik.http.routers.odoo-local-insecure.service: odoo
      traefik.http.routers.odoo-local-insecure.middlewares: odoo-web-redirect
      traefik.http.routers.odoo-local.rule: Host(`odoo.${LOCAL_DOMAIN}`)
      traefik.http.routers.odoo-local.entrypoints: websecure
      traefik.http.routers.odoo-local.service: odoo
      traefik.http.routers.odoo-local.tls: true
      runtipi.managed: true
 
```
