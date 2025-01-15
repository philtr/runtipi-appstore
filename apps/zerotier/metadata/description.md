# Checklist
## Dynamic compose for zerotier
This is a zerotier update for using dynamic compose.
##### Reaching the app :
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Specific instructions :
- [ ] 🩺 Healthcheck
- [ ] ⌨ Command
- [ ] 🌐 Network mode (host)
- [ ] 📱 Devices
- [ ] 🔓 Capacity add

# New JSON
```json
{
  "$schema": "../dynamic-compose-schema.json",
  "services": [
    {
      "name": "zerotier",
      "image": "zerotier/zerotier:1.14.2",
      "isMain": true,
      "networkMode": "host",
      "command": "${NETWORK_ID}",
      "devices": [
        "/dev/net/tun"
      ],
      "capAdd": [
        "NET_ADMIN",
        "SYS_ADMIN"
      ],
      "healthCheck": {
        "test": "true"
      }
    }
  ]
} 
```
# Original YAML
```yaml
version: '3.7'
services:
  zerotier:
    container_name: zerotier
    image: zerotier/zerotier:1.14.2
    restart: on-failure
    command: ${NETWORK_ID}
    cap_add:
    - NET_ADMIN
    - SYS_ADMIN
    devices:
    - /dev/net/tun
    healthcheck:
      test:
      - CMD
      - 'true'
    network_mode: host
    labels:
      runtipi.managed: true
 
```
