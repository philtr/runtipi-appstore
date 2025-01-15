## Dynamic compose for crowdsec
This is a crowdsec update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://crowdsec.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] /etc/localtime:/etc/localtime:ro
- [ ] /var/run/docker.sock:/var/run/docker.sock:ro
- [ ] ${APP_DATA_DIR}/data/crowdsec/acquis.yaml:/etc/crowdsec/acquis.yaml
- [ ] ${APP_DATA_DIR}/data/crowdsec:/etc/crowdsec
- [ ] ${APP_DATA_DIR}/data/crowdsec/db:/var/lib/crowdsec/data
- [ ] /var/log/auth.log:/var/log/auth.log:ro
- [ ] /var/log/traefik:/var/log/traefik:ro
- [ ] /var/log/crowdsec:/var/log/crowdsec:ro
- [ ] ${APP_DATA_DIR}/data/crowdsec-dashboard/data:/data
- [ ] ${APP_DATA_DIR}/data/crowdsec/db:/metabase-data
##### Specific instructions :
- [ ] 🌳 Environment
- [ ] 🔗 Depends on
