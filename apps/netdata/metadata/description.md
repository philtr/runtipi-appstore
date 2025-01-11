# Checklist
## Dynamic compose for netdata
This is a netdata update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://netdata.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/config:/etc/netdata
- [ ] ${APP_DATA_DIR}/data/lib:/var/lib/netdata
- [ ] ${APP_DATA_DIR}/data/cache:/var/cache/netdata
- [ ] /etc/passwd:/host/etc/passwd:ro
- [ ] /etc/group:/host/etc/group:ro
- [ ] /proc:/host/proc:ro
- [ ] /sys:/host/sys:ro
- [ ] /etc/os-release:/host/etc/os-release:ro
- [ ] /var/run/docker.sock:/var/run/docker.sock:ro
##### Specific instructions :
- [ ] 🔢 PID (host)
- [ ] 🔓 Capacity add
- [ ] Security options 🔐

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "netdata",
      "image": "netdata/netdata:v2.1.1",
      "isMain": true,
      "internalPort": 19999,
      "pid": "host",
      "volumes": [
        {
          "hostPath": "${APP_DATA_DIR}/data/config",
          "containerPath": "/etc/netdata"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/lib",
          "containerPath": "/var/lib/netdata"
        },
        {
          "hostPath": "${APP_DATA_DIR}/data/cache",
          "containerPath": "/var/cache/netdata"
        },
        {
          "hostPath": "/etc/passwd",
          "containerPath": "/host/etc/passwd",
          "readOnly": true
        },
        {
          "hostPath": "/etc/group",
          "containerPath": "/host/etc/group",
          "readOnly": true
        },
        {
          "hostPath": "/proc",
          "containerPath": "/host/proc",
          "readOnly": true
        },
        {
          "hostPath": "/sys",
          "containerPath": "/host/sys",
          "readOnly": true
        },
        {
          "hostPath": "/etc/os-release",
          "containerPath": "/host/etc/os-release",
          "readOnly": true
        },
        {
          "hostPath": "/var/run/docker.sock",
          "containerPath": "/var/run/docker.sock",
          "readOnly": true
        }
      ],
      "capAdd": [
        "SYS_PTRACE",
        "SYS_ADMIN"
      ],
      "securityOpt": [
        "apparmor:unconfined"
      ]
    }
  ]
} 
```
# Original YAML
```yaml
version: '3.7'
services:
  netdata:
    image: netdata/netdata:v2.1.1
    container_name: netdata
    pid: host
    restart: unless-stopped
    cap_add:
    - SYS_PTRACE
    - SYS_ADMIN
    security_opt:
    - apparmor:unconfined
    ports:
    - ${APP_PORT}:19999
    volumes:
    - ${APP_DATA_DIR}/data/config:/etc/netdata
    - ${APP_DATA_DIR}/data/lib:/var/lib/netdata
    - ${APP_DATA_DIR}/data/cache:/var/cache/netdata
    - /etc/passwd:/host/etc/passwd:ro
    - /etc/group:/host/etc/group:ro
    - /proc:/host/proc:ro
    - /sys:/host/sys:ro
    - /etc/os-release:/host/etc/os-release:ro
    - /var/run/docker.sock:/var/run/docker.sock:ro
    networks:
    - tipi_main_network
    labels:
      traefik.enable: true
      traefik.http.middlewares.netdata-web-redirect.redirectscheme.scheme: https
      traefik.http.services.netdata.loadbalancer.server.port: 19999
      traefik.http.routers.netdata-insecure.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.netdata-insecure.entrypoints: web
      traefik.http.routers.netdata-insecure.service: netdata
      traefik.http.routers.netdata-insecure.middlewares: netdata-web-redirect
      traefik.http.routers.netdata.rule: Host(`${APP_DOMAIN}`)
      traefik.http.routers.netdata.entrypoints: websecure
      traefik.http.routers.netdata.service: netdata
      traefik.http.routers.netdata.tls.certresolver: myresolver
      traefik.http.routers.netdata-local-insecure.rule: Host(`netdata.${LOCAL_DOMAIN}`)
      traefik.http.routers.netdata-local-insecure.entrypoints: web
      traefik.http.routers.netdata-local-insecure.service: netdata
      traefik.http.routers.netdata-local-insecure.middlewares: netdata-web-redirect
      traefik.http.routers.netdata-local.rule: Host(`netdata.${LOCAL_DOMAIN}`)
      traefik.http.routers.netdata-local.entrypoints: websecure
      traefik.http.routers.netdata-local.service: netdata
      traefik.http.routers.netdata-local.tls: true
      runtipi.managed: true
 
```
