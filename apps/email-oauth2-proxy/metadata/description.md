# Checklist
## Dynamic compose for email-oauth2-proxy
This is a email-oauth2-proxy update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://email-oauth2-proxy.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/config:/config:rw
##### Specific instructions :
- [ ] 🌳 Environment

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "email-oauth2-proxy",
      "image": "ghcr.io/blacktirion/email-oauth2-proxy-docker:2024.11.19",
      "isMain": true,
      "internalPort": 80,
      "environment": {
        "DEBUG": "true",
        "CACHE_STORE": "/config/credstore.config",
        "LOGFILE": "true",
        "LOCAL_SERVER_AUTH": "true"
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
version: '3.9'
services:
  email-oauth2-proxy:
    container_name: email-oauth2-proxy
    image: ghcr.io/blacktirion/email-oauth2-proxy-docker:2024.11.19
    ports:
    - ${APP_PORT}:80
    restart: unless-stopped
    volumes:
    - ${APP_DATA_DIR}/data/config:/config:rw
    environment:
    - DEBUG=true
    - CACHE_STORE=/config/credstore.config
    - LOGFILE=true
    - LOCAL_SERVER_AUTH=true
    networks:
    - tipi_main_network
    labels:
      traefik.enable: false
      runtipi.managed: true
 
```
