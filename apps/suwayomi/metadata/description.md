# Checklist
## Dynamic compose for suwayomi
This is a suwayomi update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://suwayomi.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data:/home/suwayomi/.local/share/Tachidesk
##### Specific instructions :
- [ ] 🌳 Environment

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "suwayomi",
      "image": "ghcr.io/suwayomi/tachidesk:v1.1.1",
      "isMain": true,
      "internalPort": 4567,
      "environment": {
        "TZ": "${TZ}",
        "BIND_IP": "0.0.0.0"
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data",
          "containerPath": "/home/suwayomi/.local/share/Tachidesk"
        }
      ]
    }
  ]
} 
```
# Original YAML
```yaml
version: '3.9'
services:
  suwayomi:
    image: ghcr.io/suwayomi/tachidesk:v1.1.1
    container_name: suwayomi
    environment:
    - TZ=${TZ}
    - BIND_IP=0.0.0.0
    volumes:
    - ${APP_DATA_DIR}/data:/home/suwayomi/.local/share/Tachidesk
    ports:
    - ${APP_PORT}:4567
    restart: unless-stopped
    networks:
    - tipi_main_network
    labels:
      runtipi.managed: true
 
```
