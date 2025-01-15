# Checklist
## Dynamic compose for eclipse-mosquitto
This is a eclipse-mosquitto update for using dynamic compose.
##### Reaching the app :
- [ ] 🌐 Additionnal Port(s)
- [ ] http://localip:port
- [ ] https://eclipse-mosquitto.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/data:/mosquitto/data
- [ ] ${APP_DATA_DIR}/data/config:/mosquitto/config
- [ ] ${APP_DATA_DIR}/data/scripts/dynsec-setup.sh:/dynsec-setup.sh
##### Specific instructions :
- [ ] 🌳 Environment
- [ ] ⌨ Command
- [ ] 🙉 Expose
- [ ] 🔗 Depends on

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "eclipse-mosquitto",
      "image": "eclipse-mosquitto:2.0.20",
      "isMain": true,
      "addPorts": [
        {
          "hostPort": 1883,
          "containerPort": 1883
        }
      ],
      "expose": [
        1883
      ],
      "environment": {
        "TZ": "${TZ}",
        "MQTT_DYNSEC_ADMIN_USER": "admin",
        "MQTT_DYNSEC_ADMIN_PASSWORD": "${MQTT_ADMIN_PASSWORD}"
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/data",
          "containerPath": "/mosquitto/data"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/config",
          "containerPath": "/mosquitto/config"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/scripts/dynsec-setup.sh",
          "containerPath": "/dynsec-setup.sh"
        }
      ],
      "command": [
        "/dynsec-setup.sh",
        "/usr/sbin/mosquitto",
        "-c",
        "/mosquitto/config/mosquitto.conf"
      ]
    },
    {
      "name": "mosquitto-management-center",
      "image": "cedalo/management-center:2",
      "internalPort": 8088,
      "expose": [
        8088
      ],
      "environment": {
        "TZ": "${TZ}",
        "CEDALO_MC_BROKER_ID": "mosquitto-broker",
        "CEDALO_MC_BROKER_NAME": "mosquitto-broker-2",
        "CEDALO_MC_BROKER_URL": "mqtt://${INTERNAL_IP}:1883",
        "CEDALO_MC_BROKER_USERNAME": "admin",
        "CEDALO_MC_BROKER_PASSWORD": "${MQTT_ADMIN_PASSWORD}",
        "CEDALO_MC_USERNAME": "admin",
        "CEDALO_MC_PASSWORD": "${CEDALO_MC_PASSWORD}"
      },
      "dependsOn": [
        "eclipse-mosquitto"
      ]
    }
  ]
} 
```
# Original YAML
```yaml
version: '3.7'
services:
  eclipse-mosquitto:
    image: eclipse-mosquitto:2.0.20
    container_name: eclipse-mosquitto
    environment:
    - TZ=${TZ}
    - MQTT_DYNSEC_ADMIN_USER=admin
    - MQTT_DYNSEC_ADMIN_PASSWORD=${MQTT_ADMIN_PASSWORD}
    ports:
    - 1883:1883
    command:
    - /dynsec-setup.sh
    - /usr/sbin/mosquitto
    - -c
    - /mosquitto/config/mosquitto.conf
    expose:
    - 1883
    volumes:
    - ${APP_DATA_DIR}/data/data:/mosquitto/data
    - ${APP_DATA_DIR}/data/config:/mosquitto/config
    - ${APP_DATA_DIR}/data/scripts/dynsec-setup.sh:/dynsec-setup.sh
    restart: unless-stopped
    networks:
    - tipi_main_network
    labels:
      runtipi.managed: true
  mosquitto-management-center:
    image: cedalo/management-center:2
    container_name: mosquitto-management-center
    environment:
    - TZ=${TZ}
    - CEDALO_MC_BROKER_ID=mosquitto-broker
    - CEDALO_MC_BROKER_NAME=mosquitto-broker-2
    - CEDALO_MC_BROKER_URL=mqtt://${INTERNAL_IP}:1883
    - CEDALO_MC_BROKER_USERNAME=admin
    - CEDALO_MC_BROKER_PASSWORD=${MQTT_ADMIN_PASSWORD}
    - CEDALO_MC_USERNAME=admin
    - CEDALO_MC_PASSWORD=${CEDALO_MC_PASSWORD}
    ports:
    - ${APP_PORT}:8088
    expose:
    - 8088
    depends_on:
    - eclipse-mosquitto
    networks:
    - tipi_main_network
    restart: unless-stopped
    labels:
      traefik.enable: true
      traefik.http.middlewares.mosquitto-web-redirect.redirectscheme.scheme: https
      traefik.http.services.mosquitto.loadbalancer.server.port: 8088
      traefik.http.routers.mosquitto-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.mosquitto-insecure.entrypoints: web
      traefik.http.routers.mosquitto-insecure.service: mosquitto-web
      traefik.http.routers.mosquitto-insecure.middlewares: mosquitto-web-redirect
      traefik.http.routers.mosquitto.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.mosquitto.entrypoints: websecure
      traefik.http.routers.mosquitto.service: mosquitto-web
      traefik.http.routers.mosquitto.tls.certresolver: myresolver
      traefik.http.routers.mosquitto-local-insecure.rule: Host(`mosquitto.${LOCAL_DOMAIN}`)
      traefik.http.routers.mosquitto-local-insecure.entrypoints: web
      traefik.http.routers.mosquitto-local-insecure.service: mosquitto-web
      traefik.http.routers.mosquitto-local-insecure.middlewares: mosquitto-web-redirect
      traefik.http.routers.mosquitto-local.rule: Host(`mosquitto.${LOCAL_DOMAIN}`)
      traefik.http.routers.mosquitto-local.entrypoints: websecure
      traefik.http.routers.mosquitto-local.service: mosquitto-web
      traefik.http.routers.mosquitto-local.tls: true
      runtipi.managed: true
 
```
