# Checklist
## Dynamic compose for flowise
This is a flowise update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://flowise.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/flowise:/root/.flowise
##### Specific instructions :
- [ ] 🌳 Environment
- [ ] 🚪 Entrypoint (/bin/sh -c "sleep 3; flowise start")

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "flowise",
      "image": "flowiseai/flowise:2.2.3",
      "isMain": true,
      "internalPort": 8009,
      "environment": {
        "PORT": "8009",
        "FLOWISE_USERNAME": "${FLOWISE_USERNAME}",
        "FLOWISE_PASSWORD": "${FLOWISE_PASSWORD}",
        "FLOWISE_SECRETKEY_OVERWRITE": "${FLOWISE_SECRETKEY_OVERWRITE}",
        "LANGCHAIN_ENDPOINT": "${LANGCHAIN_ENDPOINT}",
        "LANGCHAIN_API_KEY": "${LANGCHAIN_API_KEY}",
        "LANGCHAIN_PROJECT": "${LANGCHAIN_PROJECT}",
        "LANGCHAIN_TRACING_V2": "${LANGCHAIN_TRACING_V2}",
        "DISABLE_FLOWISE_TELEMETRY": "${DISABLE_FLOWISE_TELEMETRY}",
        "DATABASE_PATH": "/root/.flowise",
        "APIKEY_PATH": "/root/.flowise",
        "SECRETKEY_PATH": "/root/.flowise/logs",
        "LOG_PATH": "/root/.flowise"
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/flowise",
          "containerPath": "/root/.flowise"
        }
      ],
      "entrypoint": "/bin/sh -c \"sleep 3; flowise start\""
    }
  ]
} 
```
# Original YAML
```yaml
version: '3.7'
services:
  flowise:
    image: flowiseai/flowise:2.2.3
    restart: unless-stopped
    entrypoint: /bin/sh -c "sleep 3; flowise start"
    container_name: flowise
    environment:
    - PORT=8009
    - FLOWISE_USERNAME=${FLOWISE_USERNAME}
    - FLOWISE_PASSWORD=${FLOWISE_PASSWORD}
    - FLOWISE_SECRETKEY_OVERWRITE=${FLOWISE_SECRETKEY_OVERWRITE}
    - LANGCHAIN_ENDPOINT=${LANGCHAIN_ENDPOINT}
    - LANGCHAIN_API_KEY=${LANGCHAIN_API_KEY}
    - LANGCHAIN_PROJECT=${LANGCHAIN_PROJECT}
    - LANGCHAIN_TRACING_V2=${LANGCHAIN_TRACING_V2}
    - DISABLE_FLOWISE_TELEMETRY=${DISABLE_FLOWISE_TELEMETRY}
    - DATABASE_PATH=/root/.flowise
    - APIKEY_PATH=/root/.flowise
    - SECRETKEY_PATH=/root/.flowise/logs
    - LOG_PATH=/root/.flowise
    ports:
    - ${APP_PORT}:8009
    volumes:
    - ${APP_DATA_DIR}/data/flowise:/root/.flowise
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.flowise-web-redirect.redirectscheme.scheme: https
      traefik.http.services.flowise.loadbalancer.server.port: 8009
      traefik.http.routers.flowise-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.flowise-insecure.entrypoints: web
      traefik.http.routers.flowise-insecure.service: flowise
      traefik.http.routers.flowise-insecure.middlewares: flowise-web-redirect
      traefik.http.routers.flowise.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.flowise.entrypoints: websecure
      traefik.http.routers.flowise.service: flowise
      traefik.http.routers.flowise.tls.certresolver: myresolver
      traefik.http.routers.flowise-local-insecure.rule: Host(`flowise.${LOCAL_DOMAIN}`)
      traefik.http.routers.flowise-local-insecure.entrypoints: web
      traefik.http.routers.flowise-local-insecure.service: flowise
      traefik.http.routers.flowise-local-insecure.middlewares: flowise-web-redirect
      traefik.http.routers.flowise-local.rule: Host(`flowise.${LOCAL_DOMAIN}`)
      traefik.http.routers.flowise-local.entrypoints: websecure
      traefik.http.routers.flowise-local.service: flowise
      traefik.http.routers.flowise-local.tls: true
      runtipi.managed: true
 
```
