# Checklist
## Dynamic compose for recyclarr
This is a recyclarr update for using dynamic compose.
##### Reaching the app :
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/config:/config
##### Specific instructions :
- [ ] 🌳 Environment
- [ ] 👤 User (${TIPI_UID}:${TIPI_GID})

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "recyclarr",
      "image": "ghcr.io/recyclarr/recyclarr:7.4.0",
      "isMain": true,
      "user": "${TIPI_UID}:${TIPI_GID}",
      "environment": {
        "RECYCLARR_CREATE_CONFIG": "${RECYCLARR_CREATE_CONFIG-true}",
        "TZ": "${TZ}"
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/config",
          "containerPath": "/config"
        }
      ]
    }
  ]
} 
```
# Original YAML
```yaml
services:
  recyclarr:
    user: ${TIPI_UID}:${TIPI_GID}
    container_name: recyclarr
    image: ghcr.io/recyclarr/recyclarr:7.4.0
    restart: unless-stopped
    volumes:
    - ${APP_DATA_DIR}/data/config:/config
    environment:
    - RECYCLARR_CREATE_CONFIG=${RECYCLARR_CREATE_CONFIG-true}
    - TZ=${TZ}
    networks:
    - tipi_main_network
    labels:
      traefik.enable: false
      runtipi.managed: true
 
```
