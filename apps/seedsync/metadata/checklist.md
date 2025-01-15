## Dynamic compose for seedsync
This is a seedsync update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://seedsync.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/config:/config
- [ ] ${ROOT_FOLDER_HOST}/media/torrents/complete:/downloads
##### Specific instructions :
- [ ] 🌳 Environment
- [ ] 👤 User (${SEEDSYNC_PUID:-1000}:${SEEDSYNC_PGID:-1000})
