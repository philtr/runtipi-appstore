# Checklist
## Dynamic compose for postfix-relay
This is a postfix-relay update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://postfix-relay.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/config:/root/config:rw
##### Specific instructions :
- [ ] 🌳 Environment
- [ ] 🩺 Healthcheck

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "postfix-relay",
      "image": "simenduev/postfix-relay:1.4.0",
      "isMain": true,
      "internalPort": 25,
      "environment": {
        "ACCEPTED_NETWORKS": "${RELAY_ACCEPTED_NETWORKS}",
        "SMTP_HOST": "${RELAY_SMTP_HOST}",
        "SMTP_LOGIN": "${RELAY_SMTP_LOGIN}",
        "SMTP_PASSWORD": "${RELAY_SMTP_PASSWORD}",
        "SMTP_PORT": "${RELAY_SMTP_PORT}",
        "TLS_VERIFY": "${RELAY_TLS_VERIFY}",
        "USE_TLS": "${RELAY_USE_TLS}"
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/config",
          "containerPath": "/root/config"
        }
      ],
      "healthCheck": {
        "interval": "10s",
        "timeout": "5s",
        "retries": 5,
        "startPeriod": "30s",
        "test": "netstat -an | grep 25 > /dev/null; if [ 0 != $? ]; then exit 1; fi;"
      }
    }
  ]
} 
```
# Original YAML
```yaml
version: '3.8'
services:
  postfix-relay:
    container_name: postfix-relay
    image: simenduev/postfix-relay:1.4.0
    restart: unless-stopped
    ports:
    - ${APP_PORT}:25
    volumes:
    - ${APP_DATA_DIR}/data/config:/root/config:rw
    environment:
    - ACCEPTED_NETWORKS=${RELAY_ACCEPTED_NETWORKS}
    - SMTP_HOST=${RELAY_SMTP_HOST}
    - SMTP_LOGIN=${RELAY_SMTP_LOGIN}
    - SMTP_PASSWORD=${RELAY_SMTP_PASSWORD}
    - SMTP_PORT=${RELAY_SMTP_PORT}
    - TLS_VERIFY=${RELAY_TLS_VERIFY}
    - USE_TLS=${RELAY_USE_TLS}
    healthcheck:
      test: netstat -an | grep 25 > /dev/null; if [ 0 != $? ]; then exit 1; fi;
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 30s
    networks:
    - tipi_main_network
    labels:
      traefik.enable: false
      runtipi.managed: true
 
```
