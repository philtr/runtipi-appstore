# Checklist
## Dynamic compose for crowdsec
This is a crowdsec update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://crowdsec.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] /etc/localtime:/etc/localtime:ro
- [ ] /var/run/docker.sock:/var/run/docker.sock:ro
- [ ] ${APP_DATA_DIR}/data/crowdsec/acquis.yaml:/etc/crowdsec/acquis.yaml
- [ ] ${APP_DATA_DIR}/data/crowdsec:/etc/crowdsec
- [ ] ${APP_DATA_DIR}/data/crowdsec/db:/var/lib/crowdsec/data
- [ ] /var/log/auth.log:/var/log/auth.log:ro
- [ ] /var/log/traefik:/var/log/traefik:ro
- [ ] /var/log/crowdsec:/var/log/crowdsec:ro
- [ ] ${APP_DATA_DIR}/data/crowdsec-dashboard/data:/data
- [ ] ${APP_DATA_DIR}/data/crowdsec/db:/metabase-data
##### Specific instructions :
- [ ] 🌳 Environment
- [ ] 🔗 Depends on

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "crowdsec",
      "image": "crowdsecurity/crowdsec:v1.6.4",
      "isMain": true,
      "environment": {
        "COLLECTIONS": " crowdsecurity/linux crowdsecurity/traefik crowdsecurity/http-cve crowdsecurity/whitelist-good-actors crowdsecurity/sshd",
        "GID": "${GID-1000}"
      },
      "volumes": [
        {
          "hostPath": "/etc/localtime",
          "containerPath": "/etc/localtime",
          "readOnly": true
        },
        {
          "hostPath": "/var/run/docker.sock",
          "containerPath": "/var/run/docker.sock",
          "readOnly": true
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/crowdsec/acquis.yaml",
          "containerPath": "/etc/crowdsec/acquis.yaml"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/crowdsec",
          "containerPath": "/etc/crowdsec"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/crowdsec/db",
          "containerPath": "/var/lib/crowdsec/data"
        },
        {
          "hostPath": "/var/log/auth.log",
          "containerPath": "/var/log/auth.log",
          "readOnly": true
        },
        {
          "hostPath": "/var/log/traefik",
          "containerPath": "/var/log/traefik",
          "readOnly": true
        },
        {
          "hostPath": "/var/log/crowdsec",
          "containerPath": "/var/log/crowdsec",
          "readOnly": true
        }
      ]
    },
    {
      "name": "crowdsec-bouncer-traefik",
      "image": "fbonalair/traefik-crowdsec-bouncer:latest",
      "environment": {
        "CROWDSEC_BOUNCER_API_KEY": "${CROWDSEC_BOUNCER_API_KEY}",
        "CROWDSEC_AGENT_HOST": "crowdsec:8080"
      },
      "dependsOn": [
        "crowdsec"
      ]
    },
    {
      "name": "crowdsec-dashboard",
      "image": "metabase/metabase",
      "internalPort": 3000,
      "environment": {
        "MB_DB_FILE": "/data/metabase.db",
        "MGID": "${GID-1000}"
      },
      "dependsOn": [
        "crowdsec"
      ],
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/crowdsec-dashboard/data",
          "containerPath": "/data"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/crowdsec/db",
          "containerPath": "/metabase-data"
        }
      ]
    }
  ]
} 
```
# Original YAML
```yaml
services:
  crowdsec:
    container_name: crowdsec
    image: crowdsecurity/crowdsec:v1.6.4
    restart: unless-stopped
    volumes:
    - /etc/localtime:/etc/localtime:ro
    - /var/run/docker.sock:/var/run/docker.sock:ro
    - ${APP_DATA_DIR}/data/crowdsec/acquis.yaml:/etc/crowdsec/acquis.yaml
    - ${APP_DATA_DIR}/data/crowdsec:/etc/crowdsec
    - ${APP_DATA_DIR}/data/crowdsec/db:/var/lib/crowdsec/data
    - /var/log/auth.log:/var/log/auth.log:ro
    - /var/log/traefik:/var/log/traefik:ro
    - /var/log/crowdsec:/var/log/crowdsec:ro
    environment:
    - COLLECTIONS= crowdsecurity/linux crowdsecurity/traefik crowdsecurity/http-cve
      crowdsecurity/whitelist-good-actors crowdsecurity/sshd
    - GID=${GID-1000}
    networks:
    - tipi_main_network
    labels:
      runtipi.managed: true
  crowdsec-bouncer-traefik:
    container_name: crowdsec-bouncer-traefik
    image: fbonalair/traefik-crowdsec-bouncer:latest
    restart: unless-stopped
    depends_on:
    - crowdsec
    environment:
    - CROWDSEC_BOUNCER_API_KEY=${CROWDSEC_BOUNCER_API_KEY}
    - CROWDSEC_AGENT_HOST=crowdsec:8080
    networks:
    - tipi_main_network
    labels:
      runtipi.managed: true
  crowdsec-dashboard:
    container_name: crowdsec-dashboard
    image: metabase/metabase
    restart: unless-stopped
    ports:
    - ${APP_PORT}:3000
    environment:
    - MB_DB_FILE=/data/metabase.db
    - MGID=${GID-1000}
    depends_on:
    - crowdsec
    volumes:
    - ${APP_DATA_DIR}/data/crowdsec-dashboard/data:/data
    - ${APP_DATA_DIR}/data/crowdsec/db:/metabase-data
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.crowdsec-web-redirect.redirectscheme.scheme: https
      traefik.http.services.crowdsec.loadbalancer.server.port: 3000
      traefik.http.routers.crowdsec-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.crowdsec-insecure.entrypoints: web
      traefik.http.routers.crowdsec-insecure.service: crowdsec
      traefik.http.routers.crowdsec-insecure.middlewares: crowdsec-web-redirect
      traefik.http.routers.crowdsec.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.crowdsec.entrypoints: websecure
      traefik.http.routers.crowdsec.service: crowdsec
      traefik.http.routers.crowdsec.tls.certresolver: myresolver
      traefik.http.routers.crowdsec-local-insecure.rule: Host(`crowdsec.${LOCAL_DOMAIN}`)
      traefik.http.routers.crowdsec-local-insecure.entrypoints: web
      traefik.http.routers.crowdsec-local-insecure.service: crowdsec
      traefik.http.routers.crowdsec-local-insecure.middlewares: crowdsec-web-redirect
      traefik.http.routers.crowdsec-local.rule: Host(`crowdsec.${LOCAL_DOMAIN}`)
      traefik.http.routers.crowdsec-local.entrypoints: websecure
      traefik.http.routers.crowdsec-local.service: crowdsec
      traefik.http.routers.crowdsec-local.tls: true
      runtipi.managed: true
 
```
