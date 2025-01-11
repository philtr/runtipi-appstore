# Checklist
## Dynamic compose for speedtest-tracker
This is a speedtest-tracker update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://speedtest-tracker.tipi.local
- [ ] Additionnal Port(s)
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/speedtest-tracker/config:/config
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
      "name": "speedtest-tracker",
      "image": "lscr.io/linuxserver/speedtest-tracker:1.0.3",
      "isMain": true,
      "internalPort": 80,
      "addPorts": [
        {
          "hostPort": 8212,
          "containerPort": 443
        }
      ],
      "environment": {
        "PUID": "1000",
        "PGID": "1000",
        "DB_CONNECTION": "pgsql",
        "DB_HOST": "speedtest-tracker-db",
        "DB_PORT": "5432",
        "DB_DATABASE": "speedtest-tracker",
        "DB_USERNAME": "tipi",
        "DB_PASSWORD": "${SPEEDTEST_TRACKER_DB_PASSWORD}",
        "SPEEDTEST_SCHEDULE": "${SPEEDTEST_SCHEDULE:-'30 * * * *'}",
        "SPEEDTEST_SERVERS": "${SPEEDTEST_SERVERS:-''}",
        "TZ": "${TZ}",
        "APP_TIMEZONE": "${TZ}",
        "DISPLAY_TIMEZONE": "${TZ}",
        "APP_KEY": "${SPEEDTEST_APP_KEY}"
      },
      "dependsOn": [
        "speedtest-tracker-db"
      ],
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/speedtest-tracker/config",
          "containerPath": "/config"
        }
      ]
    },
    {
      "name": "speedtest-tracker-db",
      "image": "postgres:15",
      "environment": {
        "POSTGRES_USER": "tipi",
        "POSTGRES_PASSWORD": "${SPEEDTEST_TRACKER_DB_PASSWORD}",
        "POSTGRES_DB": "speedtest-tracker"
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
services:
  speedtest-tracker:
    image: lscr.io/linuxserver/speedtest-tracker:1.0.3
    container_name: speedtest-tracker
    environment:
    - PUID=1000
    - PGID=1000
    - DB_CONNECTION=pgsql
    - DB_HOST=speedtest-tracker-db
    - DB_PORT=5432
    - DB_DATABASE=speedtest-tracker
    - DB_USERNAME=tipi
    - DB_PASSWORD=${SPEEDTEST_TRACKER_DB_PASSWORD}
    - SPEEDTEST_SCHEDULE=${SPEEDTEST_SCHEDULE:-'30 * * * *'}
    - SPEEDTEST_SERVERS=${SPEEDTEST_SERVERS:-''}
    - TZ=${TZ}
    - APP_TIMEZONE=${TZ}
    - DISPLAY_TIMEZONE=${TZ}
    - APP_KEY=${SPEEDTEST_APP_KEY}
    restart: unless-stopped
    volumes:
    - ${APP_DATA_DIR}/data/speedtest-tracker/config:/config
    ports:
    - ${APP_PORT}:80
    - 8212:443
    depends_on:
    - speedtest-tracker-db
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.speedtest-tracker-web-redirect.redirectscheme.scheme: https
      traefik.http.services.speedtest-tracker.loadbalancer.server.port: 80
      traefik.http.routers.speedtest-tracker-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.speedtest-tracker-insecure.entrypoints: web
      traefik.http.routers.speedtest-tracker-insecure.service: speedtest-tracker
      traefik.http.routers.speedtest-tracker-insecure.middlewares: speedtest-tracker-web-redirect
      traefik.http.routers.speedtest-tracker.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.speedtest-tracker.entrypoints: websecure
      traefik.http.routers.speedtest-tracker.service: speedtest-tracker
      traefik.http.routers.speedtest-tracker.tls.certresolver: myresolver
      traefik.http.routers.speedtest-tracker-local-insecure.rule: Host(`speedtest-tracker.${LOCAL_DOMAIN}`)
      traefik.http.routers.speedtest-tracker-local-insecure.entrypoints: web
      traefik.http.routers.speedtest-tracker-local-insecure.service: speedtest-tracker
      traefik.http.routers.speedtest-tracker-local-insecure.middlewares: speedtest-tracker-web-redirect
      traefik.http.routers.speedtest-tracker-local.rule: Host(`speedtest-tracker.${LOCAL_DOMAIN}`)
      traefik.http.routers.speedtest-tracker-local.entrypoints: websecure
      traefik.http.routers.speedtest-tracker-local.service: speedtest-tracker
      traefik.http.routers.speedtest-tracker-local.tls: true
      runtipi.managed: true
  speedtest-tracker-db:
    container_name: speedtest-tracker-db
    image: postgres:15
    restart: unless-stopped
    environment:
    - POSTGRES_USER=tipi
    - POSTGRES_PASSWORD=${SPEEDTEST_TRACKER_DB_PASSWORD}
    - POSTGRES_DB=speedtest-tracker
    volumes:
    - ${APP_DATA_DIR}/data/postgres:/var/lib/postgresql/data
    networks:
    - tipi_main_network
    labels:
      runtipi.managed: true
 
```
