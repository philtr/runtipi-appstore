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
