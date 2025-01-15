## Dynamic compose for plausible
This is a plausible update for using dynamic compose.
##### Reaching the app :
- [ ] http://localip:port
- [ ] https://plausible.tipi.local
##### In app tests :
- [ ] 📝 Register and log in
- [ ] 🖱 Basic interaction
- [ ] 🌆 Uploading data
- [ ] 🔄 Check data after restart
##### Volumes mapping :
- [ ] ${APP_DATA_DIR}/data/database/:/var/lib/postgresql/data
- [ ] ${APP_DATA_DIR}/data/plausible-event-data:/var/lib/clickhouse
- [ ] ${APP_DATA_DIR}/data/clickhouse/clickhouse-config.xml:/etc/clickhouse-server/config.d/logging.xml:ro
- [ ] ${APP_DATA_DIR}/data/clickhouse/clickhouse-user-config.xml:/etc/clickhouse-server/users.d/logging.xml:ro
##### Specific instructions :
- [ ] 🌳 Environment
- [ ] ⌨ Command
- [ ] 🔗 Depends on
- [ ] 🧱 Ulimits
