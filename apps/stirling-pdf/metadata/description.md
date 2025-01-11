# Checklist
## Dynamic compose for stirling-pdf
This is a stirling-pdf update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://stirling-pdf.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/trainingData:/usr/share/tesseract-ocr/5/tessdata
- [ ] ${APP_DATA_DIR}/data/extraConfigs:/configs
- [ ] ${APP_DATA_DIR}/data/customFiles:/customFiles/
- [ ] ${APP_DATA_DIR}/data/logs:/logs/
##### Specific instructions :
- [ ] 🌳 Environment
- [ ] 👑 Privileged

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "stirling-pdf",
      "image": "stirlingtools/stirling-pdf:0.36.6",
      "isMain": true,
      "internalPort": 8080,
      "environment": {
        "DOCKER_ENABLE_SECURITY": "${STIRLING_PDF_DOCKER_ENABLE_SECURITY-false}"
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/trainingData",
          "containerPath": "/usr/share/tesseract-ocr/5/tessdata"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/extraConfigs",
          "containerPath": "/configs"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/customFiles",
          "containerPath": "/customFiles/"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/logs",
          "containerPath": "/logs/"
        }
      ],
      "privileged": true
    }
  ]
} 
```
# Original YAML
```yaml
version: '3.9'
services:
  stirling-pdf:
    image: stirlingtools/stirling-pdf:0.36.6
    restart: unless-stopped
    container_name: stirling-pdf
    privileged: true
    ports:
    - ${APP_PORT}:8080
    volumes:
    - ${APP_DATA_DIR}/data/trainingData:/usr/share/tesseract-ocr/5/tessdata
    - ${APP_DATA_DIR}/data/extraConfigs:/configs
    - ${APP_DATA_DIR}/data/customFiles:/customFiles/
    - ${APP_DATA_DIR}/data/logs:/logs/
    environment:
    - DOCKER_ENABLE_SECURITY=${STIRLING_PDF_DOCKER_ENABLE_SECURITY-false}
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.stirling-pdf-web-redirect.redirectscheme.scheme: https
      traefik.http.services.stirling-pdf.loadbalancer.server.port: 8080
      traefik.http.routers.stirling-pdf-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.stirling-pdf-insecure.entrypoints: web
      traefik.http.routers.stirling-pdf-insecure.service: stirling-pdf
      traefik.http.routers.stirling-pdf-insecure.middlewares: stirling-pdf-web-redirect
      traefik.http.routers.stirling-pdf.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.stirling-pdf.entrypoints: websecure
      traefik.http.routers.stirling-pdf.service: stirling-pdf
      traefik.http.routers.stirling-pdf.tls.certresolver: myresolver
      traefik.http.routers.stirling-pdf-local-insecure.rule: Host(`stirling-pdf.${LOCAL_DOMAIN}`)
      traefik.http.routers.stirling-pdf-local-insecure.entrypoints: web
      traefik.http.routers.stirling-pdf-local-insecure.service: stirling-pdf
      traefik.http.routers.stirling-pdf-local-insecure.middlewares: stirling-pdf-web-redirect
      traefik.http.routers.stirling-pdf-local.rule: Host(`stirling-pdf.${LOCAL_DOMAIN}`)
      traefik.http.routers.stirling-pdf-local.entrypoints: websecure
      traefik.http.routers.stirling-pdf-local.service: stirling-pdf
      traefik.http.routers.stirling-pdf-local.tls: true
      runtipi.managed: true
 
```
