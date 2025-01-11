## Dynamic compose for authentik
This is a authentik update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://authentik.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/authentik-media:/media
- [ ] ${APP_DATA_DIR}/data/authentik-custom-templates:/templates
- [ ] /var/run/docker.sock:/var/run/docker.sock
- [ ] ${APP_DATA_DIR}/data/authentik-media:/media
- [ ] ${APP_DATA_DIR}/data/authentik-certs:/certs
- [ ] ${APP_DATA_DIR}/data/authentik-custom-templates:/templates
- [ ] ${APP_DATA_DIR}/data/postgres:/var/lib/postgresql/data
- [ ] ${APP_DATA_DIR}/data/redis:/data
##### Specific instructions :
- [ ] 🌳 Environment
- [ ] ⌨ Command
- [ ] 🔗 Depends on
- [ ] 👤 User (root)
- [ ] 🩺 Healthcheck
