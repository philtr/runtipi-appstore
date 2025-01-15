# Checklist
## Dynamic compose for pihole
This is a pihole update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://pihole.tipi.local
- [ ] 🌐 Additionnal Port(s)
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/pihole:/etc/pihole
- [ ] ${APP_DATA_DIR}/data/dnsmasq:/etc/dnsmasq.d
##### Specific instructions :
- [ ] 🌳 Environment
- [ ] 🖥 Hostname
- [ ] 🔓 Capacity add
- 🏷 DNS (skipped)

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "pihole",
      "image": "pihole/pihole:2024.07.0",
      "isMain": true,
      "internalPort": 80,
      "addPorts": [
        {
          "hostPort": 53,
          "containerPort": 53,
          "interface": "${NETWORK_INTERFACE:-0.0.0.0}",
          "tcp": true,
          "udp": true
        }
      ],
      "hostname": "pihole",
      "environment": {
        "TZ": "${TZ}",
        "WEBPASSWORD": "${APP_PASSWORD}"
      },
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/pihole",
          "containerPath": "/etc/pihole"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/dnsmasq",
          "containerPath": "/etc/dnsmasq.d"
        }
      ],
      "capAdd": [
        "NET_ADMIN"
      ]
    }
  ]
} 
```
# Original YAML
```yaml
version: '3.7'
services:
  pihole:
    container_name: pihole
    image: pihole/pihole:2024.07.0
    restart: unless-stopped
    hostname: pihole
    dns:
    - 127.0.0.1
    ports:
    - ${NETWORK_INTERFACE:-0.0.0.0}:53:53/tcp
    - ${NETWORK_INTERFACE:-0.0.0.0}:53:53/udp
    - ${APP_PORT}:80
    volumes:
    - ${APP_DATA_DIR}/data/pihole:/etc/pihole
    - ${APP_DATA_DIR}/data/dnsmasq:/etc/dnsmasq.d
    environment:
      TZ: ${TZ}
      WEBPASSWORD: ${APP_PASSWORD}
    cap_add:
    - NET_ADMIN
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.pihole-web-redirect.redirectscheme.scheme: https
      traefik.http.services.pihole.loadbalancer.server.port: 80
      traefik.http.routers.pihole-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.pihole-insecure.entrypoints: web
      traefik.http.routers.pihole-insecure.service: pihole
      traefik.http.routers.pihole-insecure.middlewares: pihole-web-redirect
      traefik.http.routers.pihole.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.pihole.entrypoints: websecure
      traefik.http.routers.pihole.service: pihole
      traefik.http.routers.pihole.tls.certresolver: myresolver
      traefik.http.routers.pihole-local-insecure.rule: Host(`pihole.${LOCAL_DOMAIN}`)
      traefik.http.routers.pihole-local-insecure.entrypoints: web
      traefik.http.routers.pihole-local-insecure.service: pihole
      traefik.http.routers.pihole-local-insecure.middlewares: pihole-web-redirect
      traefik.http.routers.pihole-local.rule: Host(`pihole.${LOCAL_DOMAIN}`)
      traefik.http.routers.pihole-local.entrypoints: websecure
      traefik.http.routers.pihole-local.service: pihole
      traefik.http.routers.pihole-local.tls: true
      runtipi.managed: true
 
```
