# Checklist
## Dynamic compose for scrypted
This is a scrypted update for using dynamic compose.
##### Reaching the app :
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/scrypted/database:/server/volume
- [ ] ${ROOT_FOLDER_HOST}/media/data/NVR/scrypted:/nvr
##### Specific instructions :
- [ ] 🌳 Environment
- [ ] 🌐 Network mode (host)
- [ ] 👑 Privileged
- [ ] 📱 Devices
- [ ] 📃 Logging

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "scrypted",
      "image": "koush/scrypted:18-jammy-full.s6-v0.88.0",
      "isMain": true,
      "networkMode": "host",
      "environment": {
        "SCRYPTED_WEBHOOK_UPDATE_AUTHORIZATION": "${SCRYPTED_BEARER_TOKEN}",
        "SCRYPTED_DOCKER_AVAHI": "false"
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/scrypted/database",
          "containerPath": "/server/volume"
        },
        {
          "hostPath": "${ROOT_FOLDER_HOST}/media/data/NVR/scrypted",
          "containerPath": "/nvr"
        }
      ],
      "privileged": true,
      "devices": [
        "/dev/bus/usb:/dev/bus/usb",
        "/dev/dri:/dev/dri"
      ],
      "logging": {
        "driver": "json-file",
        "options": {
          "max-size": "10m",
          "max-file": "10"
        }
      }
    }
  ]
} 
```
# Original YAML
```yaml
version: '3'
services:
  scrypted:
    container_name: scrypted
    image: koush/scrypted:18-jammy-full.s6-v0.88.0
    privileged: true
    volumes:
    - ${APP_DATA_DIR}/data/scrypted/database:/server/volume
    - ${ROOT_FOLDER_HOST}/media/data/NVR/scrypted:/nvr
    environment:
    - SCRYPTED_WEBHOOK_UPDATE_AUTHORIZATION=${SCRYPTED_BEARER_TOKEN}
    - SCRYPTED_DOCKER_AVAHI=false
    devices:
    - /dev/bus/usb:/dev/bus/usb
    - /dev/dri:/dev/dri
    restart: unless-stopped
    network_mode: host
    logging:
      driver: json-file
      options:
        max-size: 10m
        max-file: '10'
    labels:
      runtipi.managed: true
 
```
