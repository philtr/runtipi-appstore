# Checklist
## Dynamic compose for homebridge
This is a homebridge update for using dynamic compose.
##### Reaching the app :
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/homebridge:/homebridge
##### Specific instructions :
- [ ] 🌳 Environment
- [ ] 🌐 Network mode (host)
- [ ] 👑 Privileged
- [ ] 📃 Logging

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "homebridge",
      "image": "homebridge/homebridge:2024-05-02",
      "isMain": true,
      "networkMode": "host",
      "environment": {
        "TZ": "${TZ}"
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/homebridge",
          "containerPath": "/homebridge"
        }
      ],
      "privileged": true,
      "logging": {
        "driver": "json-file",
        "options": {
          "max-size": "10mb",
          "max-file": "1"
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
  homebridge:
    container_name: homebridge
    image: homebridge/homebridge:2024-05-02
    restart: unless-stopped
    privileged: true
    network_mode: host
    environment:
    - TZ=${TZ}
    volumes:
    - ${APP_DATA_DIR}/data/homebridge:/homebridge
    logging:
      driver: json-file
      options:
        max-size: 10mb
        max-file: '1'
    labels:
      runtipi.managed: true
 
```
