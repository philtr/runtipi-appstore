# Checklist
## Dynamic compose for cross-seed
This is a cross-seed update for using dynamic compose.
##### Reaching the app :
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/config:/config
- [ ] ${ROOT_FOLDER_HOST}/app-data/transmission-vpn/data/config/transmission-home/torrents:/torrents:ro
- [ ] ${ROOT_FOLDER_HOST}/media/torrents/watch:/cross-seeds
##### Specific instructions :
- [ ] 🌳 Environment
- [ ] 👤 User (1000:1000)
- [ ] ⌨ Command

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "cross-seed",
      "image": "ghcr.io/cross-seed/cross-seed:6.8.7",
      "isMain": true,
      "user": "1000:1000",
      "environment": {
        "TZ": "${TZ}"
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/config",
          "containerPath": "/config"
        },
        {
          "hostPath": "${ROOT_FOLDER_HOST}/app-data/transmission-vpn/data/config/transmission-home/torrents",
          "containerPath": "/torrents",
          "readOnly": true
        },
        {
          "hostPath": "${ROOT_FOLDER_HOST}/media/torrents/watch",
          "containerPath": "/cross-seeds"
        }
      ],
      "command": "daemon"
    }
  ]
} 
```
# Original YAML
```yaml
services:
  cross-seed:
    container_name: cross-seed
    image: ghcr.io/cross-seed/cross-seed:6.8.7
    user: 1000:1000
    restart: unless-stopped
    command: daemon
    environment:
    - TZ=${TZ}
    volumes:
    - ${APP_DATA_DIR}/data/config:/config
    - ${ROOT_FOLDER_HOST}/app-data/transmission-vpn/data/config/transmission-home/torrents:/torrents:ro
    - ${ROOT_FOLDER_HOST}/media/torrents/watch:/cross-seeds
    networks:
    - tipi_main_network
    labels:
      traefik.enable: false
      runtipi.managed: true
 
```
