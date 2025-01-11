# Checklist
## Dynamic compose for homeassistant
This is a homeassistant update for using dynamic compose.
##### Reaching the app :
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/config:/config
##### Specific instructions :
- [ ] 🌐 Network mode (host)
- [ ] 👑 Privileged

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "homeassistant",
      "image": "ghcr.io/home-assistant/home-assistant:stable",
      "isMain": true,
      "networkMode": "host",
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/config",
          "containerPath": "/config"
        }
      ],
      "privileged": true
    }
  ]
} 
```
# Original YAML
```yaml
version: '3'
services:
  homeassistant:
    container_name: homeassistant
    image: ghcr.io/home-assistant/home-assistant:stable
    volumes:
    - ${APP_DATA_DIR}/config:/config
    restart: unless-stopped
    privileged: true
    network_mode: host
    labels:
      runtipi.managed: true
 
```
