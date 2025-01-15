# Checklist
## Dynamic compose for matter-server
This is a matter-server update for using dynamic compose.
##### Reaching the app :
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] /run/dbus:/run/dbus:ro
- [ ] ${APP_DATA_DIR}/data:/data
##### Specific instructions :
- [ ] 🌐 Network mode (host)
- [ ] 🔐 Security options

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "matter-server",
      "image": "ghcr.io/home-assistant-libs/python-matter-server:7.0.1",
      "isMain": true,
      "networkMode": "host",
      "volumes": [
        {
          "hostPath": "/run/dbus",
          "containerPath": "/run/dbus",
          "readOnly": true
        },
        {
          "hostPath": "${APP_DATA_DIR}/data",
          "containerPath": "/data"
        }
      ],
      "securityOpt": [
        "apparmor=unconfined"
      ]
    }
  ]
} 
```
# Original YAML
```yaml
services:
  matter-server:
    image: ghcr.io/home-assistant-libs/python-matter-server:7.0.1
    container_name: matter-server
    restart: unless-stopped
    network_mode: host
    security_opt:
    - apparmor=unconfined
    volumes:
    - /run/dbus:/run/dbus:ro
    - ${APP_DATA_DIR}/data:/data
    labels:
      traefik.enable: false
      runtipi.managed: true
 
```
