## Dynamic compose for cross-seed
This is a cross-seed update for using dynamic compose.
##### Reaching the app :
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/config:/config
- [ ] ${ROOT_FOLDER_HOST}/app-data/transmission-vpn/data/config/transmission-home/torrents:/torrents:ro
- [ ] ${ROOT_FOLDER_HOST}/media/torrents/watch:/cross-seeds
##### Specific instructions :
- [ ] 🌳 Environment
- [ ] 👤 User (1000:1000)
- [ ] ⌨ Command
