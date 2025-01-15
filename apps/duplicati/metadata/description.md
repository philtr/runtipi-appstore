# Checklist
## Dynamic compose for duplicati
This is a duplicati update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://duplicati.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/duplicati-config:/config
- [ ] ${ROOT_FOLDER_HOST}:/backups/runtipi-folder
- [ ] /home:/source
##### Specific instructions :
- [ ] 🌳 Environment

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "duplicati",
      "image": "lscr.io/linuxserver/duplicati:2.1.0",
      "isMain": true,
      "internalPort": 8200,
      "environment": {
        "PUID": "0",
        "PGID": "0",
        "TZ": "${TZ}",
        "SETTINGS_ENCRYPTION_KEY": "${DUPLICATI_ENCRYPTION_KEY}"
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/duplicati-config",
          "containerPath": "/config"
        },
        {
          "hostPath": "${ROOT_FOLDER_HOST}",
          "containerPath": "/backups/runtipi-folder"
        },
        {
          "hostPath": "/home",
          "containerPath": "/source"
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
  duplicati:
    container_name: duplicati
    image: lscr.io/linuxserver/duplicati:2.1.0
    ports:
    - ${APP_PORT}:8200
    volumes:
    - ${APP_DATA_DIR}/data/duplicati-config:/config
    - ${ROOT_FOLDER_HOST}:/backups/runtipi-folder
    - /home:/source
    environment:
    - PUID=0
    - PGID=0
    - TZ=${TZ}
    - SETTINGS_ENCRYPTION_KEY=${DUPLICATI_ENCRYPTION_KEY}
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.duplicati-web-redirect.redirectscheme.scheme: https
      traefik.http.services.duplicati.loadbalancer.server.port: 8200
      traefik.http.routers.duplicati-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.duplicati-insecure.entrypoints: web
      traefik.http.routers.duplicati-insecure.service: duplicati
      traefik.http.routers.duplicati-insecure.middlewares: duplicati-web-redirect
      traefik.http.routers.duplicati.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.duplicati.entrypoints: websecure
      traefik.http.routers.duplicati.service: duplicati
      traefik.http.routers.duplicati.tls.certresolver: myresolver
      traefik.http.routers.duplicati-local-insecure.rule: Host(`duplicati.${LOCAL_DOMAIN}`)
      traefik.http.routers.duplicati-local-insecure.entrypoints: web
      traefik.http.routers.duplicati-local-insecure.service: duplicati
      traefik.http.routers.duplicati-local-insecure.middlewares: duplicati-web-redirect
      traefik.http.routers.duplicati-local.rule: Host(`duplicati.${LOCAL_DOMAIN}`)
      traefik.http.routers.duplicati-local.entrypoints: websecure
      traefik.http.routers.duplicati-local.service: duplicati
      traefik.http.routers.duplicati-local.tls: true
      runtipi.managed: true
 
```
