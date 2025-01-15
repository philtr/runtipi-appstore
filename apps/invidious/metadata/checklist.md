## Dynamic compose for invidious
This is a invidious update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://invidious.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/postgres:/var/lib/postgresql/data
- [ ] ${APP_DATA_DIR}/data/init/sql:/config/sql
- [ ] ${APP_DATA_DIR}/data/init/init-invidious-db.sh:/docker-entrypoint-initdb.d/init-invidious-db.sh
##### Specific instructions :
- [ ] 🌳 Environment
- [ ] 🩺 Healthcheck
- [ ] 🔗 Depends on
- [ ] ⌨ Command
- [ ] ▶ Read only
- [ ] 🔒 Capacity drop
- [ ] 🔐 Security options
